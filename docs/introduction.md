# Introduction

> **Change**: tb3-proposal-refactor
> **Source slides**: 2–4, 7, 17–21 (TB3_Proposal_Final_35slides.pptx)
> **Status**: This introduction describes a **research proposal**. All experimental outcomes are planned; none have been obtained.

---

## 1. Research Motivation

The convergence of large language models (LLMs) and robotic navigation systems has opened a pathway for natural language control of mobile robots. A user can describe a destination in natural language — "please go to the lab table" — and an LLM can parse that description into an actionable navigation goal. This capability is particularly relevant for Chinese-language user interfaces in laboratory and office environments, where users may prefer to issue commands without memorizing a command vocabulary.

However, language understanding alone is insufficient for reliable indoor robot operation. Navigation on a physical mobile robot involves continuous uncertainty: wheel odometry accumulates drift when wheels slip; 2D LiDAR sensors have blind spots above their scan plane; path planners may reject geometrically plausible routes when the robot's footprint cannot fit through an inflated costmap representation of a narrow passage; and localization may diverge without producing an immediately observable error. A system that parses a user's command correctly but dispatches a navigation goal under any of these conditions may navigate unsafely, fail silently, or report success without evidence.

Furthermore, natural language inputs introduce ambiguity that physical navigation does not. When a user says "go to the door" in a space with two doors, the system must ask for clarification rather than guess. When a user cancels a mission in progress, the system must confirm the cancellation through the navigation action server rather than assume the robot has stopped. These requirements go beyond what a navigation stack alone can provide.

This paper proposes a research project that addresses the gap between language command acceptance and reliable, verifiable indoor navigation on a physical TurtleBot3 Burger robot running Ubuntu 22.04, ROS 2 Humble, and Nav2.

---

## 2. Research Gap and Limitations of Existing Work

Recent work in LLM-based robot control has demonstrated the feasibility of connecting language reasoning to robot actions. Yao et al. [1] introduced the ReAct paradigm, in which an LLM alternates between reasoning steps and tool-call actions, using environmental feedback to update its plan. Ahn et al. [2] showed that language plans can be grounded in robot skill affordances, ensuring that only feasible actions are selected. Pollini et al. [3] demonstrated an LLM–ROS 2 integration architecture and reported latency improvements in a Gazebo simulation environment.

However, each of these contributions operates outside the specific context of this study:

- ReAct [1] addresses question-answering and web navigation tasks; it does not establish physical navigation safety on a TurtleBot3 Burger.
- SayCan [2] grounds language in robot affordances through pre-existing skill primitives; it does not address the landmark-allowlist validation, sensor freshness checking, or evidence-grounded reporting required in this project.
- Pollini et al. [3] report latency improvements under simulation conditions specific to their integration; these results cannot be directly extended to a physical Burger running this laboratory's configuration.

On the navigation side, Nav2 [4] provides a mature modular navigation stack with demonstrated performance in campus-scale deployments. SLAM Toolbox [5] provides the mapping and localization infrastructure. The BARN Challenge [6] documents navigation reliability challenges in constrained environments, relevant to this project's narrow-passage scenarios.

These navigation works, however, do not address Chinese-language command parsing, landmark disambiguation, or the measurement of an AI agent's incremental contribution beyond Nav2's inherent capability.

**Gap**: There is no existing study that evaluates — with controlled baselines and planned ablations — whether task clarification, pre-dispatch robot-state validation, and evidence-grounded reporting improve reliability and reporting accuracy for a Chinese-language-controlled TurtleBot3 Burger in a physical indoor environment, holding the Nav2 navigation stack constant.

---

## 3. Proposed Method

This project proposes a **validation-aware AI agent** that operates as a task-reasoning and validation layer above Nav2. The agent:

1. Receives Chinese-language user commands.
2. Parses intent using an LLM constrained by a JSON schema and a registered landmark allowlist, issuing clarification requests for ambiguous or underspecified inputs.
3. Performs pre-dispatch validation: checking sensor data freshness, localization status, transform chain validity (map → odom → base\_link), and path feasibility using the Nav2 path planner.
4. Dispatches validated goals to Nav2 via a Navigation Adapter interface.
5. Monitors navigation action feedback and handles explicit cancellation, deadline enforcement, and single-retry logic under qualified conditions.
6. Delivers mission status reports derived exclusively from Nav2 action results and a structured event log.

The agent is organized into eight modules (see `design.md`). Real-time obstacle avoidance remains entirely within Nav2's control loop; the agent monitors outcome but does not intercept navigation control.

