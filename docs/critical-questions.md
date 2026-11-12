# Critical Questions: Q1–Q10

> **Source**: TB3_Proposal_Final_35slides.pptx — Slides 11–12 (Section 04 · Critical Questions), Slide Notes for Slides 11–12.
>
> These ten questions were identified in the original proposal as critical technical challenges. They are preserved verbatim (Chinese original) with English translation, source slide attribution, and system design implications drawn from the slide notes. Where a question identifies an unresolved issue, it is preserved as such and not presented as solved.

---

## Group I — Localization, Sensing, and Navigation Planning

*Source: Slide 11 (Original Slide 11)*

---

### Q1 — Odometry Divergence During Physical Constraint

**Chinese (Slide 11)**: 輪子轉動但小車被卡住時，里程計和真實位置可能如何不同？

**English**: When the wheels rotate but the robot is physically stuck, how might the odometric estimate diverge from the robot's true position?

**Context and Analysis** (Slide Notes, Slide 11):

When the robot is physically constrained (e.g., wedged against a wall or caught on an obstacle), wheel encoders continue to report rotation, causing the odometric position estimate to accumulate a fictitious displacement. The assumption that wheel velocity equals ground-plane velocity fails when traction is lost. The divergence between odometric and true position may not be immediately visible in LiDAR scans if the robot has not moved far enough to shift the scan pattern.

Cross-checking scan alignment, localization confidence, TF chain validity, navigation action feedback, and direct observation is necessary to avoid treating odometric progress as evidence of physical progress.

**System Implication**: Progress monitoring SHALL use navigation action feedback (from Nav2), not odometric displacement alone. Anomalous state detection (stuck, timeout) SHALL be logged as a separate event class.

**Evidence basis**: Nav2 Marathon 2 [4]; TurtleBot3 technical documentation [8].

---

### Q2 — Distinguishing Localization Error from Controller Error

**Chinese (Slide 11)**: 地圖正確但車子沿牆偏移，如何區分定位錯誤與控制問題？

**English**: If the map is correct but the robot drifts along a wall, how do you distinguish a localization error from a controller or planner error?

**Context and Analysis** (Slide Notes, Slide 11):

Lateral drift along a wall can result from: (a) localization error — the AMCL particle filter has converged to an incorrect pose, so the reference trajectory is wrong; (b) controller error — the trajectory tracker produces lateral error relative to a correctly computed reference; (c) costmap/footprint misconfiguration — the planner generates a path that clips wall boundaries due to incorrect inflation settings; or (d) odometry drift during the trajectory.

Diagnosis procedure: Compare LaserScan against the map wall in RViz to check scan-to-map alignment. Inspect `map → odom → base_link` transform continuity. Verify localization stability metrics. If the scan aligns with the map but the robot drifts physically, the issue is more likely controller or odometry related.

**System Implication**: The agent's state check SHALL inspect sensor data freshness and TF chain validity before dispatch. Post-hoc failure classification SHALL be recorded (localization error, controller error, or unclassified) in the event log.

**Evidence basis**: Nav2 [4]; SLAM Toolbox [5].

---

### Q3 — LiDAR Blind Spots and Table-Top Obstacles

**Chinese (Slide 11)**: LiDAR 顯示沒有障礙物，為什麼仍可能撞到桌子？

**English**: If the LiDAR shows no obstacle, why might the robot still collide with a table?

**Context and Analysis** (Slide Notes, Slide 11):

The TurtleBot3 Burger's 2D LiDAR scans a single horizontal plane at a fixed height. Table surfaces, overhanging structural elements, and objects above the scan plane are entirely invisible to the sensor. The costmap will show no obstacle, the planner will generate a clear path, and the robot will navigate directly into the table's legs or underside until a leg enters the scan plane (which may happen only when the robot is already in contact).

Additionally, stale LiDAR data (data age exceeding a freshness threshold) will cause the costmap to reflect a past state rather than the current environment.

