## Purpose

The `evidence-reporting` capability defines how the system constructs and delivers user-facing mission status reports based exclusively on evidence recorded in the event log and Nav2 action results. The system SHALL NOT report a mission outcome that is not supported by an observable action result or event log entry. This capability also defines the terminal report states, their triggering conditions, the evidence requirements for each, and the format of the event log that serves as the evidence base.

## ADDED Requirements

### Requirement: Evidence-Only Reporting

Every user-facing mission status report SHALL be derived from confirmed Nav2 action results or event log records, prohibiting unsupported outcome claims.

#### Scenario: Report generated from confirmed Nav2 result

WHEN the navigation action server returns a terminal result for a mission,  
THEN the system SHALL retrieve the corresponding event log entries, construct a report that cites the action result code and at least one log entry, and present the report to the user via the Output module (Module ⑧).

#### Scenario: No confirmed result available

WHEN no terminal result from the navigation action server is available within the mission deadline,  
THEN the system SHALL report `TIMEOUT` or `FAULT` as appropriate, and SHALL explicitly indicate in the user report that the mission outcome is unconfirmed.

### Requirement: Terminal Report State Mapping

The system SHALL map observed execution conditions to defined report states supported by verified evidence, and SHALL NOT report any state whose prerequisite condition and evidence are not satisfied.

#### Scenario: Pre-execution terminal state mapping

WHEN a task is ambiguous, invalid, or fails pre-dispatch checks,  
THEN the system SHALL report CLARIFY for ambiguous intent, REJECTED for unregistered tools or landmarks, NOT_READY for failed sensor or localization checks, and NO_PATH for unfeasible global paths.

#### Scenario: Execution terminal state mapping

WHEN a dispatched navigation mission terminates,  
THEN the system SHALL report ARRIVED for confirmed SUCCEEDED status with destination pose evidence, FAILED for terminal action failure, CANCELED for confirmed cancellation, TIMEOUT for expired deadlines, and FAULT for unconfirmed cancellation or communication exceptions.

#### Scenario: ARRIVED report constructed correctly

WHEN the navigation action result is `SUCCEEDED` and the event log contains the final observed pose at the destination,  
THEN the system SHALL report `ARRIVED` to the user, including the destination landmark ID and evidence of the SUCCEEDED result.

#### Scenario: ARRIVED report prohibited without SUCCEEDED result

WHEN the navigation action result is not `SUCCEEDED` or no action result has been received,  
THEN the system SHALL NOT report `ARRIVED` regardless of any other observable condition.

### Requirement: Event Log Structure

Every mission SHALL record structured lifecycle events in JSONL format, capturing mission ID, timestamp, event type, and diagnostic payload.

#### Scenario: Dispatch event logged

WHEN a navigation goal is dispatched,  
THEN the system SHALL append a `dispatch` event to `events.jsonl` with the mission ID, timestamp (T2), and landmark ID.

#### Scenario: Terminal result event logged

WHEN the navigation action server returns a terminal result,  
THEN the system SHALL append a `result` event to `events.jsonl` with the mission ID, result code (SUCCEEDED/FAILED/CANCELED), reason (if available), and timestamp (T3).

### Requirement: Uncertainty Disclosure

The system SHALL disclose operational uncertainty in user-facing reports whenever mission outcomes or robot states cannot be definitively verified.

#### Scenario: Timeout with partial feedback

WHEN the mission deadline expires and the event log contains feedback entries but no terminal result,  
THEN the system SHALL report `TIMEOUT` to the user and SHALL include the last feedback event's timestamp and the total elapsed mission time.

### Requirement: Pre-Dispatch State Reports

The system SHALL deliver explicit status reports explaining the failure cause whenever pre-dispatch validation prevents goal transmission.

#### Scenario: NOT_READY report delivered

WHEN any pre-dispatch check fails and results in `NOT_READY`,  
THEN the system SHALL report `NOT_READY` to the user, SHALL identify the failed check (e.g., "sensor data stale" or "localization unavailable"), and SHALL NOT imply that the navigation goal was attempted.

### Requirement: Report Delivery via Output Module

All user-facing mission reports SHALL be delivered through the Output and Interaction module referencing the original Chinese destination name.

#### Scenario: Report delivered for completed mission

WHEN a terminal mission state is determined,  
THEN the system SHALL deliver the corresponding report to the user within a time proportional to the complexity of evidence aggregation, and SHALL NOT withhold the report pending further navigation.
