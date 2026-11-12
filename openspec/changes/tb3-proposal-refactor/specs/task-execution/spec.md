## Purpose

The `task-execution` capability defines the observable behaviour of the system from the moment a validated navigation goal is dispatched to Nav2 through to the terminal result. This includes goal dispatch via the Navigation Adapter, monitoring of navigation action feedback, handling of explicit user cancellation, deadline (timeout) enforcement, single-retry logic under qualified conditions, and the derivation of terminal mission states (ARRIVED, FAILED, CANCELED, TIMEOUT, FAULT) from Nav2 action results and event log evidence.

## ADDED Requirements

### Requirement: Validated Goal Dispatch

The system SHALL dispatch a navigation goal to Nav2 via the Navigation Adapter only after all pre-dispatch validation checks have passed.

#### Scenario: Dispatch after successful pre-checks

WHEN all navigation-validation checks pass for a validated landmark,  
THEN the system SHALL dispatch a navigation goal via `navigate_to_landmark`, SHALL record the mission ID, dispatch timestamp (T2), and landmark ID in the event log, and SHALL enter the navigation monitoring loop.

### Requirement: Navigation Action Monitoring

After dispatching a goal, the system SHALL monitor action feedback and status until a terminal state is reached or the mission deadline expires.

#### Scenario: Navigation feedback received

WHEN the navigation action server publishes feedback (e.g., estimated time remaining, current pose),  
THEN the system SHALL append the feedback event to the event log with the mission ID and timestamp, and SHALL continue monitoring.

#### Scenario: Navigation succeeds

WHEN the navigation action server returns a result of `SUCCEEDED`,  
THEN the system SHALL record the terminal result and final observed pose in the event log, SHALL transition the mission state to ARRIVED (pending evidence verification by the `evidence-reporting` capability), and SHALL exit the monitoring loop.

#### Scenario: Navigation fails

WHEN the navigation action server returns a result of `FAILED`,  
THEN the system SHALL record the failure result and reason in the event log, and SHALL evaluate retry eligibility according to the retry requirement below.

#### Scenario: Navigation is externally cancelled

WHEN the navigation action server returns a result of `CANCELED` without a user-initiated cancel command having been issued,  
THEN the system SHALL record the cancellation result and reason in the event log and SHALL report `CANCELED` to the evidence-reporting capability.

### Requirement: Explicit Cancellation Handling

The system SHALL invoke navigation cancellation immediately upon receiving an explicit cancellation command and await action server confirmation.

#### Scenario: Cancellation confirmed by action server

WHEN the `cancel_navigation` function returns a confirmed cancellation result from the action server,  
THEN the system SHALL record the cancellation confirmation in the event log and SHALL report the mission state as CANCELED to the `evidence-reporting` capability.

#### Scenario: Cancellation not confirmed within timeout

WHEN the action server does not confirm cancellation within a configured timeout after `cancel_navigation` is invoked,  
THEN the system SHALL report `FAULT` to the `evidence-reporting` capability, SHALL log the unconfirmed cancellation event, and SHALL NOT report the robot as stopped or at a safe state.

### Requirement: Mission Deadline Enforcement

The system SHALL enforce a mission deadline on all dispatched goals, canceling the active goal and reporting timeout if the deadline expires.

#### Scenario: Deadline reached without terminal result

WHEN the mission deadline expires before the navigation action server returns a terminal result,  
THEN the system SHALL invoke `cancel_navigation`, SHALL log a TIMEOUT event with the mission ID and elapsed time, and SHALL report `TIMEOUT` to the `evidence-reporting` capability regardless of the navigation action's subsequent state.

### Requirement: Single Retry Under Qualified Conditions

The system SHALL permit at most one navigation retry upon action failure, and only when fresh validation checks pass and time remains.

#### Scenario: Retry conditions met after FAILED result

WHEN the navigation action returns `FAILED`, pre-dispatch checks pass on a fresh evaluation, a valid path exists, and the mission deadline has not expired,  
THEN the system SHALL log a retry event and dispatch the goal one additional time via `navigate_to_landmark`.

#### Scenario: Retry conditions not met

WHEN the navigation action returns `FAILED` and any retry condition is not met (stale data, no valid path, or deadline expired),  
THEN the system SHALL report `FAILED` without retry, and SHALL log all checked conditions and their results.

#### Scenario: Second failure after retry

WHEN a navigation retry also results in `FAILED` or any other non-SUCCEEDED terminal result,  
THEN the system SHALL report the observed terminal result without further retry, and SHALL log the full sequence of attempts.

### Requirement: Safe Terminal State on Exception

The system SHALL transition to a safe FAULT state and cancel any active goal when communication or lifecycle exceptions occur.

#### Scenario: Action server becomes unavailable during monitoring

WHEN the navigation action server connection is lost or the server becomes unresponsive during active monitoring,  
THEN the system SHALL log the connection loss event, report `FAULT` to evidence reporting, and return the system to an idle state.

### Requirement: Mission State Transitions

The system SHALL restrict mission state transitions to permitted terminal states directly verified by Nav2 action feedback and event log evidence.

#### Scenario: Terminal transition mapping

WHEN an active navigation goal terminates,  
THEN the system SHALL transition only to ARRIVED upon SUCCEEDED with destination evidence, FAILED upon unrecoverable failure, CANCELED upon confirmed user cancellation, TIMEOUT upon deadline expiration, or FAULT upon unconfirmed cancellation or connection loss.

#### Scenario: Attempting to report ARRIVED without SUCCEEDED result

WHEN the navigation action result is not `SUCCEEDED`,  
THEN the system SHALL NOT report `ARRIVED` regardless of the robot's observed physical position.
