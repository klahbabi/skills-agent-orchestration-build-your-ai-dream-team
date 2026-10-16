# Agent team

I will use this custom agent team to build Mona's Project Pulse dashboard:

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the team, delegates scoped work to specialists, manages phases and dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository and requirements, then creates an implementation plan with file assignments, dependencies, edge cases, and validation expectations. | `.github/agents/planner.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Shapes the dashboard's user experience, accessibility, information hierarchy, responsive layout, and visual design. | `.github/agents/designer.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements application logic and assigned runnable-app support, following repository patterns and validating the changes. | `.github/agents/coder.agent.md` |

I am using GitHub Copilot CLI in a Codespace to orchestrate the work.
