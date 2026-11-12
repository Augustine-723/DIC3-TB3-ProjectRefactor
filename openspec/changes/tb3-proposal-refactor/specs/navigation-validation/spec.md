## Purpose

The `navigation-validation` capability defines the pre-dispatch robot-state and path-feasibility checks that the system MUST perform after a task intent is validated and before any navigation goal is sent to Nav2. These checks verify sensor data freshness, localization status, transform chain validity, and geometric path feasibility with respect to the pre-built map and the robot's configured footprint — ensuring that the system only dispatches goals under conditions where navigation is likely executable.

## ADDED Requirements

### Requirement: Sensor Data Freshness Check

Before dispatching any navigation goal, the system SHALL verify that the most recent LiDAR scan message is within a configured freshness threshold, prohibiting dispatch on stale data.

#### Scenario: LiDAR data is current

WHEN the most recent `/scan` message timestamp is within the configured freshness threshold,  
THEN the sensor freshness check SHALL pass and the system SHALL proceed to the localization check.

#### Scenario: LiDAR data is stale

WHEN the most recent `/scan` message timestamp is older than the configured freshness threshold,  
THEN the system SHALL report `NOT_READY` to the user with an indication that sensor data is stale, SHALL NOT dispatch a navigation goal, and SHALL log the stale data event with the observed timestamp.

### Requirement: Localization Status Check

Before dispatching any navigation goal, the system SHALL verify that robot localization is active and that the `map → odom → base_link` transform chain is valid and recent.

#### Scenario: Localization is valid and transform chain is fresh

WHEN the localization system is active, the transform chain `map → odom → base_link` is available and its timestamp is within the configured threshold,  
THEN the localization check SHALL pass and the system SHALL proceed to the path feasibility check.

#### Scenario: Transform chain is invalid or stale

WHEN any component of the `map → odom → base_link` transform chain is unavailable, has an invalid timestamp, or indicates localization divergence,  
THEN the system SHALL report `NOT_READY` to the user, SHALL NOT dispatch a navigation goal, and SHALL log the transform chain status and the observed timestamps.

### Requirement: Navigation Lifecycle Check

Before dispatching any navigation goal, the system SHALL verify that the Nav2 navigation action server is active and available.

#### Scenario: Navigation server is available

WHEN the navigation action server responds to a lifecycle status query within the configured timeout,  
THEN the lifecycle check SHALL pass and the system SHALL proceed to path feasibility checking.

#### Scenario: Navigation server is unavailable

WHEN the navigation action server does not respond within the configured timeout,  
THEN the system SHALL report `NOT_READY` to the user with an indication that the navigation service is unavailable, SHALL NOT dispatch a goal, and SHALL log the failure.

### Requirement: Path Feasibility Check

After passing sensor and localization checks, the system SHALL evaluate path feasibility using the registered landmark pose within a bounded timeout.

#### Scenario: Valid path exists

WHEN `get_path` returns a non-empty path from the robot's current estimated pose to the target landmark pose,  
THEN the path feasibility check SHALL pass and the system SHALL be authorized to dispatch the navigation goal via the `navigate_to_landmark` function.

#### Scenario: No valid path found

WHEN `get_path` returns an empty result or indicates that no path can be found (e.g., the target is surrounded by inflated obstacles or the robot footprint cannot traverse the route),  
THEN the system SHALL report `NO_PATH` to the user, SHALL NOT dispatch a navigation goal, and SHALL log the path check result including the target landmark ID.

#### Scenario: Path check times out

WHEN the `get_path` request does not return within the configured timeout,  
THEN the system SHALL report `NOT_READY` or `NO_PATH` (as appropriate to the error type), SHALL NOT dispatch a goal, and SHALL log the timeout event.

### Requirement: Pre-Check Result Logging

The system SHALL record the outcome and observed diagnostics of every pre-dispatch validation check in the structured event log.

#### Scenario: Pre-check passes

WHEN all pre-dispatch checks pass and goal dispatch is authorized,  
THEN the system SHALL log a combined pre-check pass event with the mission ID and timestamp before dispatching the goal.

#### Scenario: Pre-check fails at any stage

WHEN any pre-dispatch check fails,  
THEN the system SHALL log the failure event with the check type, observed values, and the resulting system state (`NOT_READY` or `NO_PATH`), and SHALL NOT proceed to goal dispatch.

### Requirement: No Goal Dispatch on Pre-Check Failure

Under all conditions, the system SHALL NOT dispatch a Nav2 navigation goal if any pre-dispatch validation check has returned a failure result.

#### Scenario: Sensor check passes but localization fails

WHEN the sensor freshness check passes but the localization check fails,  
THEN the system SHALL report `NOT_READY`, SHALL NOT dispatch a goal, and SHALL log the failed check as the cause.
