# Core Concepts: Five Design Principles

> **Source**: TB3_Proposal_Final_35slides.pptx — Slide 7 (Section 03 · Design Principles).
> References: [2] SayCan, [4] Nav2 Marathon 2, [5] SLAM Toolbox, [6] BARN Challenge, [8] TurtleBot3 Documentation, [9] Nav2 Tuning Guide.
>
> **Note**: These are widely adopted engineering principles in mobile robotics. They are presented as design constraints that govern the system architecture, not as propositions unique to this project. They are grounded in the cited literature but are not claimed to have unanimous expert consensus beyond their documented evidence bases.

---

## Principle 1 — Localization Uncertainty

**Design Principle (Slide 7)**: 定位具有不確定性 (*Localization has inherent uncertainty*)

**System Implication (Slide 7)**: 檢查定位、TF 與資料時間 (*Check localization, TF, and data timestamps*)

### Meaning

A mobile robot's belief about its position in the world is an estimate, not a measurement. On the TurtleBot3 Burger, wheel odometry accumulates drift when wheels slip or the robot is physically constrained (e.g., stuck against a wall while continuing to rotate). The localization stack (AMCL or equivalent) fuses odometry and LiDAR scan data to estimate position in the map frame, but divergence between the `map → odom → base_link` transform chain indicates potential localization error.

### Evidence Basis

Nav2's Marathon 2 field trials document the importance of monitoring localization status across extended navigation runs [4]. SLAM Toolbox provides the mapping and localization infrastructure; its documentation discusses map consistency and odometry integration [5]. TurtleBot3 technical documentation covers wheel encoder characteristics and their limitations [8].

**Evidence boundary**: These sources support the importance of monitoring localization; they do not guarantee that any specific check eliminates localization failures on the Burger in this laboratory environment.

### Design Implications for This System

- The agent's pre-dispatch check SHALL verify that the robot's localization confidence is within an acceptable threshold before dispatching a Nav2 goal.
- The check SHALL inspect the `map → odom → base_link` TF chain for validity and recency.
- If localization is deemed invalid, the agent SHALL report `NOT_READY` and SHALL NOT dispatch a goal.
- Localization checks do not replace Nav2's own recovery behaviours during goal execution.

---

## Principle 2 — Sensor Limitations

**Design Principle (Slide 7)**: 感測不到不等於沒有障礙物 (*Not sensed does not mean no obstacle*)

**System Implication (Slide 7)**: 資料過期禁止啟動；揭露雷射盲區 (*Stale data prohibits dispatch; disclose LiDAR blind spots*)

### Meaning

The TurtleBot3 Burger uses a 2D LiDAR that scans a single horizontal plane at a fixed height. Objects above or below this scan plane — table surfaces, overhanging structural elements, objects on elevated shelves, or low objects below scan height — are invisible to the sensor. Additionally, sensor data has a timestamp; if the most recent scan is older than a defined threshold, it cannot be assumed to reflect the current environment.

### Evidence Basis

TurtleBot3 technical documentation specifies LiDAR scan height and angular range [8]. Nav2 documentation discusses costmap update latency and sensor data age [9]. The BARN Challenge demonstrated that even high-performing navigation systems fail in constrained environments, partly attributable to sensor coverage limitations [6].

**Evidence boundary**: Blind-spot geometry is platform-specific. Disclosed limitations cannot be remedied by software alone; some require environmental management or additional sensors.

### Design Implications for This System

- The agent's pre-dispatch check SHALL verify that the most recent LiDAR scan message is within a configured freshness threshold.
- The agent SHALL NOT use stale sensor data as evidence of a clear path.
- The system documentation and user-facing reports SHALL disclose the 2D LiDAR blind spot above the scan plane.
- Stale data at pre-dispatch SHALL result in a `NOT_READY` report; the agent SHALL NOT proceed to goal dispatch.

---

## Principle 3 — Physical Navigation Feasibility

**Design Principle (Slide 7)**: 路徑要容納實際車身 (*The path must accommodate the physical robot body*)

**System Implication (Slide 7)**: 核對 footprint，測試窄通道 (*Verify footprint; test narrow passages*)

### Meaning

A path whose centre-line geometrically clears an obstacle does not guarantee that the full robot footprint can traverse the path. Nav2's costmap inflates obstacles by the robot's footprint radius; if the inflation radius plus footprint width exceeds the available gap, the planner will reject the path even when the centre-line appears clear. The TurtleBot3 Burger's footprint must be accurately configured in Nav2 parameters, and narrow passages in the laboratory environment must be physically tested to confirm passability.

### Evidence Basis

The Nav2 Tuning Guide provides guidance on footprint configuration and inflation radius trade-offs [9]. The BARN Challenge results illustrate that footprint-to-gap ratio is a critical factor in navigation success in constrained environments [6]. SayCan's grounding principle — that language plans must conform to robot physical capabilities — applies here to spatial navigation [2].

