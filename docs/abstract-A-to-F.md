# Abstract: A–F Research Structure

> **Source**: TB3_Proposal_Final_35slides.pptx — Slides 2–4, 17–21.
> **Status**: This document is a research **proposal**. No robot experiments have been conducted. All experimental outcomes are planned and remain unverified.

---

## A — Attention Getter

*Research background and motivation* (Source: Slides 2–3)

Recent work in language-controlled robotics has demonstrated that large language models (LLMs) can reason over task descriptions and invoke external tools to execute plans [1, 2]. Integration of natural language processing with ROS 2 navigation stacks has also attracted increasing attention, with reported benefits in reducing command-interpretation latency [3]. At the same time, mobile robot navigation in constrained indoor environments remains an open challenge: narrow passages, sensor blind spots, and localization uncertainty contribute to reliability failures even when navigation stacks such as Nav2 perform well in simulation [4, 6].

The TurtleBot3 Burger is a low-cost differential-drive platform widely used in ROS 2 research. Operating it in a real-world indoor environment — accepting Chinese-language task commands, navigating to named landmarks, and reporting results to users — requires a system that bridges high-level language understanding and low-level navigation reliability. No existing study within this laboratory context evaluates the combined benefit of task clarification, pre-dispatch robot-state validation, and evidence-grounded reporting on a physical TurtleBot3 Burger running Ubuntu 22.04, ROS 2 Humble, and Nav2.

---

## B — But

*Limitations and research challenges* (Source: Slides 2–3, 7–10)

Despite progress, three classes of limitation motivate this study:

1. **Language–navigation integration gap.** Existing integrations of LLMs with ROS 2 (e.g., [3]) report latency improvements under specific simulation conditions and cannot be directly applied to this physical platform and use case. Landmark ambiguity (e.g., two doors) and out-of-scope commands (e.g., requesting raw velocity control) are not addressed.

2. **Navigation reliability under real-world conditions.** The TurtleBot3 Burger's 2D LiDAR has blind spots above its scan plane; wheel odometry diverges when wheels slip; and the robot footprint may prevent passage through narrow doorways even when the path centre-line clears the opening. Nav2 navigation can succeed in Gazebo simulation while failing on the physical robot due to differences in sensor timing, odometry, and environment geometry.

3. **Unverified agent contribution.** It is unclear how much of a fully integrated language agent's end-to-end performance is attributable to the underlying Nav2 navigation stack versus the agent's clarification, pre-check, and reporting components. Without a controlled baseline comparison, the incremental benefit of these agent features cannot be measured.

---

## C — Cure

*Proposed approach* (Source: Slides 3–4, 13–16)

This project proposes a **validation-aware AI agent** for indoor navigation on the TurtleBot3 Burger. The agent:

- Accepts Chinese-language task commands from a user.
- Parses intent using an LLM constrained by a JSON schema and a landmark allowlist, requesting clarification for ambiguous or unknown targets before dispatching any goal.
- Performs a pre-dispatch robot-state check: verifying sensor data freshness, localization status, TF chain validity, and path feasibility against the pre-built map.
- Dispatches validated navigation goals to Nav2, monitors execution, supports explicit cancellation, and handles timeouts and failures.
- Reports results grounded in Nav2 action outcomes and an event log, distinguishing ARRIVED, FAILED, CANCELED, NOT\_READY, NO\_PATH, CLARIFY, REJECTED, and TIMEOUT/FAULT states.

Three baselines enable controlled comparison:
- **B0**: Fixed menu + Nav2 (no language input, no agent).
- **B1**: LLM + Schema + Allowlist (language parsing, no clarification or pre-check).
- **B2**: Full agent (B1 + clarification + agent pre-check + evidence reporting).

---

## D — Development

*System architecture and methodology* (Source: Slides 4, 13–16, 25–28, Appendix A04–A07)

The system is organized into eight modules:

| Module | Responsibility |
|--------|---------------|
| ① User | Submit and confirm tasks in Chinese |
| ② Task Input | Receive and relay messages |
| ③ Task Agent | LLM-based intent parsing and tool selection |
| ④ Data Acquisition | Query landmark registry and robot state |
| ⑤ Analysis Engine | Validate sensor freshness and path feasibility |
| ⑥ Domain Knowledge | Landmark registry, permission rules |
| ⑦ Decision & Tool | Dispatch, cancel, monitor Nav2 actions |
| ⑧ Output & Interaction | Clarification requests and evidence-grounded reports |

