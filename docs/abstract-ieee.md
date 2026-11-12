# Abstract — IEEE Style

> **Change**: tb3-proposal-refactor
> **Source slides**: 2–4, 17–21 (TB3_Proposal_Final_35slides.pptx)
> **Format**: IEEE conference paper abstract style (target ≤ 250 words)
> **Status**: This abstract describes a **research proposal**. No experimental results have been obtained.

---

## Abstract

Language-controlled mobile robot navigation requires a system that correctly interprets user commands, validates whether navigation is feasible under current robot conditions, executes goals reliably, and reports outcomes in a manner grounded in observable evidence. Existing integrations of large language models (LLMs) with ROS 2 navigation stacks address language parsing and latency but do not systematically evaluate the incremental contribution of task clarification, pre-dispatch robot-state validation, and evidence-grounded reporting on a physical platform. This paper presents a research proposal for a validation-aware AI agent operating on a TurtleBot3 Burger running Ubuntu 22.04, ROS 2 Humble, and Nav2. The agent accepts Chinese-language task commands, parses intent using an LLM constrained by a JSON schema and landmark allowlist, performs pre-dispatch checks of sensor freshness, localization validity, and path feasibility, dispatches validated goals to Nav2, and delivers reports derived exclusively from Nav2 action results and an event log. To isolate the agent's contribution from the underlying navigation stack, three baseline conditions are defined: B0 (fixed menu, Nav2 only), B1 (LLM and schema, no clarification or pre-check), and B2 (full agent). Evaluation is planned across 60 Chinese-language task cases, 40 simulation runs per method, and 12 paired real-robot cases per method, with two ablation variants. Research questions address whether clarification and state validation reduce improper dispatch (RQ1), whether evidence-grounded reporting improves accuracy (RQ2), and what interaction latency the full agent introduces (RQ3). All experimental outcomes reported here are planned; no robot experiments have been conducted.

*(Word count: approximately 230)*

---

## Keywords

indoor navigation, language-controlled robotics, ROS 2, Nav2, TurtleBot3, large language models, task clarification, evidence-grounded reporting, reliability evaluation
