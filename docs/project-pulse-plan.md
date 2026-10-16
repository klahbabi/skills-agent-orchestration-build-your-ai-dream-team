# Project Pulse implementation plan

## Summary

Build Mona's Project Pulse as a small, static dashboard that lets contributors scan active projects, owners, current status, recent activity, priority or risk, and a short contributor-friendly summary. Use plain HTML, CSS, and JSON with no build step or application framework. The app must open in its UI, not a server directory listing, through the VS Code **Run Project Pulse Dashboard** configuration.

The repository has no package-based build, test, or lint setup. Follow the static-app contract in `.github/project-pulse-brief.md`, the requirements and checks in `.github/steps/2-step.md` and `.github/steps/3-step.md`, and the file ownership and sequencing rules in `.github/agents/`.

## Roles and file ownership

- **Orchestrator** coordinates the work, gives each specialist the scoped assignments below, sequences work around the shared HTML/CSS contract, integrates the outputs, and reports validation. It does not implement the app.
- **Planner** researches requirements and dependencies and maintains this plan; it does not implement code.
- **Designer** owns `app/styles.css`. It defines the visual hierarchy, responsive card layout, status and priority treatments, readable spacing, and accessibility-related presentation. It coordinates the classes and states the HTML will use before implementation; it does not edit the other assigned files.
- **Coder** owns `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. It implements semantic, accessible markup and data rendering, supplies representative project data, and creates the strict-JSON preview configuration. It does not edit `app/styles.css`.

This file is the Planner's output and is not an app implementation assignment. Keep specialist scopes separate; the Orchestrator resolves interface details rather than allowing concurrent edits to the same file.

## Ordered implementation steps

| Step | Owner and assigned files | Work and completion conditions |
| --- | --- | --- |
| 1. Confirm the contract | Orchestrator; no app files | Review this plan and the brief. Agree with Designer and Coder on the markup/CSS class contract (`.dashboard`, `.project-card`, status and priority hooks) and the data schema before parallel implementation. |
| 2. Establish the project data | Coder; `app/project-data.json` | Create valid JSON with a top-level `projects` array. Include multiple representative projects, each with `name`, `owner`, `status`, `recentActivity`, and `priority`, plus a short contributor-friendly `summary` to meet the brief. Use readable sample values; avoid implying live integrations. |
| 3. Define the presentation | Designer; `app/styles.css` | Implement a polished, responsive dashboard and project-card presentation with clear hierarchy, readable spacing, status badges, priority/risk treatment, adequate contrast, visible focus states, and reduced-motion-safe behavior. Include the required `.dashboard` and `.project-card` selectors, rounded corners, and shadows. Confirm the class/state contract with Coder. |
| 4. Implement the page and preview configuration | Coder; `app/index.html`, `.vscode/launch.json` | Build a semantic page titled exactly **Project Pulse**, link `styles.css`, and load `project-data.json` to render visible `.project-card` elements. Show each project's name, owner, status, recent activity, priority, and summary. Create strict JSON with no comments for a **Run Project Pulse Dashboard** configuration, use `python3 -m http.server 5500`, set `cwd` to `${workspaceFolder}/app`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`. |
| 5. Integrate and validate | Orchestrator with Designer and Coder; assigned files only for necessary owner-specific fixes | Check that the agreed HTML hooks match the stylesheet, the data fields are all rendered, the launch path opens `index.html`, and the visual layout works at narrow and wide viewport sizes. Each specialist fixes issues only in their assigned files. |

## dependencies and work ordering

- Step 1 is a prerequisite for Steps 2–4: the page, stylesheet, and data must agree on field names, status/priority states, and CSS hooks.
- Once that contract is agreed, the Designer can build `app/styles.css` in parallel with the Coder preparing `app/project-data.json`; these files have separate owners and no data dependency beyond the agreed contract.
- The Coder can prepare `.vscode/launch.json` in parallel with those tasks because its command, working directory, and URL are specified. Verify it against the completed app during integration.
- The Coder should complete `app/index.html` after the data schema and Designer's class/state contract are known. This avoids HTML/CSS ownership conflicts and ensures the page can render every required field using the actual styling hooks.
- Step 5 must run after all four assigned outputs exist. Any fix touching CSS goes to Designer; any fix touching HTML, JSON data, or launch configuration goes to Coder. Re-run affected checks after fixes.

Thus independent data, design, and launch setup can proceed in parallel after the interface is agreed; page integration and end-to-end validation are sequential.

## Edge cases and risks

- **Data loading:** A browser opened directly with `file://` may block fetching JSON. Use the configured local HTTP server. In the page, provide a clear loading state and a helpful visible error if the request fails or JSON is invalid; do not leave an empty dashboard or unhandled rejection.
- **Empty or incomplete data:** Handle an empty `projects` array with an informative empty state. Do not assume optional summary text exists; provide a safe fallback or ensure the supplied sample data includes it.
- **Unexpected values and content:** Keep unknown status or priority values readable with a neutral fallback rather than relying on a known-color mapping. Long names, activity, or summaries must wrap without overflowing cards.
- **Responsive and accessible presentation:** Check keyboard focus, semantic headings and labels, sufficient text/background contrast, and narrow screens. Do not convey status or risk by color alone; respect reduced-motion preferences.
- **Preview behavior:** The server must run with `app/` as its working directory and the URL must end in `/index.html`; serving the repository root or opening only `/` can show a directory listing. Port 5500 may already be occupied, so stop an existing preview or report the conflict before interpreting a failed launch.
- **Scope:** This is a static sample dashboard, not a live project tracker. Do not add a framework, backend, build system, or unrequested repository changes.

## validation expectations

### Plan and exercise checks

- Confirm this file exists and includes Project Pulse, Designer, Coder, all four required file paths, dependencies, parallel work decisions, and validation expectations.
- The Step 2 workflow, `.github/workflows/2-step.yml`, checks for this file and those key phrases. Run `bash scripts/validate-exercise.sh` for repository-level exercise checks; it is not a substitute for the Step 2 workflow's plan-specific keyphrase checks.

### Implementation checks for the next phase

- `python3 -m json.tool app/project-data.json >/dev/null` and `python3 -m json.tool .vscode/launch.json >/dev/null` both succeed.
- Verify `app/project-data.json` has a top-level `projects` array and that every project has `name`, `owner`, `status`, `recentActivity`, and `priority`; include a contributor-friendly summary as required by the brief.
- Verify `app/index.html` has the exact title **Project Pulse**, references `styles.css` and `project-data.json`, and renders each project in a `project-card` with visible `status`, `recentActivity`, and `priority` values (as well as owner and summary).
- Verify `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`, and inspect responsive and accessibility behavior in the browser.
- Verify `.vscode/launch.json` parses as strict JSON and names the configuration exactly **Run Project Pulse Dashboard**. Confirm it uses `python3 -m http.server 5500`, serves from `${workspaceFolder}/app`, and opens `http://localhost:%s/index.html`.
- Run the launch configuration in VS Code and confirm the browser displays the dashboard rather than a directory listing. As a non-UI smoke check, serve from `app/` with `python3 -m http.server 5500` and request `http://localhost:5500/index.html`; stop the server afterward.
- The Step 3 workflow (`.github/workflows/3-step.yml`) checks file presence, required phrases, JSON syntax, and selected CSS/data/launch requirements. Its checks do not run or browser-test the app, so the preview check remains a manual validation responsibility.

## Open questions

No blocking questions. The brief does not specify real project names, sample owners, exact status/priority vocabularies, or a visual brand palette; use clear representative sample projects and a consistent, accessible visual system, without presenting sample data as live.