**System Implication**: The system documentation SHALL disclose the 2D LiDAR scan height limitation. Sensor freshness SHALL be checked before dispatch. The system SHALL NOT claim that an absence of LiDAR returns guarantees a collision-free path in environments with elevated obstacles. Laboratory environment management (removing elevated obstacles from paths) is a prerequisite for safe operation.

**Evidence basis**: TurtleBot3 documentation [8].

**Unresolved**: The blind spot cannot be eliminated by software. Mitigation requires environment management or additional sensors (outside scope of this project).

---

### Q4 — Footprint vs. Centre-Line Passability

**Chinese (Slide 11)**: 中心線能穿過窄門，為何規劃器仍拒絕？

**English**: If the path centre-line passes through a narrow doorway, why does the planner still reject the path?

**Context and Analysis** (Slide Notes, Slide 11):

Nav2's costmap inflates obstacles by the robot's footprint radius. If the door opening width minus the inflation radius on each side is less than the robot's footprint width, the inflated costmap will mark the entire door opening as occupied, and the global planner will find no valid path. This occurs even when the geometric centre-line of the robot would fit through the door.

Contributing factors include: costmap resolution (cells may be coarser than the door opening), robot turning pose at the doorway (the planner must account for the full swept footprint during the turn), and actual footprint configuration in Nav2 parameters.

**System Implication**: The Burger footprint MUST be correctly configured before experiments. Narrow passages in the laboratory SHALL be physically tested to confirm passability. If `get_path` returns no valid path, the agent SHALL report `NO_PATH` without claiming the destination is physically unreachable (the distinction between planner rejection and physical obstruction may be unresolvable from planning data alone). The agent SHALL NOT permit footprint parameter modification to force path acceptance.

**Evidence basis**: Nav2 Tuning Guide [9].

---

### Q5 — New Obstacle After Global Planning

**Chinese (Slide 11)**: 已完成全域規劃後出現新箱子，哪個模組應先反應？

**English**: After global planning is complete, a new box appears on the path. Which module should react first?

**Context and Analysis** (Slide Notes, Slide 11):

Real-time obstacle response must not wait for LLM re-reasoning. The correct response sequence is: (1) the LiDAR scan updates the local costmap; (2) Nav2's local planner or recovery behaviours detect the obstacle and respond (stop, re-plan locally, or trigger recovery); (3) if the obstacle blocks the global path, Nav2 may trigger a global re-plan or report navigation failure; (4) the agent monitors the navigation action result and reports accordingly.

**System Implication**: Low-latency obstacle response SHALL remain entirely within Nav2's control loop. The agent SHALL NOT intercept Nav2's real-time obstacle response. The agent monitors navigation status (via action feedback) and reports the final outcome, including failure due to obstacle.

**Evidence basis**: Nav2 [4]; Nav2 Collision Monitor [11].

---

## Group II — Reliability and Agent Evaluation

*Source: Slide 12 (Original Slide 12)*

---

### Q6 — Does Larger Inflation Radius Always Mean Safer?

**Chinese (Slide 12)**: 擴大 inflation_radius 是否必然更安全？

**English**: Does increasing the inflation radius necessarily make navigation safer?

**Context and Analysis** (Slide Notes, Slide 12):

Increasing `inflation_radius` changes the cost distribution around obstacles in the costmap but does not directly increase the robot's physical safety margin. Effects of over-inflation include: narrow passages becoming geometrically impassable to the planner (even when the robot's physical footprint would fit); longer, less efficient planned paths that increase completion time and energy consumption; and in extreme cases, no valid path being found even for large open spaces.

Safety is better characterized by measuring arrival rate, collision events, minimum clearance distance, and completion time under a given parameter set, rather than assuming more inflation equals more safety.

**System Implication**: Navigation parameters SHALL be fixed at empirically verified values after laboratory testing. The evaluation SHALL measure arrival rate, collision events (zero planned; logged if they occur), minimum clearance, and completion time for each method under the same fixed parameters. Parameter values SHALL be documented in the design artifact.

**Evidence basis**: Nav2 Tuning Guide [9].

---

### Q7 — Comparing Methods with Different Collision Rates

**Chinese (Slide 12)**: 較快方法偶爾碰撞，較慢方法沒有觀察到碰撞，如何比較？

