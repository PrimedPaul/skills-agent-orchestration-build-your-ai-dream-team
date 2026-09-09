# Agent team

Mona's Project Pulse dashboard will be built by a specialized agent team
orchestrated through the GitHub Copilot CLI in this Codespace. The
Orchestrator turns the request into coordinated phases, using the Planner
first and then assigning non-overlapping implementation and design work to
the appropriate specialists.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Coordinates the team, converts the plan into dependency-aware phases, delegates explicit file scopes, integrates the work, and reports blockers and outcomes. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository and relevant documentation, identifies risks and edge cases, and produces the ordered implementation plan, file assignments, dependencies, validation expectations, and open questions. | `.github/agents/planner.agent.md` |
| Designer | Gemini 3.1 Pro (copilot) | Shapes the dashboard's UI/UX, accessibility, information hierarchy, responsive behavior, and visual polish, including project cards, status badges, priority treatment, and deterministic CSS hooks. | `.github/agents/designer.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements scoped application logic and supporting runnable-app configuration with explicit errors, predictable structure, and testable behavior; validates its assigned changes before reporting back. | `.github/agents/coder.agent.md` |

All agents leave staging, committing, and pushing under the learner's control
through Copilot CLI prompts.
