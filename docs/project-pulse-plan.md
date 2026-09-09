# Project Pulse implementation plan

## Goal and repository context

Build the lightweight, static Project Pulse contributor dashboard described in
`.github/project-pulse-brief.md`. The repository currently documents the agent
roles in `docs/agent-team.md` and has no existing dashboard implementation to
extend. The completed app must be served from `app/`, present the dashboard
rather than a directory listing, and use local JSON project data.

The dashboard's first view should make the project name, owner, status, recent
activity, and priority/risk understandable at a glance. It must use the exact
visible title **Project Pulse**, a card-based responsive layout, readable
status and priority treatments, and accessible semantic markup.

## File ownership and deliverables

The Orchestrator assigns exclusive write ownership as follows. Agents may read
the other files to confirm the shared interface, but must not edit files outside
their assignment.

| Owner | Assigned file(s) | Deliverable |
| --- | --- | --- |
| Designer | `app/styles.css` | Polished, responsive, accessible presentation for the agreed HTML hooks, including `.dashboard` and `.project-card`. |
| Coder | `app/index.html` | Semantic dashboard document, stylesheet and JSON references, and deterministic client-side rendering of visible project cards from the JSON data. |
| Coder | `app/project-data.json` | Valid static fixture with a top-level `projects` array and multiple complete project records. |
| Coder | `.vscode/launch.json` | Strict JSON VS Code launch configuration named `Run Project Pulse Dashboard` that runs the prescribed local server from `app/` and opens `index.html`. |
| Orchestrator | No implementation file | Establishes the interface below, delegates non-overlapping work, reviews the integrated result, and reports outcomes/blockers. |

No agent should change `README.md`, existing documentation, `.vscode/tasks.json`,
agent definitions, or exercise workflow files for this task.

## Shared implementation contract

Before delegation, the Orchestrator provides these fixed contracts to both
specialists:

- `app/project-data.json` is an object with a top-level `projects` array. Each
  object has non-empty string values for `name`, `owner`, `status`,
  `recentActivity`, and `priority`.
- `app/index.html` loads `styles.css` and fetches or otherwise explicitly loads
  `project-data.json`; it creates one visible card per project using the exact
  `project-card` class. The card displays all five fields, particularly
  `status`, `recentActivity`, and `priority`.
- HTML supplies stable structural hooks: `.dashboard` on the dashboard
  container, a project-card collection element, and field-specific classes or
  data attributes for name, owner, status, activity, and priority. These allow
  the stylesheet to work without requiring a later HTML edit.
- The markup uses a document language, viewport metadata, a single `h1`,
  landmark elements where appropriate, headings in order, and real text rather
  than color alone for status and priority. Dynamic rendered content must remain
  readable to assistive technologies.
- The page has a clear loading state and an explicit, human-readable error
  message if the JSON fetch fails or the expected `projects` array is absent.
  Rendering must not inject data with `innerHTML`; create elements and set
  `textContent` so fixture values cannot become markup.
- `.vscode/launch.json` is strict JSON without comments. Its configuration is
  named exactly `Run Project Pulse Dashboard`, has
  `"cwd": "${workspaceFolder}/app"`, runs
  `python3 -m http.server 5500`, and uses `serverReadyAction` to open
  `http://localhost:%s/index.html`. This server is required because browser
  `fetch` of the JSON should not be tested from a `file://` URL.

## Work phases, dependencies, and scheduling

### Phase 1 — establish the contract (sequential)

1. The Orchestrator confirms the file ownership table and shares the contract
   above with Designer and Coder.
2. The Orchestrator confirms that Coder will create all three absent app data,
   markup, and runtime files while Designer owns only the stylesheet.

This short phase is **sequential** because the agents need stable selector
names, field names, and launch requirements before they can work without
overlap.

### Phase 2 — specialist implementation (parallel)

After Phase 1, run these assignments **in parallel**:

1. **Designer:** create `app/styles.css`.
   - Implement a polished mobile-first dashboard: a contained `.dashboard`,
     responsive card grid/list, and clearly elevated `.project-card` elements.
   - Include readable typography, spacing, `border-radius`, `box-shadow`,
     visible status badges, and priority/risk emphasis that does not rely only
     on color.
   - Provide keyboard-visible focus styling for any interactive element and
     preserve sufficient foreground/background contrast.
   - Test narrow and wide viewports so cards do not create horizontal scrolling
     or clip field values.