**English**: If a faster method occasionally collides and a slower method shows no observed collisions, how do you compare them?

**Context and Analysis** (Slide Notes, Slide 12):

Zero observed collisions in a small sample does not establish a collision probability of zero. With 12 real-robot cases per method, a Wilson 95% confidence interval for zero collisions out of 12 trials extends to approximately 22%. Comparisons must account for: identical scenario definitions (same start, goal, environmental state), identical trial counts, and statistical reporting including sample size *n* and confidence intervals.

Declaring the slower method "safer" on the basis of zero observed collisions in a small sample is statistically unsound. Both methods require the same number of trials, same scenarios, and the same reporting of uncertainty.

**System Implication**: The evaluation SHALL report sample size *n*, observed counts, and Wilson 95% confidence intervals for all proportional metrics. Zero observed collisions SHALL be reported as "0/n (95% CI: 0–x%)" rather than as a definitive safety claim. Simulation and physical robot results SHALL be reported separately.

**Evidence basis**: BARN Challenge evaluation methodology [6]; statistical reporting norms.

---

### Q8 — Gazebo Success, Physical Robot Failure

**Chinese (Slide 12)**: Gazebo 成功但實機失敗，要先檢查什麼？

**English**: If the Gazebo simulation succeeds but the physical robot fails, what should be checked first?

**Context and Analysis** (Slide Notes, Slide 12):

Simulation-to-real transfer gaps arise from: sensor model differences (ideal vs. noisy LiDAR, perfect vs. drifting odometry), timing differences (simulation may run faster or slower than real-time), environment geometry differences (Gazebo model vs. actual laboratory layout), footprint and physics model accuracy, communication latency, and software configuration differences between the simulation and physical robot setup.

Diagnostic priority: sensor freshness and TF validity on the physical robot; localization convergence; odometry vs. scan alignment; costmap and footprint parameter consistency between simulation and physical configuration; navigation action server availability and response.

**System Implication**: Simulation and physical robot results SHALL be reported separately and SHALL NOT be aggregated. The model version, Nav2 configuration, and software versions SHALL be documented for both environments. When a discrepancy is observed, the event log SHALL record the full configuration context.

**Evidence basis**: BARN Challenge [6]; TurtleBot3 documentation [8].

---

### Q9 — Proving Agent Contribution vs. Nav2 Alone

**Chinese (Slide 12)**: 如何證明 Agent 有幫助，而不是 Nav2 本來就會導航？

**English**: How do you demonstrate that the agent contributes, rather than Nav2 alone being capable of the navigation?

**Context and Analysis** (Slide Notes, Slide 12):

This is the core research question addressed by the baseline comparison design. Nav2 is fixed and identical across all baselines. B0 provides a fixed-menu (no language) baseline that isolates Nav2's raw navigation capability for valid pre-registered goals. B1 adds language parsing (LLM + Schema + Allowlist) without clarification or pre-check. B2 adds clarification, agent pre-check, and evidence reporting.

The incremental contribution of agent features is measured by comparing: improper dispatch rate (B0 N/A, B1 vs. B2), reporting accuracy (B0 N/A for language tasks, B1 vs. B2), task handling accuracy, and interaction latency across baselines. Two ablations (B2 − Clarification, B2 − Agent Pre-check) further isolate individual feature contributions. Nav2's underlying safety protections are not removed in any ablation.

**System Implication**: The evaluation design (B0, B1, B2 with ablations) SHALL be implemented as specified. Language parsing metrics are N/A for B0 (which has no language input) and SHALL be clearly marked as N/A rather than zero. All baseline comparisons SHALL use the same map, Nav2 parameters, and goal set.

**Evidence basis**: Original proposal Slides 17, 21.

---

### Q10 — Ambiguous Destination and Mid-Task Cancellation

**Chinese (Slide 12)**: 使用者說去門口但有兩個門口，或途中取消，系統該怎麼辦？

**English**: If the user says "go to the door" but there are two doors, or if the user cancels mid-task, what should the system do?

**Context and Analysis** (Slide Notes, Slide 12):

