# Related Work

> **Change**: tb3-proposal-refactor
> **Source slides**: 5–6, 8–10, 22–24 (TB3_Proposal_Final_35slides.pptx), Appendix A01–A03
> **Status**: Literature review for a research proposal. Claims are qualified by the evidence boundaries documented in the original proposal slides. No experimental results from this project are reported here.
>
> **Reference numbering**: Preserved from the original proposal (Slides 34–35, Appendix A13–A14).

---

## 1. LLM Reasoning and Tool Use

### 1.1 ReAct: Synergizing Reasoning and Acting

Yao et al. [1] introduced ReAct, a paradigm in which an LLM alternates between language reasoning traces and task-specific action calls (tool invocations), using the results of each action as new observations that inform subsequent reasoning. ReAct was evaluated on HotpotQA, FEVER, ALFWorld, and WebShop, demonstrating that interleaved tool interaction improves performance on knowledge-intensive and decision-making tasks compared to either pure reasoning or pure action without reasoning.

**Design relevance to this project**: ReAct motivates the design of the Task Agent (Module ③), in which the LLM selects tools (landmark lookup, sensor state check, navigation dispatch, cancel) and updates its task plan based on tool results. The event log plays the role of the observation stream.

**Evidence boundary** (Slide A01): ReAct was evaluated on textual and simulated tasks; it does not establish physical navigation safety or collision avoidance behaviour on the TurtleBot3 Burger. Claims about tool interaction cannot be directly transferred to physical robot safety guarantees.

### 1.2 SayCan: Grounding Language in Robotic Affordances

Ahn et al. [2] proposed SayCan, an approach that combines LLM planning with robot skill affordance functions. Rather than allowing an LLM to plan arbitrary action sequences, SayCan constrains the plan to actions that are feasible given the robot's current state, as measured by skill-specific value functions. SayCan was evaluated on a mobile manipulation robot performing real-world kitchen tasks.

**Design relevance to this project**: SayCan's grounding principle — that language plans must conform to robot physical capabilities — directly motivates both the landmark allowlist (restricting navigation to pre-registered, reachable destinations) and the agent pre-dispatch check (verifying that current sensor and localization state permits safe navigation). The five core design principles in this project (see `docs/core-concepts.md`) reflect a similar grounding philosophy for the differential-drive navigation context.

**Evidence boundary** (Slide A01): SayCan uses pre-existing skill primitives specific to its mobile manipulation platform. Its allowlist mechanism is conceptually analogous to, but architecturally distinct from, the schema and landmark registry used in this project. Results on the SayCan platform cannot be transferred to the TurtleBot3 Burger context.

### 1.3 LLM–ROS 2 Integration and Navigation Latency

Pollini et al. [3] investigated the latency characteristics of integrating ChatGPT with a ROS 2 architecture for natural language robot navigation commands. Their study reported latency improvements attributable to their specific integration architecture in a Gazebo simulation environment with a mobile robot.

**Design relevance to this project**: This work directly motivates RQ3 (interaction latency) in this project. The latency segments T0→T1 (command to parsed intent), T1→T2 (intent to dispatch), and T2→T3 (dispatch to terminal result) are designed to enable latency decomposition analogous to Pollini et al.'s measurement approach.

**Evidence boundary** (Slide A01): Results are reported under simulation conditions with a specific LLM provider and integration architecture. These results cannot be directly extrapolated to the TurtleBot3 Burger running in this laboratory's physical environment. The LLM provider and model for this project have not yet been selected; actual latency is a planned measurement, not a predicted value.

---

## 2. ROS 2 Navigation and SLAM

### 2.1 Nav2: Modular Navigation for ROS 2

Macenski et al. [4] describe the Marathon 2 Navigation System (Nav2), a behaviour-tree-based modular navigation framework for ROS 2. Nav2 provides modular components for localization, global path planning, local trajectory control, and recovery behaviours. Its field validation includes campus-scale navigation experiments with long-distance runs.

**Design relevance to this project**: Nav2 is the navigation infrastructure on which this project's agent layer operates. All navigation goal dispatch, monitoring, and real-time obstacle avoidance in this system go through Nav2. The modular architecture enables the agent to interact with specific Nav2 action servers (path planning, goal dispatch, cancellation) without replacing the control loop.

**Evidence boundary** (Slide A02): Nav2's campus navigation experiments do not address Chinese-language landmark disambiguation, pre-dispatch state validation, or evidence-grounded reporting. Nav2 provides navigation capability but does not constitute an AI agent for Chinese-language task understanding.