2. **Coder:** create `app/project-data.json`, `app/index.html`, and
   `.vscode/launch.json`.
   - Supply several deterministic, varied project records so status and
     priority styling can be inspected.
   - Build the semantic document and small, explicit data-loading/rendering
     logic described in the contract; avoid framework and package dependencies.
   - Configure the specified VS Code launch command, working directory, and
     browser URL.

This work is safe to run **in parallel**: no assigned file has two writers, and
the selectors/data schema were frozen in Phase 1. The Designer's styling does
not depend on Coder's final card contents, while Coder's HTML uses the already
agreed deterministic hooks.

### Phase 3 — integration and review (sequential)

Once both specialists report completion, the Orchestrator reviews the four
files together. It confirms the HTML hook names match the stylesheet, card
rendering follows the JSON schema, and the launch configuration serves the
same directory the HTML expects. Integration is **sequential** because it
requires the final outputs of both parallel assignments and may reveal an
interface mismatch that needs routing back to the file owner.

## Dependencies

- **Runtime:** Python 3 available as `python3` for
  `python3 -m http.server 5500`; a VS Code/Codespaces environment for the
  launch configuration; and a modern browser with Fetch API and JavaScript
  enabled.
- **Implementation:** no npm packages, build tools, frameworks, or external
  network assets are required. Relative `styles.css` and `project-data.json`
  paths keep the preview self-contained.
- **Information:** the field schema and dashboard requirements in
  `.github/project-pulse-brief.md`, plus the role boundaries in
  `docs/agent-team.md`.
- **Ordering:** Coder's data file must be available before meaningful runtime
  rendering can succeed; Designer's visual work may proceed concurrently after
  the shared contract is established; the browser preview waits for all files
  and launch configuration to be complete.

## Validation and acceptance checks

### Static checks

The implementation owner and Orchestrator should verify all of the following:

1. `python3 -m json.tool app/project-data.json` succeeds; the parsed object has
   `projects` as an array with multiple records, and every record has non-empty
   `name`, `owner`, `status`, `recentActivity`, and `priority` strings.
2. `python3 -m json.tool .vscode/launch.json` succeeds. Confirm the exact
   configuration name, app working directory, `python3 -m http.server 5500`
   command, `serverReadyAction`, and
   `http://localhost:%s/index.html` target are present.
3. Inspect `app/index.html` to confirm the exact `Project Pulse` title,
   stylesheet reference, JSON reference, `.dashboard`, and rendered
   `project-card` usage. Confirm it displays the required field values and has
   no external package requirement.
4. Inspect `app/styles.css` for `.dashboard`, `.project-card`,
   `border-radius`, `box-shadow`, responsive rules, readable text contrast,
   and a non-color-only priority/status distinction.

### Runtime checks

1. Start the configured **Run Project Pulse Dashboard** launch configuration,
   or from `app/` run `python3 -m http.server 5500`.
2. Open `http://localhost:5500/index.html`; do not accept a directory listing
   as the preview result.
3. Confirm the browser renders one visible `.project-card` for every JSON
   project and each card shows its name, owner, status, recent activity, and
   priority.
4. Confirm browser developer tools show no failed resource requests or
   uncaught JavaScript errors. Temporarily testing an unavailable/malformed
   data response should show the planned explicit error state rather than a
   blank dashboard.
5. Check a narrow mobile viewport and a desktop viewport: cards reflow,
   labels remain readable, and no essential content is hidden or horizontally
   clipped. Stop the preview server after testing.

### Accessibility checks

1. Verify `html[lang]`, meaningful page title, one `h1`, logical heading
   order, and landmark structure.
2. Keyboard-test any focusable UI: focus is visible and follows a sensible
   order. The initial static dashboard need not add controls merely for
   decoration.
3. Check status and priority can be understood from their text labels without
   interpreting color, and that text/background combinations meet at least
   WCAG AA normal-text contrast where applicable.
4. With a screen reader or accessibility-tree inspection, confirm the rendered
   card information is exposed as understandable text in a logical order and
   that loading/error messages are announced or clearly discoverable.

## Completion criteria

The Orchestrator reports completion only after the static, runtime, and
accessibility checks above pass, all four assigned files are present, and no
files outside the stated scope were modified. Any failed JSON load, selector
contract mismatch, unavailable Python runtime, or browser-only accessibility
issue is reported as a blocker with the responsible file owner; the learner
retains all staging, commit, and push decisions.