**Ambiguous destination**: When the parsed intent maps to multiple registered landmarks (e.g., "door" resolves to both "door-north" and "door-south"), the agent MUST issue a clarification request and wait for user input before dispatching any goal. The agent SHALL NOT guess a coordinate, select arbitrarily, or dispatch to the nearest match without user confirmation.

**Mid-task cancellation**: An explicit cancellation command from the user triggers immediate invocation of the cancellation tool. The agent SHALL wait for Nav2 to confirm the cancellation via the action result before reporting `CANCELED`. Issuing a cancellation request does not guarantee immediate physical stop; the robot may continue moving until Nav2 confirms termination. If cancellation is not confirmed within a timeout, the agent SHALL report `FAULT` or an unconfirmed-cancellation state rather than claiming the robot has stopped.

**System Implication**: The clarification mechanism SHALL be invoked for ambiguous destinations before any goal dispatch. The single-task-lock constraint SHALL prevent new goals from being dispatched while a mission is active. Cancellation SHALL take priority over new goal dispatches. All cancellation outcomes SHALL be logged with confirmation status.

**Evidence basis**: ReAct tool-use pattern [1]; SayCan skill grounding [2]; Nav2 Simple Commander API [10] (specific API availability for ROS 2 Humble must be verified against installed version).

---

## Summary Table

| Q | Category | Core Issue | System Response |
|---|----------|------------|-----------------|
| Q1 | Localization | Odometry divergence when stuck | Monitor via action feedback; log anomalous state |
| Q2 | Localization | Drift diagnosis (localization vs. controller) | Pre-dispatch TF/state check; post-hoc error classification |
| Q3 | Sensing | LiDAR blind spot above scan plane | Disclose limitation; enforce data freshness; environment management |
| Q4 | Planning | Footprint vs. centre-line passability | Correct footprint config; `get_path` check; report `NO_PATH` |
| Q5 | Control | New obstacle after global plan | Real-time response stays in Nav2; agent monitors outcome |
| Q6 | Evaluation | Inflation radius ≠ safety | Fixed verified params; measure arrival, collision, time |
| Q7 | Evaluation | Small-sample collision comparison | Report *n* + Wilson CI; separate simulation/physical |
| Q8 | Evaluation | Sim-to-real gap | Separate reporting; document all configs |
| Q9 | Evaluation | Agent contribution vs. Nav2 alone | Controlled B0/B1/B2 comparison + ablations |
| Q10 | Agent | Ambiguity and cancellation handling | CLARIFY before dispatch; cancellation-first; log all outcomes |

---

## References

| ID | Citation |
|----|----------|
| [1] | Yao, S., et al. (2023). ReAct: Synergizing Reasoning and Acting in Language Models. https://arxiv.org/abs/2210.03629 |
| [2] | Ahn, M., et al. (2022). Do As I Can, Not As I Say: Grounding Language in Robotic Affordances. https://arxiv.org/abs/2204.01691 |
| [4] | Macenski, S., et al. (2020). The Marathon 2: A Navigation System. https://arxiv.org/abs/2003.00368 |
| [5] | Macenski, S., & Jambrecic, I. (2021). SLAM Toolbox: SLAM for the dynamic world. https://joss.theoj.org/papers/10.21105/joss.02783 |
| [6] | Xiao, X., et al. (2022). Autonomous Ground Navigation in Highly Constrained Spaces: BARN Challenge. https://arxiv.org/abs/2208.10473 |
| [8] | ROBOTIS. TurtleBot3 Features and Navigation. https://emanual.robotis.com/docs/en/platform/turtlebot3/features/ |
| [9] | ROS Navigation maintainers. Nav2 Tuning Guide. https://docs.nav2.org/jazzy/configuration_and_development/tuning_guide/ |
| [10] | ROS Navigation maintainers. Nav2 Simple Commander API. https://docs.nav2.org/jazzy/configuration_and_development/simple_commander_api/simple_commander_api/ |
| [11] | ROS Navigation maintainers. Nav2 Collision Monitor. https://github.com/ros-navigation/navigation2/blob/main/nav2_collision_monitor/README.md |
