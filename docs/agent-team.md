# Agent team

To build Mona's Project Pulse dashboard, I'm using a four-agent custom team defined under `.github/agents/` and orchestrated with GitHub Copilot CLI running in a Codespace.

## Orchestrator
- **Model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Coordinates the Planner, Coder, and Designer agents. Breaks the request into phases, assigns explicit file scopes, decides what can run in parallel vs. sequentially, and reports the integrated result. Does not implement anything itself.
- **Definition:** `.github/agents/orchestrator.agent.md`

## Planner
- **Model:** Claude Opus 4.7 (copilot)
- **Responsibility:** Researches the repository and relevant docs/dependencies, identifies edge cases and risks, and produces an ordered implementation plan with file assignments, dependencies, and validation expectations. Does not write code.
- **Definition:** `.github/agents/planner.agent.md`

## Coder
- **Model:** GPT-5.5 (copilot)
- **Responsibility:** Implements code-oriented tasks within the file scope assigned by the Orchestrator — building the dashboard logic and, for Project Pulse, creating support files like `.vscode/launch.json` so the app is easy to run and preview.
- **Definition:** `.github/agents/coder.agent.md`

## Designer
- **Model:** Gemini 3.1 Pro (copilot)
- **Responsibility:** Owns UI/UX, accessibility, information architecture, and visual design within its assigned scope — for Project Pulse, producing a polished dashboard with project cards, status badges, priority treatment, and responsive layout.
- **Definition:** `.github/agents/designer.agent.md`

All four agents avoid staging, committing, or pushing changes — git operations remain under the learner's control via Copilot CLI prompts.
