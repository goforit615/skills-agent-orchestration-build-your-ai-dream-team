# Project Pulse Implementation Plan

## Summary

Build Mona's Project Pulse as a static, contributor-friendly dashboard that shows project name, owner, status, recent activity, and priority. GitHub Copilot CLI in the Codespace orchestrates the work: the Orchestrator uses this plan to coordinate the Planner, Designer, and Coder without overlapping file ownership.

## Agent responsibilities and file assignments

| Agent | Responsibility | Assigned files |
|---|---|---|
| Designer | Define the responsive visual system, accessible information hierarchy, card layout, status badges, and priority treatment. Provide the CSS hooks that the HTML uses. | `app/styles.css` |
| Coder | Define the data, implement data loading and semantic dashboard markup, and configure a predictable local preview. Follow the Designer's agreed CSS hooks. | `app/project-data.json`, `app/index.html`, `.vscode/launch.json` |
| Orchestrator | Sequence dependencies, ensure the Designer and Coder do not edit the same file, integrate the work, and verify the dashboard. | No implementation files |

## Ordered implementation steps

1. **Create the project data — Coder**
   - Create `app/project-data.json` with a top-level `projects` array.
   - Every project must provide `name`, `owner`, `status`, `recentActivity`, and `priority`.
   - Use several representative projects with varied statuses and priority levels so the dashboard and status styling are meaningful.

2. **Agree the display contract — Designer and Coder**
   - The Designer defines the class names and visual treatment for the dashboard, cards, status badges, and priority indicators.
   - The contract must include `.dashboard` and `.project-card`; the Coder uses these exact hooks in the markup.
   - Keep colors, typography, spacing, contrast, and responsive behavior accessible and easy to scan.

3. **Implement dashboard presentation — parallel work after the display contract**
   - **Designer:** create `app/styles.css` with a responsive card layout, `.dashboard`, `.project-card`, readable status/priority treatment, `border-radius`, and `box-shadow`.
   - **Coder:** create `app/index.html` with a visible `Project Pulse` heading, a stylesheet link to `styles.css`, and JavaScript that fetches `project-data.json` and renders one project card per data item. Cards must expose the status, recent activity, and priority values in the DOM.

4. **Add the local launch configuration — Coder**
   - Create `.vscode/launch.json` only after `app/index.html` exists.
   - Use strict JSON with no comments.
   - Add a configuration named `Run Project Pulse Dashboard` that serves `${workspaceFolder}/app` with `python3 -m http.server 5500`.
   - Configure `serverReadyAction` to open `http://localhost:%s/index.html`, avoiding a directory listing.

5. **Integrate and validate — Orchestrator**
   - Confirm the data field names, HTML rendering logic, and CSS selectors agree.
   - Launch the dashboard and confirm it opens directly to `index.html`, renders all project cards, and presents a polished, responsive first view.

## Dependencies

| Work | Depends on | Reason |
|---|---|---|
| `app/project-data.json` | None | Establishes the canonical project fields and values. |
| Display contract | `app/project-data.json` | Status and priority vocabulary informs badge treatment. |
| `app/styles.css` | Display contract | CSS selectors and visual rules must be agreed first. |
| `app/index.html` | `app/project-data.json`, display contract | Rendering uses the exact data fields and CSS hooks. |
| `.vscode/launch.json` | `app/index.html` | The preview target must already exist for end-to-end validation. |
| Integration validation | All assigned files | Validates the complete experience rather than isolated files. |

## Parallel work decisions

- **Sequential:** Create `app/project-data.json` first, then agree the display contract. Both establish interfaces used by later work.
- **Parallel:** Once the contract is agreed, the Designer can implement `app/styles.css` while the Coder implements `app/index.html`; their file scopes do not overlap.
- **Sequential:** Create `.vscode/launch.json` after the HTML is in place, then run integration validation after all files are complete.

## Edge cases and risks

- Opening `index.html` as `file://` prevents reliable `fetch` behavior; always preview through the configured HTTP server.
- `recentActivity` must use the same camelCase spelling in JSON and rendering code.
- VS Code accepts JSONC, but automated JSON parsing does not; `launch.json` must remain strict JSON without comments or trailing commas.
- The Coder and Designer must keep their file boundaries; communicate class names rather than editing each other's files.
- If port 5500 is unavailable, update the server command and browser URL together.

## Validation expectations

1. `python3 -m json.tool app/project-data.json` succeeds, and the file has a `projects` array with the required fields on every item.
2. `app/index.html` contains `Project Pulse`, links `styles.css`, references `project-data.json`, and renders `.project-card` elements containing status, recent activity, and priority.
3. `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`, with accessible contrast and responsive card behavior.
4. `python3 -m json.tool .vscode/launch.json` succeeds. The configuration is named `Run Project Pulse Dashboard`, uses `${workspaceFolder}/app`, and opens `index.html`.
5. Running the launch configuration starts the server, opens the dashboard rather than a directory listing, displays project cards with all required information, and produces no browser-console fetch or JSON errors.