Three baseline conditions enable controlled comparison:
- **B0**: Fixed menu + Nav2 (no language input, establishes Nav2's raw navigation capability).
- **B1**: LLM + Schema + Allowlist (language parsing without clarification or pre-check).
- **B2**: Full agent (B1 + clarification + agent pre-check + evidence reporting).

Two ablations (B2 − Clarification; B2 − Agent Pre-check) isolate individual feature contributions. Nav2's safety mechanisms are retained in all conditions.

---

## 4. Research Questions

Three research questions drive the evaluation design:

**RQ1 — Task Reliability**: Does task clarification and agent-level pre-dispatch state validation reduce the rate of improper goal dispatch compared to B0 and B1 baselines?

*Measured by*: Improper dispatch rate (invalid tasks where a navigation goal was sent / total invalid-dispatch cases), Task handling accuracy.

**RQ2 — Reporting Accuracy**: Does evidence-grounded reporting improve the consistency between reported and observed mission outcomes compared to B0 and B1?

*Measured by*: Reporting accuracy (evidence-consistent reports / evaluable reports).

**RQ3 — Interaction Latency**: What additional interaction latency does the full B2 agent introduce compared to B0 and B1?

*Measured by*: Latency segments T0→T1 (command to parsed intent), T1→T2 (intent to Nav2 goal dispatch), T2→T3 (dispatch to terminal navigation result), reported as p50 and p95.

---

## 5. Expected Contributions

> ⚠️ The following are **expected** contributions of a planned study. They are not measured results.

1. **Controlled baseline comparison framework**: A methodology for attributing performance differences between B0, B1, and B2 to specific agent-level features, using fixed Nav2 infrastructure across all conditions.

2. **Specification of a validation-aware agent**: A formal requirements specification (OpenSpec spec-driven format) covering task understanding, navigation validation, task execution, evidence reporting, and evaluation.

3. **Evaluation dataset design**: A defined protocol for 60 Chinese-language task cases, 40 simulation runs per method, and 12 paired real-robot cases per method, with event log and evaluation CSV formats.

4. **Documented failure analysis**: A planned commitment to log and report all failure outcomes (FAILED, TIMEOUT, FAULT, NOT\_READY, NO\_PATH, CANCELED) without exclusion, supporting reproducible reliability evaluation.

5. **System design documentation**: An eight-module architecture description with explicit data flow, Navigation Adapter interface, and design decision rationale, grounded in the project's five core design principles.

---

## 6. Paper Organization

The remainder of this proposal is organized as follows. Section 2 (Related Work) reviews LLM reasoning and tool use, ROS 2/Nav2 navigation, navigation reliability benchmarks, and the three research debates that informed this project's design decisions. The OpenSpec artifacts in `openspec/changes/tb3-proposal-refactor/` provide formal requirements specifications and the system design. The research methodology and evaluation protocol are documented in `docs/abstract-A-to-F.md` (Sections D and E) and the `evaluation` capability specification.

---

## References

| ID | Citation |
|----|----------|
| [1] | Yao, S., et al. (2023). ReAct: Synergizing Reasoning and Acting in Language Models. https://arxiv.org/abs/2210.03629 |
| [2] | Ahn, M., et al. (2022). Do As I Can, Not As I Say: Grounding Language in Robotic Affordances. https://arxiv.org/abs/2204.01691 |
| [3] | Pollini, D., et al. (2025). Reducing Latency in LLM-Based Natural Language Commands Processing for Robot Navigation. https://arxiv.org/abs/2506.00075 |
| [4] | Macenski, S., et al. (2020). The Marathon 2: A Navigation System. https://arxiv.org/abs/2003.00368 |
| [5] | Macenski, S., & Jambrecic, I. (2021). SLAM Toolbox: SLAM for the dynamic world. https://joss.theoj.org/papers/10.21105/joss.02783 |
| [6] | Xiao, X., et al. (2022). Autonomous Ground Navigation in Highly Constrained Spaces: BARN Challenge. https://arxiv.org/abs/2208.10473 |
| [7] | El-Hariry, M., et al. (2025). NavBench: A Unified Robotics Benchmark for RL-Based Autonomous Navigation. https://arxiv.org/abs/2505.14526 |
| [8] | ROBOTIS. TurtleBot3 Features and Navigation. https://emanual.robotis.com/docs/en/platform/turtlebot3/features/ |
| [9] | ROS Navigation maintainers. Nav2 Tuning Guide. https://docs.nav2.org/jazzy/configuration_and_development/tuning_guide/ |
| [10] | ROS Navigation maintainers. Nav2 Simple Commander API. https://docs.nav2.org/jazzy/configuration_and_development/simple_commander_api/simple_commander_api/ |
| [11] | ROS Navigation maintainers. Nav2 Collision Monitor. https://github.com/ros-navigation/navigation2/blob/main/nav2_collision_monitor/README.md |
| [12] | Xiao, X., and BARN organizers (2026). The BARN Challenge 2026. https://people.cs.gmu.edu/~xiao/Research/BARN_Challenge/BARN_Challenge26.html |
