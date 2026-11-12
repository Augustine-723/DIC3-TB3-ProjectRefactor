# Research Trade-offs: Three Design Debates

> **Source**: TB3_Proposal_Final_35slides.pptx — Slides 8–10 (Section 03 · Research Debates), Appendix Slide A03 (Slide 24).
> **Note**: The positions summarized here reflect the team's reading of the cited literature. They are not represented as the unanimous view of the robotics research community. Where the literature genuinely leaves questions unresolved, those questions are preserved as stated.

---

## Debate I — Modular vs. End-to-End Navigation

**Source**: Slide 8 (Original Slide 8; evidence in slide notes)

### Perspective A — Modular Navigation

Separating localization, planning, and control into distinct modules provides clear component boundaries that aid debugging and re-use. When a failure occurs, it can be traced to a specific module (e.g., localization divergence versus planner rejection versus controller overshoot). Nav2's behaviour-tree architecture exemplifies this approach; its Marathon 2 field trials document long-distance navigation with module-specific recovery behaviours [4].

**Supporting evidence**: Nav2's modular architecture has been validated in campus-scale navigation experiments [4]. SLAM Toolbox provides replaceable mapping and localization modules [5].

### Perspective B — End-to-End Navigation

Learning sensorimotor mappings directly from data may yield adaptive behaviours that modular pipelines cannot easily achieve. Reinforcement-learning navigation policies (e.g., as benchmarked in NavBench [7]) can generalize across environments without hand-tuned module boundaries.

**Supporting evidence**: NavBench [7] reports that RL-based policies can transfer across simulation and physical platforms. The BARN Challenge [6] provides a shared evaluation framework where both classical and learned methods are compared.

### Evidence and Unresolved Questions

Different platforms, task distributions, and evaluation conditions make direct comparison difficult. NavBench [7] and the BARN Challenge [6] provide shared frameworks that enable controlled comparison, but results from one platform or scenario cannot be assumed to generalize to the TurtleBot3 Burger in this laboratory. Unresolved questions include: generalization to unknown environments, failure-mode boundaries for learned policies, and low-cost physical validation.

A principled comparison requires the same tasks, computational resources, and test scenarios, reporting success, collision, and latency — this is the team's methodology position, not a surveyed consensus.

### Our Approach (Slide 8)

**Fixed Nav2; Agent as the research variable.**

Nav2 is held constant across all baselines (B0, B1, B2) to isolate the contribution of agent-level features (clarification, pre-check, evidence reporting) from the navigation stack itself. This design does not claim Nav2 is superior to end-to-end alternatives; it treats Nav2 as a controlled fixed factor while the agent design is the independent variable.

### Remaining Limitation

Fixing Nav2 means this study does not evaluate end-to-end navigation alternatives. Comparative claims about modular versus end-to-end navigation on the TurtleBot3 Burger are outside scope.

---

## Debate II — Map-Based vs. Mapless Navigation

**Source**: Slide 9 (Original Slide 9; evidence in slide notes)

### Perspective A — Map-Based Navigation

A pre-built map supports global path planning, enables named landmark destinations (via a landmark registry), and allows the robot to reason about destination reachability before dispatch. SLAM Toolbox provides the map construction and localization infrastructure [5]; Nav2's global planner operates on the occupancy grid [4].

**Supporting evidence**: SLAM Toolbox [5] and Nav2 [4] together provide a mature map-based navigation pipeline. Named-landmark registries are a natural extension of this architecture.

### Perspective B — Mapless Navigation

Local-observation-only navigation (e.g., reactive controllers or RL policies trained without global maps) reduces the dependency on pre-built maps and can handle environments where maps are unavailable or stale. BARN Challenge entries include mapless approaches [6].

**Supporting evidence**: BARN [6] includes evaluation of local-reactive navigation in constrained spaces. Some BARN entries succeed without global maps.

### Evidence and Unresolved Questions

Map-based navigation depends on map currency; a map that does not reflect added furniture or moved obstacles may produce path plans that fail on the physical robot. Mapless navigation faces challenges with dead-ends, ambiguous landmark identification, and lack of global destination reasoning. Neither approach fully resolves dynamic environment changes or multi-room navigation without additional infrastructure.

The team's reading of [4] and [5] grounds the map-based choice in practical availability and landmark-naming requirements. The choice does not claim map-based navigation is universally superior.

### Our Approach (Slide 9)

**Pre-built map + Landmark Registry + real-time Costmap.**