**Evidence boundary**: Footprint configuration is environment-specific; parameters validated in one environment do not automatically transfer to another layout.

### Design Implications for This System

- The Burger's footprint MUST be accurately configured in Nav2 parameters before any navigation experiment.
- The agent SHALL perform a path feasibility check (using the Navigation Adapter's `get_path` function) before dispatching a goal.
- If no feasible path exists, the agent SHALL report `NO_PATH` and SHALL NOT dispatch a goal or claim that the destination is unreachable due to a physical obstacle (the distinction may be unresolvable from planning data alone).
- The agent SHALL NOT permit modification of footprint parameters to force goal acceptance.

---

## Principle 4 — Hierarchical Agent and Navigation Control

**Design Principle (Slide 7)**: 語意任務與即時控制分層 (*Semantic tasks and real-time control are hierarchically separated*)

**System Implication (Slide 7)**: LLM 提任務，Nav2 執行導航 (*LLM provides tasks; Nav2 executes navigation*)

### Meaning

Language reasoning and real-time robot control operate at fundamentally different timescales. LLM inference may take hundreds of milliseconds to seconds; obstacle avoidance and motor control must react in tens of milliseconds. Any architecture that routes real-time control decisions through an LLM risks unsafe latency. The proposed system enforces a strict layer boundary: the LLM and agent handle task-level decisions (parsing intent, selecting tools, reporting results), while Nav2 handles all planning, control, and real-time safety responses.

### Evidence Basis

ReAct demonstrates the interleaved reasoning-and-acting paradigm for LLM-based task handling, with tool calls and observations alternating at task level [1]. SayCan grounds language plans in robot skill affordances rather than allowing unconstrained LLM output to control actuators [2]. The Marathon 2 Nav2 architecture provides modular separation of localization, planning, control, and recovery [4]. The LLM–ROS 2 integration study [3] reports that latency between language command receipt and navigation dispatch is a measurable and architecturally significant constraint.

**Evidence boundary**: This principle is grounded in architectural design, not in a formal real-time performance measurement for this specific system. Actual latency for B2 is a planned measurement (RQ3).

### Design Implications for This System

- The LLM Task Agent SHALL NOT output robot coordinates, wheel velocities, or any low-level control command directly.
- All tool calls from the agent SHALL pass through a schema-validated, allowlist-restricted tool layer before reaching Nav2.
- Real-time obstacle avoidance SHALL remain entirely within Nav2's control loop; the agent monitors navigation status but does not modify it in real time.
- Explicit cancellation requests bypass LLM re-reasoning and are dispatched directly through the cancellation tool.

---

## Principle 5 — Reproducible Reliability Evaluation

**Design Principle (Slide 7)**: 可靠性要重複驗證 (*Reliability requires repeated verification*)

**System Implication (Slide 7)**: 保留失敗，多場景與多指標評估 (*Preserve failures; evaluate across multiple scenarios and metrics*)

### Meaning

A single successful navigation run does not establish reliability. Reliability is a statistical property that requires repeated trials under controlled and varied conditions. Failed trials, timeouts, and unconfirmed outcomes must be logged and reported, not discarded, because excluding failures inflates apparent success rates. Comparing methods requires the same environment conditions, start and goal configurations, and statistical reporting standards.

### Evidence Basis

The BARN Challenge [6] provides a framework for multi-condition navigation evaluation with explicit failure reporting. NavBench [7] reinforces the value of unified benchmarks across platforms and conditions. Statistical reporting norms (sample size *n*, confidence intervals for proportions) are standard practice in robotics evaluation literature.

**Evidence boundary**: This principle describes a sound evaluation methodology; the actual reliability figures for this system are unknown until experiments are completed.

### Design Implications for This System

- All trial outcomes (including FAILED, TIMEOUT, FAULT, NOT\_READY, NO\_PATH) SHALL be logged to `events.jsonl` and preserved in evaluation CSVs.
- Navigation success rate and task handling accuracy SHALL be reported with sample size *n* and Wilson 95% confidence interval.
- Simulation and physical robot results SHALL be reported separately; Gazebo results SHALL NOT be presented as equivalent to physical robot results.
- The evaluation design SHALL include 40 simulation runs per method and 12 paired real-robot cases per method (B0, B1, B2).

---

## Summary Table

| # | Principle | System Implication | Key References |
|---|-----------|-------------------|----------------|
| 1 | Localization uncertainty | Check TF chain and localization before dispatch | [4][5][8] |
| 2 | Sensor limitations | Enforce data freshness; disclose LiDAR blind spots | [6][8][9] |
| 3 | Physical navigation feasibility | Verify footprint; check path before dispatch | [2][6][9] |
| 4 | Hierarchical agent/navigation control | LLM for tasks; Nav2 for real-time control | [1][2][3][4] |
| 5 | Reproducible reliability evaluation | Preserve failures; multi-scenario multi-metric reporting | [6][7] |

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