### 2.2 SLAM Toolbox: Mapping and Localization

Macenski and Jambrecic [5] describe SLAM Toolbox, an optimization-based simultaneous localization and mapping system for ROS 2. SLAM Toolbox provides tools for map construction, map loading, localization in a pre-built map, and map maintenance under changing conditions.

**Design relevance to this project**: SLAM Toolbox provides the mapping and localization infrastructure that the agent's pre-dispatch check depends on. The localization check verifies that the SLAM Toolbox-based localization is active and that the `map → odom → base_link` transform chain is current and valid before dispatching any navigation goal.

**Evidence boundary** (Slide A02): SLAM Toolbox is a general mapping and localization system; it does not address semantic task planning or complete obstacle avoidance. Localization quality in this laboratory's specific environment must be characterized during integration testing.

---

## 3. Navigation Reliability in Constrained Environments

### 3.1 BARN Challenge 2022: Highly Constrained Navigation

Xiao et al. [6] report results from the BARN Challenge at ICRA 2022, a benchmark for autonomous ground navigation in highly constrained environments with randomly generated obstacle configurations. The challenge evaluated multiple navigation systems — including Nav2-based approaches and learned policies — in both Gazebo simulation and physical obstacle courses, measuring success rate, collision events, and completion time.

**Design relevance to this project**: The BARN Challenge results directly motivate the safety-versus-efficiency design debate (Slide 10): some navigation systems failed in constrained spaces even with high simulation success rates; collision rates and completion times varied substantially across methods. This motivates the decision to fix Nav2 parameters after offline empirical verification rather than tuning for speed, and to measure arrival rate, collision events, and completion time rather than relying on a single composite metric.

**Evidence boundary** (Slide A02): BARN used a specific set of randomly generated obstacle courses and a Clearpath Jackal platform. Results — including success rates and rankings — cannot be directly applied to the TurtleBot3 Burger in this laboratory's specific environment. Footprint-to-gap ratios, sensor configurations, and environment layouts differ.

### 3.2 NavBench: Unified RL Navigation Benchmark

El-Hariry et al. [7] present NavBench, a unified benchmarking framework for reinforcement learning-based navigation across multiple robot platforms and environments. NavBench demonstrates RL policy transfer across simulation and physical platforms under a shared evaluation protocol.

**Design relevance to this project**: NavBench's cross-platform evaluation methodology supports the value of unified benchmarks (Core Concept 5: Reproducible Reliability Evaluation). It also provides evidence for the viability of end-to-end learned navigation in Research Debate I, motivating the team's decision to acknowledge the alternative rather than dismiss it.

**Evidence boundary**: NavBench evaluates RL policies on platforms and tasks different from the TurtleBot3 Burger running Nav2 in this laboratory. Its results cannot be used to claim RL navigation superiority or inferiority on the Burger.

### 3.3 BARN Challenge 2026

Xiao and the BARN organizers [12] describe the ongoing BARN Challenge 2026, which extends the benchmark with updated obstacle configurations and evaluation protocols. The challenge continues to provide a shared framework for comparing classical, learning-based, and hybrid navigation approaches.

**Design relevance to this project**: The continued BARN Challenge reflects the unresolved nature of Research Debate I (modular vs. end-to-end navigation): no single approach has claimed universally superior performance across all conditions, reinforcing the decision not to make sweeping comparative claims in this project.

---

## 4. Research Debates and Design Positioning

The following three debates, documented in `docs/research-tradeoffs.md`, directly shaped the design decisions in this project. They are reproduced here in summary form for the academic reader.

### 4.1 Modular vs. End-to-End Navigation

Modular navigation (Nav2 [4]) provides separation of localization, planning, and control, enabling component-specific debugging and recovery. End-to-end learned navigation (NavBench [7], BARN [6]) offers potential generalization from data but faces challenges in explainability and constrained-space reliability.

**Positioning**: This project uses Nav2 as a fixed baseline, treating the agent (not the navigation method) as the independent variable. This choice allows the evaluation to attribute performance differences to agent-level features, not navigation tuning.

### 4.2 Map-Based vs. Mapless Navigation

Map-based navigation (Nav2 + SLAM Toolbox [4][5]) enables global path planning and named-landmark destinations. Mapless local navigation reduces pre-mapping requirements but loses global destination reasoning.

**Positioning**: The project uses a pre-built map with a registered landmark registry. This is required for the named-landmark navigation task and for pre-dispatch path feasibility checking.

### 4.3 Safety vs. Efficiency