The pre-built occupancy grid is used for global planning and landmark coordinate resolution. The landmark registry maps Chinese-language destination names (and aliases) to registered poses in the map frame. The real-time costmap handles dynamic obstacles detected by the LiDAR during execution. This approach is chosen because: (a) the laboratory environment is a single, bounded indoor floor whose map can be pre-built and maintained; (b) landmark-naming is a core user-interface requirement; (c) global path feasibility checking before dispatch is a system safety requirement.

### Remaining Limitation

The system cannot navigate to unregistered destinations. Map changes (moved furniture, temporary obstacles) require map updates or rely on the real-time costmap for local avoidance. Dead-ends and dynamic obstacle handling remain dependent on Nav2's recovery behaviours.

---

## Debate III — Safety vs. Efficiency

**Source**: Slide 10 (Original Slide 10; evidence in slide notes)

### Perspective A — Conservative Safety Margins

Larger inflation radii around obstacles and lower velocity limits increase the robot's tolerance for localization error and sensor noise. A robot that maintains greater clearance is less likely to collide even when its position estimate drifts slightly.

**Supporting evidence**: Nav2 Tuning Guide [9] discusses inflation radius as a safety parameter. Conservative settings reduce collision risk in environments with narrow passages.

### Perspective B — Efficiency-Favoring Settings

Overly conservative inflation radii may render narrow passages geometrically impassable from the planner's perspective, even when the robot's physical footprint could traverse them. Excessive velocity limits increase task completion time and interaction latency. The BARN Challenge [6] illustrates that footprint-to-gap ratio and planning aggressiveness affect both success rate and completion time.

**Supporting evidence**: BARN Challenge results [6] show that overly conservative planners fail in highly constrained environments. Nav2 Tuning Guide [9] acknowledges the trade-off between inflation radius and passability.

### Evidence and Unresolved Questions

The optimal balance between safety margin and efficiency is environment-specific. For the TurtleBot3 Burger in this laboratory, the trade-off between footprint, inflation radius, and velocity has not been empirically characterized. The correct parameter values for this specific environment must be established through testing, not assumed from general guidance. Unresolved: what inflation radius values permit passage through the laboratory's narrowest doorways while maintaining acceptable collision rates?

### Our Approach (Slide 10)

**Fixed, empirically verified parameters; evaluate arrival rate, collision events, and completion time.**

Navigation parameters (footprint, inflation radius, velocity limits) will be fixed after offline verification in the laboratory environment. The same parameter set will be used across all baselines (B0, B1, B2) to ensure that observed differences in performance are attributable to agent features, not navigation tuning. The evaluation metrics (navigation success rate, collision events, task completion time) will characterize the operating point chosen, without claiming it is optimal.

### Remaining Limitation

A single operating point does not characterize the full safety–efficiency trade-off curve. Parameter tuning results for this laboratory may not transfer to other environments. Collision events during real-robot experiments will be logged and reported; they will not be suppressed or attributed to environmental factors without evidence.

---

## Summary Table

| Debate | Perspective A | Perspective B | Our Choice | Rationale |
|--------|--------------|--------------|------------|-----------|
| Modular vs. End-to-End | Nav2: modular, debuggable | RL: data-driven, adaptive | Fixed Nav2 | Isolate agent as the research variable |
| Map-Based vs. Mapless | Pre-built map: global planning, named landmarks | Local observation: map-free | Pre-built map + Landmark Registry + Costmap | Named-landmark requirement; controlled environment |
| Safety vs. Efficiency | Conservative margins: collision-tolerant | Aggressive settings: passable, faster | Fixed verified parameters | Controlled comparison; measure both metrics |

---

## References

| ID | Citation |
|----|----------|
| [4] | Macenski, S., et al. (2020). The Marathon 2: A Navigation System. https://arxiv.org/abs/2003.00368 |
| [5] | Macenski, S., & Jambrecic, I. (2021). SLAM Toolbox: SLAM for the dynamic world. https://joss.theoj.org/papers/10.21105/joss.02783 |
| [6] | Xiao, X., et al. (2022). Autonomous Ground Navigation in Highly Constrained Spaces: BARN Challenge. https://arxiv.org/abs/2208.10473 |
| [7] | El-Hariry, M., et al. (2025). NavBench: A Unified Robotics Benchmark for RL-Based Autonomous Navigation. https://arxiv.org/abs/2505.14526 |
| [9] | ROS Navigation maintainers. Nav2 Tuning Guide. https://docs.nav2.org/jazzy/configuration_and_development/tuning_guide/ |
| [12] | Xiao, X., and BARN organizers (2026). The BARN Challenge 2026. https://people.cs.gmu.edu/~xiao/Research/BARN_Challenge/BARN_Challenge26.html |
