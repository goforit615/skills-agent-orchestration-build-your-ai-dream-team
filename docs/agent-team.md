# Agent team

Mona's Project Pulse dashboard will be built by a four-agent team, orchestrated through the GitHub Copilot CLI in a Codespace. The learner retains control of all git operations.

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| Orchestrator | Claude Opus 4.7 (copilot) | Breaks work into dependency-aware phases, delegates explicit file scopes to specialists, coordinates integration, and reports the outcome without implementing directly. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository and relevant documentation, identifies risks and edge cases, and produces implementation steps, file assignments, dependencies, and validation expectations. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements dashboard code and logic within its assigned scope, makes errors explicit and behavior testable, and validates runnable application work when assigned. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.1 Pro (copilot) | Owns UI/UX direction, accessibility, information architecture, responsive behavior, and polished Project Pulse dashboard styling. | `.github/agents/designer.agent.md` |

The Orchestrator begins with the Planner's technical plan, then assigns non-overlapping implementation and design work to the Coder and Designer in parallel where dependencies allow. It integrates and verifies their results while the GitHub Copilot CLI in the Codespace remains the control point for orchestration.
