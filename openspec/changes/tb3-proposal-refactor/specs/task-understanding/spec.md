## Purpose

The `task-understanding` capability defines how the system receives Chinese-language user commands, parses them into schema-constrained structured intents using an LLM, validates intent and landmark against an allowlist, and handles ambiguous, unknown, or out-of-scope inputs by requesting clarification or issuing a rejection — all before any navigation goal is dispatched.

## ADDED Requirements

### Requirement: Chinese Task Reception

The system SHALL accept user input as free-form Chinese-language text submitted to the Task Input module and SHALL NOT require users to format commands in a predefined syntax.

#### Scenario: Explicit navigation command received

WHEN a user submits a Chinese-language command that names a registered landmark (e.g., 「請去實驗桌」),  
THEN the system SHALL parse the input, produce a schema-constrained structured intent object (e.g., `{"intent": "navigate", "landmark_id": "desk"}`), and pass it to the validation layer without dispatching a navigation goal.

#### Scenario: Command received while mission is active

WHEN a user submits a new navigation command while a navigation mission is already active,  
THEN the system SHALL reject the new command, SHALL NOT dispatch a second navigation goal, and SHALL report to the user that a mission is already in progress.

### Requirement: LLM-Based Intent Parsing

The Task Agent SHALL use an LLM to parse Chinese-language user inputs into schema-constrained structured intents, and SHALL NOT output coordinates or control velocities directly.

#### Scenario: Successful intent parsing

WHEN the user's input is a navigation command for a potentially valid destination,  
THEN the system SHALL produce a structured intent object with at least `intent` and `landmark_id` fields, and SHALL log the LLM model identifier, prompt hash, and timestamp.

#### Scenario: LLM service unavailable or timeout

WHEN the LLM service does not respond within the configured timeout,  
THEN the system SHALL report a clarification or error state to the user and SHALL NOT dispatch a navigation goal.

### Requirement: Schema and Allowlist Validation

The tool validation layer SHALL validate structured intents against a JSON schema and registered landmark allowlist, rejecting unrecognized tool names or unregistered landmarks.

#### Scenario: Valid intent and registered landmark

WHEN the structured intent passes schema validation and the `landmark_id` is present in the landmark registry,  
THEN the system SHALL pass the validated intent to the navigation-validation capability for pre-dispatch state checking.

#### Scenario: Unknown landmark rejected

WHEN the `landmark_id` in the structured intent does not match any entry in the landmark registry,  
THEN the system SHALL report `REJECTED` to the user, SHALL NOT dispatch a navigation goal, and SHALL log the rejected intent with the reason.

#### Scenario: Invalid tool name rejected

WHEN the structured intent names a tool not on the allowlist (e.g., a request for direct velocity control),  
THEN the system SHALL report `REJECTED` to the user, SHALL NOT invoke the disallowed tool, and SHALL log the rejection.

### Requirement: Clarification for Ambiguous Input

The system SHALL issue a CLARIFY response to the user and SHALL NOT dispatch a navigation goal when parsed user intent is ambiguous, underspecified, or maps to multiple candidate landmarks.

#### Scenario: Multiple landmarks match the destination description

WHEN the user's command could map to two or more registered landmarks (e.g., 「去門口」 when both "door-north" and "door-south" are registered),  
THEN the system SHALL present the user with the candidate landmarks and SHALL wait for explicit selection before proceeding.

#### Scenario: Underspecified command

WHEN the user's command does not contain sufficient information to identify a unique navigation intent,  
THEN the system SHALL ask the user to clarify the missing information and SHALL NOT proceed to dispatch.

### Requirement: Out-of-Scope Command Rejection

The system SHALL reject out-of-scope commands requesting unauthorized tool actions or unregistered operational behaviors, and SHALL NOT execute disallowed operations.

#### Scenario: Disallowed tool operation requested

WHEN the user's command requests an action not permitted by domain rules (such as raw motor commands or unmapped spatial areas),  
THEN the system SHALL report `REJECTED` to the user with an explanation and SHALL NOT attempt execution.

#### Scenario: Compound command requesting multiple simultaneous goals

WHEN the user's command contains multiple navigation destinations or requests multiple simultaneous actions,  
THEN the system SHALL enforce the single-task constraint by either requesting clarification about which destination to execute first, or by rejecting the compound command with an explanation.

### Requirement: Explicit Cancellation Priority

The system SHALL prioritize explicit cancellation commands over any pending or new movement requests, invoking cancellation immediately and confirming action termination.

#### Scenario: Cancellation while navigating

WHEN the user sends a cancellation command during active navigation,  
THEN the system SHALL call the cancel function on the active navigation action, SHALL NOT dispatch a new goal, and SHALL report `CANCELED` only after receiving confirmation from the action server.