The LLM receives a user query, produces a schema-constrained structured intent (e.g., `{"intent":"navigate","landmark_id":"desk"}`), and passes it to the tool layer. The tool layer validates the landmark against the allowlist, checks robot state and path feasibility, then dispatches a Nav2 action goal. Navigation feedback and action results are logged to `events.jsonl` and used to construct the user-facing report. Low-latency obstacle response remains entirely within Nav2; the agent monitors navigation status but does not replace real-time control.

The five core design principles (Slide 7) govern every architectural decision — see [`docs/core-concepts.md`](core-concepts.md).

---

## E — Experiments

*Planned evaluation protocol* (Source: Slides 17–19, Appendix A09–A12)

> ⚠️ **All experiments are planned. No results have been recorded yet.**

Three evaluation dimensions:

### Language Evaluation
- **60 Chinese-language task cases** across 6 categories × 10 cases each:
  Explicit commands, ambiguous commands, unknown locations, cancellation commands, compound commands, and out-of-scope commands.
- **Split**: 20 development / 40 test, partitioned by semantic template to prevent leakage.
- Applied to B0 (N/A for language metrics), B1, and B2.

### Simulation Evaluation
- **40 simulation runs per method** (B0, B1, B2): 4 environments × 2 routes × 5 repetitions.
- Same map, route definitions, and fixed Nav2 parameters across methods.
- Evaluated with Gazebo on Ubuntu 22.04, ROS 2 Humble.

### Real-Robot Evaluation
- **12 paired cases per method** (B0, B1, B2) = 36 case–method pairs total.
- Same valid goal set across all methods; number of repetitions per case pending finalization.
- Physical TurtleBot3 Burger in a single indoor floor with pre-built map.
- No intentional collision scenarios are arranged.

### Ablations
- **B2 − Clarification**: Remove clarification step from B2; measure impact on improper dispatch rate.
- **B2 − Agent Pre-check**: Remove agent pre-dispatch state/path check from B2; Nav2's own safety mechanisms are retained.

### Metrics
| Metric | Operational Definition |
|--------|------------------------|
| Task Handling Accuracy | Correct handling / applicable tasks |
| Improper Dispatch Rate | Invalid dispatched tasks / invalid-dispatch cases |
| Reporting Accuracy | Evidence-consistent reports / evaluable reports |
| Navigation Success Rate | Arrived / valid navigation trials |
| Latency (T0→T1→T2→T3) | Command / Dispatch / Navigation segments |

Proportions reported with sample size *n* and Wilson 95% confidence interval. Simulation and physical robot results reported separately.

---

## F — Findings

*Expected contributions and acknowledged limitations* (Source: Slide 20)

> ⚠️ **These are expected contributions from a planned study, not measured results.**

### Expected Contributions

1. **Task reliability assessment**: Planned experiments will evaluate whether clarification and agent-level state validation reduce improper goal dispatch compared to B0 and B1 baselines.
2. **Evidence-grounded reporting**: The system is designed to report only states supported by Nav2 action results and event log entries, avoiding unsupported success claims.
3. **Reproducible controlled comparison**: Fixed Nav2 parameters, shared map and route definitions, and ablation design allow attribution of performance differences to specific agent components.
4. **Documented failure analysis**: All failures, timeouts, and unconfirmed states are logged and preserved; they will be reported rather than discarded.

### Acknowledged Limitations

- **Pre-built map and registered landmarks**: The system cannot navigate to unregistered destinations or adapt to map changes.
- **Single indoor floor**: Results may not generalize to multi-floor or outdoor environments.
- **2D LiDAR blind spots**: Objects above the scan plane (e.g., table surfaces, overhanging beams) are not detected; the agent cannot compensate for this sensor limitation.
- **LLM and validation latency**: Clarification requests and pre-dispatch checks add interaction latency; the magnitude is a planned measurement, not a known result.
- **Simulation-to-real gap**: Gazebo results may not transfer to the physical robot; the two are reported separately.

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