Conservative Nav2 parameters (larger inflation radius, lower velocity limits) increase tolerance for localization error but may render narrow passages geometrically impassable. Aggressive settings improve completion time but increase collision risk in constrained environments [6][9].

**Positioning**: Parameters are fixed at empirically verified values. The trade-off is characterized by measuring both arrival rate and completion time, not by selecting one at the expense of the other.

---

## 5. Research Gap and Positioning

The existing literature provides strong foundations in three areas:
1. LLM tool-use and grounding [1][2][3]: demonstrated the feasibility of connecting language reasoning to robot actions.
2. Modular navigation with Nav2 and SLAM Toolbox [4][5]: provides the navigation infrastructure.
3. Navigation reliability evaluation in constrained environments [6][7][12]: motivates rigorous multi-condition evaluation with failure preservation.

None of the reviewed works, however, address the specific combination of:
- Chinese-language task parsing with landmark disambiguation for a physical TurtleBot3 Burger.
- Pre-dispatch robot-state and path feasibility validation as a system requirement.
- Evidence-grounded mission reporting tied to Nav2 action results.
- Controlled baseline comparison (B0, B1, B2) with ablations, holding Nav2 constant, to measure the incremental contribution of agent-level features.

This project is positioned in the integration gap: it does not claim to advance the state of the art in LLM reasoning or in navigation algorithms, but proposes to evaluate whether the combination of these components — in a specific, well-defined configuration — improves task reliability and reporting accuracy in a physical indoor robot navigation context.

> **Fabrication policy**: All citations in this document are based on sources listed in the original proposal (Slides 34–35). No citations have been fabricated or inferred. Where claims require additional evidence not available in the reviewed literature, this is noted as [EVIDENCE NEEDED].

---

## References

| ID | Citation |
|----|----------|
| [1] | Yao, S., et al. (2023). ReAct: Synergizing Reasoning and Acting in Language Models. *arXiv preprint arXiv:2210.03629*. https://arxiv.org/abs/2210.03629 |
| [2] | Ahn, M., et al. (2022). Do As I Can, Not As I Say: Grounding Language in Robotic Affordances. *arXiv preprint arXiv:2204.01691*. https://arxiv.org/abs/2204.01691 |
| [3] | Pollini, D., Guterres, B. V., Guerra, R. S., & Grando, R. B. (2025). Reducing Latency in LLM-Based Natural Language Commands Processing for Robot Navigation. *arXiv preprint arXiv:2506.00075*. https://arxiv.org/abs/2506.00075 |
| [4] | Macenski, S., Martín, F., White, R., & Clavero, J. G. (2020). The Marathon 2: A Navigation System. *arXiv preprint arXiv:2003.00368*. https://arxiv.org/abs/2003.00368 |
| [5] | Macenski, S., & Jambrecic, I. (2021). SLAM Toolbox: SLAM for the dynamic world. *Journal of Open Source Software*, 6(61), 2783. https://joss.theoj.org/papers/10.21105/joss.02783 |
| [6] | Xiao, X., et al. (2022). Autonomous Ground Navigation in Highly Constrained Spaces: Lessons Learned from the BARN Challenge at ICRA 2022. *arXiv preprint arXiv:2208.10473*. https://arxiv.org/abs/2208.10473 |
| [7] | El-Hariry, M., et al. (2025). NavBench: A Unified Robotics Benchmark for Reinforcement Learning-Based Autonomous Navigation. *arXiv preprint arXiv:2505.14526*. https://arxiv.org/abs/2505.14526 |
| [8] | ROBOTIS. (n.d.). TurtleBot3 Features and Navigation [Official documentation]. https://emanual.robotis.com/docs/en/platform/turtlebot3/features/ |
| [9] | ROS Navigation maintainers. (n.d.). Nav2 Tuning Guide [Official documentation]. https://docs.nav2.org/jazzy/configuration_and_development/tuning_guide/ |
| [10] | ROS Navigation maintainers. (n.d.). Nav2 Simple Commander API [Official documentation]. https://docs.nav2.org/jazzy/configuration_and_development/simple_commander_api/simple_commander_api/ |
| [11] | ROS Navigation maintainers. (n.d.). Nav2 Collision Monitor [Official repository]. https://github.com/ros-navigation/navigation2/blob/main/nav2_collision_monitor/README.md |
| [12] | Xiao, X., and BARN organizers. (2026). The BARN Challenge 2026. https://people.cs.gmu.edu/~xiao/Research/BARN_Challenge/BARN_Challenge26.html |
