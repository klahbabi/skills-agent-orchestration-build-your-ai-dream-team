# Repository instructions

## Project shape

This repository is a GitHub Codespaces exercise about orchestrating custom agents to build Mona's Project Pulse dashboard. The exercise specification is in `.github/outline/agent-orchestration-build-your-ai-dream-team-outline.md`; `.github/project-pulse-brief.md` defines the dashboard's expected features and files. Learner outputs are a plan and handoff under `docs/`, a static app under `app/`, and a VS Code launch configuration at `.vscode/launch.json`.

The learner journey is written in `.github/steps/1-step.md` through `4-step.md`. The matching GitHub Actions workflows validate each step's expected files and key phrases when changes are pushed to `main`, post feedback to the exercise issue, and enable the next step. `.github/workflows/0-start-exercise.yml` starts the issue-based exercise. Keep step instructions, workflow path filters, and grading checks aligned when changing the curriculum.

The Codespace setup lives in `.devcontainer/`: `postCreate.sh` verifies or installs GitHub Copilot CLI and creates the Step 1 starter document; `postStart.sh` launches Copilot CLI. `.vscode/tasks.json` opens that CLI in the editor terminal.

## Custom agent workflow

The prebuilt agents in `.github/agents/` have distinct scopes: Orchestrator coordinates but does not implement; Planner researches and plans but does not code; Designer owns assigned UX and visual design; Coder implements assigned logic and support configuration. The Orchestrator should ask Planner for a plan first, assign explicit file scopes, and sequence overlapping work. All agents leave staging, commits, and pushes to the learner.

For Project Pulse, preserve the static-app contract in `.github/project-pulse-brief.md`: `app/index.html`, `app/styles.css`, and `app/project-data.json` with a top-level `projects` array containing `name`, `owner`, `status`, `recentActivity`, and `priority`. The VS Code launch configuration is strict JSON, serves from `${workspaceFolder}/app`, and opens `index.html` rather than the directory listing.

## Build and validation

There is no package-based build, test, or lint command in the repository. Run the repository's validation checks with:

```bash
bash scripts/validate-exercise.sh
```

The validation script has no test-name selector. For a focused check, use the corresponding standalone command:

```bash
# Check one workflow's YAML syntax
ruby -e 'require "yaml"; YAML.load_file(ARGV.fetch(0))' .github/workflows/3-step.yml

# Check JSON syntax
python3 -m json.tool .devcontainer/devcontainer.json >/dev/null

# Check shell syntax
bash -n .devcontainer/postCreate.sh
```

Substitute the relevant workflow, JSON, or shell file when checking a different file. For learner-created dashboard data or launch configuration, validate JSON with `python3 -m json.tool app/project-data.json` or `python3 -m json.tool .vscode/launch.json`.

The exercise workflows primarily grade expected files and literal key phrases; they do not run or browser-test the dashboard. Keep required wording and paths consistent across step prompts, workflow checks, and the project brief.
