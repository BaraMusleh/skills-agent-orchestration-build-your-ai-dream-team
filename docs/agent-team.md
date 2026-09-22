# Project Pulse agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate four custom agents:

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 | Coordinates the request, delegates scoped work to specialists, sequences dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 | Researches the repository and produces implementation phases, file assignments, dependencies, edge cases, and validation expectations. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 | Implements the static application and required launch support within its assigned file scope, then validates the result. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.1 Pro | Defines and implements the dashboard's accessible information hierarchy, interaction flow, responsive layout, and visual design. | `.github/agents/designer.agent.md` |

## How the team will build Project Pulse

The Orchestrator will first ask the Planner for a repository-informed plan. It will turn that plan into phases with explicit file ownership and dependencies. The Coder and Designer may work in parallel only when their assigned files do not overlap and neither task depends on the other's output; otherwise, the Orchestrator will run their work sequentially. After each phase, the Orchestrator will integrate and verify the results, surface blockers, and report the completed Project Pulse dashboard without allowing specialist agents to stage, commit, or push changes.
