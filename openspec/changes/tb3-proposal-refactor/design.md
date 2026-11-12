# System Design: TB3 AI Agent for Indoor Navigation

> **Change**: tb3-proposal-refactor
> **Source slides**: 4, 7, 13–16, 21, 24–28 (TB3_Proposal_Final_35slides.pptx), Appendix A03–A07
> **Status**: This is a proposed design document. No implementation has been completed or validated.

---

## 1. Overview

This design document describes the architecture and design decisions for the TurtleBot3 Burger AI Agent for indoor navigation and task decision-making. The system accepts Chinese-language user commands, parses them using an LLM constrained by a JSON schema and landmark allowlist, performs pre-dispatch robot-state validation, dispatches validated goals to Nav2, monitors execution, and delivers evidence-grounded reports to the user.

The architecture is organized into eight modules (Slide 13) operating in two layers:
- **Interaction / Agent layer** (Modules ①–③, ⑧): user-facing communication and LLM-based task reasoning.
- **Knowledge / Validation layer** (Modules ④–⑦): data acquisition, state analysis, domain knowledge, and Nav2 tool dispatch.

The ROS 2 Humble + Nav2 stack handles all localization, planning, control, and real-time safety responses.

---

## 2. Platform Constraints

| Constraint | Value |
|-----------|-------|
| Robot platform | TurtleBot3 Burger (differential drive) |
| Operating system | Ubuntu 22.04 LTS |
| Middleware | ROS 2 Humble |
| Navigation stack | Nav2 (behaviour-tree based) |
| Sensor | 2D LiDAR (single horizontal scan plane) |
| Map type | Pre-built 2D occupancy grid (single indoor floor) |
| Destination model | Registered landmark registry (finite named set) |
| Language | Chinese (Mandarin) natural language input |

**Out of scope**: Multi-floor navigation, 3D obstacle detection, dynamic landmark registration, end-to-end learned navigation, remote deployment, and any modification of robot control code.

---

## 3. Module Architecture

### Module ① — User

**Responsibility**: Submit tasks in Chinese; confirm disambiguation responses.  
**Input**: Free-form Chinese text from a human user.  
**Output**: Raw task command string to Module ②.

### Module ② — Task Input

**Responsibility**: Receive and relay user messages.  
**Input**: Raw Chinese text from Module ①.  
**Output**: Task message to Module ③.

### Module ③ — Task Agent (LLM)

**Responsibility**: Parse intent, select tools, enforce schema constraints, and issue clarification requests.  
**Input**: Task message + data from Module ④ + rules from Module ⑥.  
**Output**: Structured intent (JSON schema-constrained) or CLARIFY/REJECTED response.  
**Constraint**: SHALL NOT output robot coordinates, wheel velocities, or any low-level control parameters.  
**Reference**: ReAct tool-use pattern [1], SayCan skill grounding [2].

### Module ④ — Data Acquisition

**Responsibility**: Query the landmark registry and read robot state (scan, odometry, localization, TF).  
**Input**: Data request from Module ③ or ⑤.  
**Output**: Observations with timestamps (freshness-checked).  
**Data sources**: `landmarks.json`, `/scan`, `/odom`, `/amcl_pose` (or equivalent), TF tree.

### Module ⑤ — Analysis Engine

**Responsibility**: Validate sensor freshness, TF chain validity, and path feasibility.  
**Input**: Data from Module ④ + rules from Module ⑥.  
**Output**: Validation result (pass / NOT\_READY / NO\_PATH) with evidence.  
**Functions**: Sensor freshness check, localization status check, `get_path` path feasibility check.

### Module ⑥ — Domain Knowledge

**Responsibility**: Provide the landmark registry, permission rules, and tool allowlist.  
**Input**: Registry query from Module ④; allowlist query from Module ③.  
**Output**: Registered poses, landmark aliases, allowed tool names, and domain rules.  
**Files**: `landmarks.json`, `domain_rules.md`.

### Module ⑦ — Decision and Tool

