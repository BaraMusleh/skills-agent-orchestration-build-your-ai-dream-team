# Project Pulse implementation plan

## Summary

Build a lightweight static Project Pulse dashboard that follows the repository's existing patterns:

- no framework or build tooling
- app files under `app/`
- documentation under `docs/`
- strict JSON for `.vscode/launch.json`
- a VS Code preview configuration named `Run Project Pulse Dashboard`

The Orchestrator should keep file ownership explicit so Designer and Coder do not conflict. Lock the UI and data contract first, run non-overlapping file work in parallel, integrate in `app/index.html`, and then validate the launch flow and rendered dashboard end to end.

## Responsibilities

### Designer

- Define the dashboard's visual hierarchy and project-card anatomy.
- Define stable CSS hooks, including `.dashboard` and `.project-card`.
- Make status and priority understandable without relying on color alone.
- Specify responsive behavior for narrow screens.
- Ensure accessible contrast, spacing, typography, and content hierarchy.
- Own `app/styles.css` and styling-only fixes.

### Coder

- Define and implement the project data schema.
- Implement data loading, error handling, empty states, and card rendering.
- Build the VS Code launch configuration and verify its server assumptions.
- Own `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`.

## Ordered implementation steps

### Step 1 - Confirm the implementation contract

**Owner:** Orchestrator, with Designer and Coder input

Before implementation:

- Designer defines the page hierarchy, card content, CSS hooks, responsive behavior, and accessibility expectations.
- Coder confirms that any required JavaScript will live in `app/index.html` and that data will be loaded from `app/project-data.json`.
- Coder confirms the launch behavior:
  - configuration name: `Run Project Pulse Dashboard`
  - working directory: `${workspaceFolder}/app`
  - server command: `python3 -m http.server 5500`
  - browser target: `http://localhost:%s/index.html`

Reserve files as follows:

- `app/index.html`: Coder
- `app/styles.css`: Designer
- `app/project-data.json`: Coder
- `.vscode/launch.json`: Coder

Designer must not edit `app/index.html`; Designer supplies the visual contract and Coder implements the markup.

### Step 2 - Implement non-overlapping files in parallel

#### Step 2A - Create dashboard styling

**Owner:** Designer  
**File:** `app/styles.css`

Create polished dashboard styling with:

- `.dashboard` and `.project-card` selectors
- card layout, spacing, rounded corners, and shadows
- status badges and priority treatments
- typography and content wrapping for long values
- responsive behavior for narrow viewports
- visible, non-color-only distinctions for status and priority

The CSS structure must match the contract agreed in Step 1.

#### Step 2B - Create project data and launch support

**Owner:** Coder  
**Files:** `app/project-data.json`, `.vscode/launch.json`

For `app/project-data.json`:

- use a top-level `projects` array
- seed 3 to 5 projects so the layout is meaningful
- give every project `name`, `owner`, `status`, `recentActivity`, and `priority`
- use valid, strict JSON

For `.vscode/launch.json`:

- use valid, strict JSON without comments
- add a configuration named `Run Project Pulse Dashboard`
- serve from `${workspaceFolder}/app`
- run `python3 -m http.server 5500`
- use `serverReadyAction` to open `http://localhost:%s/index.html`
- open the page directly rather than a directory listing

These tasks can run concurrently with Step 2A because their file scopes do not overlap.

### Step 3 - Integrate the dashboard page

**Owner:** Coder  
**File:** `app/index.html`  
**Depends on:** Steps 2A and 2B

Implement the integrated page after the CSS hooks and JSON schema are stable:

- use the exact page title `Project Pulse`
- reference `styles.css`
- fetch `project-data.json`
- render a visible `.project-card` for every project
- show each project's name, owner, status, recent activity, and priority
- use accessible headings and meaningful section/card semantics
- show a clear empty state when `projects` is empty
- show a visible error state when the data cannot be loaded or parsed
- work correctly when served from `app/` by the Python HTTP server

Do not reopen `app/styles.css` or `app/project-data.json` during integration unless the Orchestrator explicitly starts a follow-up fix.

### Step 4 - Resolve integration gaps

