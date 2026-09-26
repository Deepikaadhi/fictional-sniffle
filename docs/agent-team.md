cl# Agent team

For Mona's Project Pulse dashboard, I will use a four-agent custom team defined under the repository's agent folder and orchestrated through GitHub Copilot CLI in a Codespace.

## 1. Planner
- Model: Claude Opus 4.7 (copilot)
- Responsibility: Research the repo, review documentation and constraints, identify edge cases, and produce a practical implementation plan with file assignments, dependency ordering, validation expectations, and open questions.
- Definition: .github/agents/planner.agent.md

## 2. Orchestrator
- Model: Claude Opus 4.7 (copilot)
- Responsibility: Break the plan into work phases, delegate tasks to specialist agents, manage sequencing and parallelism when safe, and verify that the final result fits together coherently.
- Definition: .github/agents/orchestrator.agent.md

## 3. Designer
- Model: Gemini 3.1 Pro (copilot)
- Responsibility: Focus on UI/UX, accessibility, information hierarchy, responsive behavior, and visual polish for the Project Pulse dashboard experience.
- Definition: .github/agents/designer.agent.md

## 4. Coder
- Model: GPT-5.5 (copilot)
- Responsibility: Implement the assigned code changes, fix bugs, add logic, and validate work within the file scope delegated by the orchestrator.
- Definition: .github/agents/coder.agent.md

This setup uses GitHub Copilot CLI in a Codespace to coordinate the workflow, keep responsibilities clear, and let each specialist contribute within a defined scope.