**Responsibility**: Dispatch validated goals to Nav2, cancel active goals, and monitor action status.  
**Input**: Validated request from Module ③ after Module ⑤ approval.  
**Output**: Nav2 action result (SUCCEEDED / FAILED / CANCELED) + event log entry.  
**Functions**: `navigate_to_landmark`, `cancel_navigation`, `read_mission_result`.  
**Constraint**: Only dispatches goals when Module ⑤ has confirmed all pre-checks pass.

### Module ⑧ — Output and Interaction

**Responsibility**: Deliver clarification requests and evidence-grounded reports to the user.  
**Input**: Mission state + evidence from Module ⑦; CLARIFY or REJECTED from Module ③.  
**Output**: User-facing message in Chinese or English with mission status and evidence reference.  
**Constraint**: SHALL NOT report ARRIVED without a SUCCEEDED Nav2 action result + pose evidence.

---

## 4. Data Flow

```
User (①) → Chinese task
         → Task Input (②)
         → Task Agent (③) ← Domain Knowledge (⑥)
                 ↓                    ↓
         Data Acquisition (④) → Analysis Engine (⑤)
                                        ↓ (validation result)
                             Decision & Tool (⑦)
                                        ↓
                                 Nav2 / ROS 2
                                 (Planning/Control/Localization)
                                        ↓
                             Nav2 action result + event log
                                        ↓
                             Task Agent (③) ← read_mission_result
                                        ↓
                             Output & Interaction (⑧) → User report
```

Low-latency obstacle response remains entirely within Nav2. The agent is notified of navigation outcomes via the Nav2 action result, not by intercepting the control loop.

---

## 5. Navigation Adapter Interface

The Navigation Adapter bridges the agent's tool calls and the Nav2 ROS 2 Humble API. It exposes the following functions:

| Function | Description | Nav2 Mapping (approximate) |
|----------|-------------|---------------------------|
| `get_path(landmark_id)` | Request path feasibility check to registered landmark | `ComputePathToPose` action (or equivalent in Humble) |
| `navigate_to_landmark(landmark_id)` | Dispatch a validated goal to the registered landmark pose | `NavigateToPose` action server |
| `cancel_navigation(mission_id)` | Cancel an active navigation goal | Action goal cancel request |
| `read_mission_result(mission_id)` | Read the terminal result of a completed action | Action result callback |

> **Note**: The specific ROS 2 Humble and Nav2 action server interfaces must be verified against the installed package versions before implementation. These function names are conceptual; the actual Python/ROS 2 implementation will map to available Humble APIs.

---

## 6. Landmark Registry

The landmark registry (`landmarks.json`) maps user-visible destination names and Chinese aliases to registered poses in the map frame. Each entry contains:

```json
{
  "landmark_id": "desk",
  "display_name": "實驗桌",
  "aliases": ["桌子", "實驗台"],
  "pose": {
    "frame": "map",
    "x": 0.0,
    "y": 0.0,
    "theta": 0.0
  },
  "source": "manually registered",
  "registered_at": "YYYY-MM-DD"
}
```

The registry is the authoritative allowlist for valid navigation destinations. Any `landmark_id` not present in the registry results in `REJECTED`.

---

## 7. Algorithm Summary

The full algorithm is provided in Appendix slides A06–A07 (Slides 27–28). The core logic is:

1. If the input is an explicit cancel: cancel active goal; return status.
2. Reject new movement while another mission is active.
3. Read domain knowledge and parse intent using LLM.
4. If parsing fails or times out: return clarification/error.
5. If intent is ambiguous: return CLARIFY; await user response.
6. Validate tool name, landmark ID, and schema against allowlists.
7. If validation fails: return REJECTED.
8. Read sensor data, localization, TF, and navigation lifecycle status.
9. If data are stale or checks fail: return NOT\_READY with evidence.
10. Resolve landmark to registered pose; request `get_path`.
11. If no valid path: return NO\_PATH.
12. Dispatch goal via `navigate_to_landmark`.
13. Monitor: read feedback + fresh status; append to event log.
    - If user cancels or status is unsafe/stale: cancel goal; return CANCELED or FAULT.
    - If SUCCEEDED: return ARRIVED with action result + observed final pose.
    - If FAILED: check retry eligibility; retry once if conditions met; else return FAILED.