**Owners:** Designer for styling issues; Coder for markup, data, or launch issues

- Designer may revise only `app/styles.css`.
- Coder may revise `app/index.html`, `app/project-data.json`, or `.vscode/launch.json`.
- Verify that real content fits the card design and wraps correctly.
- Correct responsive overflow, empty/error states, data-loading failures, or launch issues.

If both styling and markup must change, sequence the fixes:

1. Designer updates `app/styles.css`.
2. Coder makes final `app/index.html` adjustments.

This preserves ownership boundaries and avoids concurrent edits to a shared visual contract.

### Step 5 - Validate the finished dashboard

**Owner:** Orchestrator reviewing Designer and Coder outputs

Review only the four implementation files, run the parsing and launch checks below, and confirm that the dashboard is polished and usable rather than a raw prototype.

## File assignments

| Step | Owner | Files |
| --- | --- | --- |
| Step 1 | Orchestrator with Designer and Coder | No implementation changes; define contract and ownership |
| Step 2A | Designer | `app/styles.css` |
| Step 2B | Coder | `app/project-data.json`, `.vscode/launch.json` |
| Step 3 | Coder | `app/index.html` |
| Step 4 | Designer or Coder, based on issue type | Designer: `app/styles.css`; Coder: `app/index.html`, `app/project-data.json`, `.vscode/launch.json` |
| Step 5 | Orchestrator | Validation review only |

## Dependencies

1. Step 1 must finish before implementation begins.
2. Steps 2A and 2B depend on the UI/data contract from Step 1.
3. Step 3 depends on both the Designer's stable CSS hooks and the Coder's stable JSON schema.
4. Step 4 depends on a working integrated page from Step 3.
5. Step 5 depends on all integration fixes being complete.

## Parallel and sequential work decisions

### Parallel work

After Step 1:

- Designer can build `app/styles.css`.
- Coder can build `app/project-data.json`.
- Coder can build `.vscode/launch.json`.

These files do not overlap, so this work can proceed concurrently without merge conflicts.

### Sequential work

- Do not start file implementation until the contract is agreed.
- Wait to implement `app/index.html` until the stylesheet hooks and JSON schema are stable.
- If styling and markup both require fixes, complete styling changes first and markup adjustments second.
- Run final validation only after every implementation file is stable.

No two agents should edit the same file concurrently.

## Edge cases

- Opening `app/index.html` through `file://` will prevent normal JSON fetching; use the HTTP launch flow.
- Malformed or unavailable JSON must produce a visible error instead of a blank page.
- An empty `projects` array must produce an empty-state message.
- Long project names, owner names, and recent-activity text must wrap without breaking cards.
- Unknown status or priority values must degrade gracefully.
- Status and priority must not depend on color alone.
- The launch configuration must open `index.html`, not the server's directory listing.
- Because the repository has no established `.vscode/launch.json` pattern, validate the chosen launch type in Codespaces.

## Validation expectations

### File and content checks

- `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` exist.
- `app/index.html` contains the exact title `Project Pulse`.
- `app/index.html` references `styles.css` and fetches `project-data.json`.
- Rendered cards use the `project-card` class and display all required fields.
- `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.

### Parsing checks

Run:

```sh
python3 -m json.tool app/project-data.json
python3 -m json.tool .vscode/launch.json
```

Confirm:

- both files parse as strict JSON
- `app/project-data.json` has a top-level `projects` array
- every project has `name`, `owner`, `status`, `recentActivity`, and `priority`

### Launch checks

- `.vscode/launch.json` contains `Run Project Pulse Dashboard`.
- The configuration serves `${workspaceFolder}/app`.
- It runs `python3 -m http.server 5500`.
- `serverReadyAction` opens `http://localhost:%s/index.html`.
- Running the configuration opens the dashboard rather than a directory listing.

### Visual, behavioral, and accessibility checks

- Multiple project cards are visible.
- Cards have clear spacing, rounded corners, shadows, and readable typography.
- The layout remains readable at a narrow viewport.
- Long values wrap without overflow.
- Empty and data-load failure states are visible and understandable.
- Heading order and card structure remain meaningful without CSS.
- Status and priority remain understandable without color.