14. If deadline expires: cancel; return TIMEOUT with log IDs.

---

## 8. Design Decisions and Rationale

### Decision 1: Fixed Nav2, Variable Agent

**Decision**: Nav2 is held constant across all baselines (B0, B1, B2).  
**Rationale**: To isolate the agent's contribution (clarification, pre-check, evidence reporting) from the navigation stack's capability. Without a fixed baseline, observed differences in navigation success could be attributed to tuning rather than agent features.  
**Trade-off accepted**: This design does not evaluate end-to-end or learned navigation alternatives. Comparative claims about Nav2 vs. alternatives are outside scope.  
**Source**: Research Debate I (Slide 8); see `docs/research-tradeoffs.md`.

### Decision 2: Pre-Built Map + Landmark Registry

**Decision**: Navigation is map-based using a pre-built 2D occupancy grid and a finite landmark registry.  
**Rationale**: Named-landmark navigation is a core user requirement. Global path feasibility checking requires a map. The laboratory environment is bounded and mappable.  
**Trade-off accepted**: The system cannot navigate to unregistered destinations or adapt to map changes. Dynamic obstacle handling relies on the real-time costmap.  
**Source**: Research Debate II (Slide 9); see `docs/research-tradeoffs.md`.

### Decision 3: Fixed Navigation Parameters

**Decision**: Footprint, inflation radius, velocity limits, and other Nav2 parameters are fixed after offline verification and held constant across all methods and trials.  
**Rationale**: To ensure that observed performance differences between B0, B1, and B2 are attributable to agent features, not parameter tuning.  
**Trade-off accepted**: A single operating point does not characterize the full safety–efficiency trade-off. Parameter values must be verified for the laboratory environment.  
**Source**: Research Debate III (Slide 10); see `docs/research-tradeoffs.md`.

### Decision 4: Hierarchical Control Separation

**Decision**: The LLM Task Agent operates at task level only; real-time control remains entirely within Nav2.  
**Rationale**: LLM inference latency (hundreds of milliseconds to seconds) is incompatible with real-time obstacle avoidance timescales (tens of milliseconds). Routing real-time control through an LLM would create safety risk.  
**Source**: Core Concept 4 (Slide 7); see `docs/core-concepts.md`.

### Decision 5: Evidence-Only Reporting

**Decision**: The system reports mission outcomes only when supported by Nav2 action results or event log entries.  
**Rationale**: Fabricated or inferred success claims undermine the reliability evaluation. The evaluation metric for reporting accuracy requires distinguishing evidence-supported from evidence-free reports.  
**Source**: Slide 16, Appendix A08 (Slide 29); evidence-reporting spec.

---

## 9. Baseline and Ablation Design

| Configuration | Nav2 | LLM | Schema/Allowlist | Clarification | Pre-check | Evidence Report |
|--------------|------|-----|-----------------|--------------|----------|----------------|
| B0 | ✓ | — | — | — | — | — |
| B1 | ✓ | ✓ | ✓ | — | — | — |
| B2 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| B2 − Clarification | ✓ | ✓ | ✓ | — | ✓ | ✓ |
| B2 − Agent Pre-check | ✓ | ✓ | ✓ | ✓ | — | ✓ |

Nav2's own safety mechanisms (costmap, collision monitor) are active in all configurations including ablations.

---

## 10. Evaluation Protocol Summary

| Dimension | Design |
|-----------|--------|
| Language tasks | 60 cases (20 dev / 40 test), 6 categories × 10 |
| Simulation runs | 40 per method (4 env × 2 routes × 5 reps) |
| Real-robot cases | 12 per method (36 case–method pairs) |
| Ablations | B2 − Clarification; B2 − Agent Pre-check |
| Metrics | Task handling accuracy, improper dispatch rate, reporting accuracy, navigation success rate, T0–T3 latency |
| Statistics | Sample size *n*; Wilson 95% CI for proportions; p50/p95 for latency |

> ⚠️ All experiment figures are planned targets. No results have been collected.

---

## 11. Implementation and Operational Notes

### 11.1 Language Reasoning and LLM Selection
- The LLM provider, model identifier, and prompt template are not permanently fixed in the specification; the active model identifier, prompt template hash, and inference hyper-parameters MUST be recorded in `events.jsonl` for each inference call during experiments.
- The landmark registry (`landmarks.json`) and domain rules (`domain_rules.md`) serve as the authoritative sources for schema and allowlist validation.
- The `task-understanding` capability is strictly bounded to task parsing and validation; navigation execution is delegated to `task-execution`.

### 11.2 State Verification and Sensor Thresholds
- The Navigation Adapter functions `get_path` and `navigate_to_landmark` are custom interfaces on top of the Nav2 ROS 2 Humble action APIs.
- Specific freshness thresholds (maximum permissible age for `/scan` messages and TF transforms) are environment- and network-dependent configuration parameters to be tuned during physical robot integration.
- `navigation-validation` governs pre-dispatch gating only; active navigation monitoring and runtime exceptions remain under `task-execution`.

### 11.3 Action Mapping, Deadlines, and Safety Boundaries
- Navigation Adapter functions `navigate_to_landmark` and `cancel_navigation` wrap Nav2 ROS 2 Humble action clients (`NavigateToPose`); their exact topic/action bindings must be confirmed against the installed ROS 2 Humble Nav2 distribution.
- Mission deadlines and retry limits (maximum one retry on qualified failure) are configurable operational parameters.
- Low-latency reactive obstacle avoidance resides strictly within the Nav2 local planner and collision monitor; the high-level agent never intercepts or replaces motor control loops.
- Terminal state evidence reporting is delegated to `evidence-reporting`.

### 11.4 Evidence Logging and Reporting Channels
- The structured event log (`events.jsonl`) serves as the single source of truth for both user-facing reporting and experimental metric extraction.
- User report delivery channels (ROS topic, CLI console, or web interface) are transport-level details decoupled from the core evidence state mapping.
- Reporting accuracy measurement is conducted within the `evaluation` capability.

### 11.5 Evaluation Integrity and Research Boundaries
- All experimental figures are planned protocol specifications, not achieved outcomes; no physical robot benchmark runs have been executed.
- Research questions RQ1 (improper dispatch), RQ2 (reporting accuracy), and RQ3 (interaction latency) remain open empirical questions to be resolved by the protocol.
- The evaluation dataset (event traces and CSV summaries) will be produced upon execution of the planned trials.

---

## References

| ID | Citation |
|----|----------|
| [1] | Yao, S., et al. (2023). ReAct. https://arxiv.org/abs/2210.03629 |
| [2] | Ahn, M., et al. (2022). Do As I Can. https://arxiv.org/abs/2204.01691 |
| [3] | Pollini, D., et al. (2025). Reducing Latency in LLM-Based Navigation. https://arxiv.org/abs/2506.00075 |
| [4] | Macenski, S., et al. (2020). The Marathon 2. https://arxiv.org/abs/2003.00368 |
| [5] | Macenski, S., & Jambrecic, I. (2021). SLAM Toolbox. https://joss.theoj.org/papers/10.21105/joss.02783 |
| [6] | Xiao, X., et al. (2022). BARN Challenge. https://arxiv.org/abs/2208.10473 |
| [8] | ROBOTIS. TurtleBot3 Features. https://emanual.robotis.com/docs/en/platform/turtlebot3/features/ |
| [9] | Nav2 Tuning Guide. https://docs.nav2.org/jazzy/configuration_and_development/tuning_guide/ |
| [10] | Nav2 Simple Commander API. https://docs.nav2.org/jazzy/configuration_and_development/simple_commander_api/simple_commander_api/ |
| [11] | Nav2 Collision Monitor. https://github.com/ros-navigation/navigation2/blob/main/nav2_collision_monitor/README.md |
