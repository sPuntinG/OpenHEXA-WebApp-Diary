# SNT Orchestrator — Jira Items (review sheet)

> **Purpose:** the full text of every Jira issue **before** it's created, so Giulia can revise
> wording here first. Once approved, the agent creates them in project **SNT25** under Epic
> **SNT25-536** and keeps this sheet in sync (keys filled in, status updated).
> Source of the tasks: [PLAN.md](PLAN.md).

## Resuming this task (runbook)

A fresh session can continue creating these issues from a cold start by reading this file.

**Coordinates**

- Site: `bluesquare.atlassian.net`. Get the cloudId at runtime with
  `getAccessibleAtlassianResources` (do not hardcode it here — public repo).
- Project: **SNT25** (id 15083) · Epic: **SNT25-536**.
- Tools: the **Atlassian MCP** (claude.ai connector). Load schemas via `ToolSearch`
  (`createJiraIssue`, `createIssueLink`, `editJiraIssue`, `getTransitionsForJiraIssue`,
  `transitionJiraIssue`, `searchJiraIssuesUsingJql`). If the connector isn't available, the
  user runs `/mcp` → "claude.ai Atlassian" to authenticate.

**Before creating anything — avoid duplicates**

- Run `searchJiraIssuesUsingJql`: `project = SNT25 AND labels = snt-orchestrator`. Match
  existing issues by the `T-x` prefix in the summary and **skip** any already present.
- Already created (skip): Epic **SNT25-536**; Stories **SNT25-537 / 547 / 548 / 549 / 550** (Phases 0–4); Phase 0 Tasks **SNT25-538, SNT25-540–546** (T0.0–T0.7). Phase 1–4 Tasks not yet created.

**Remaining steps (in order)**

1. **Patch SNT25-538** (`editJiraIssue`) to the cleaned **T0.0** description below — i.e. drop
   the old "Phase 0 · Gate — part of Epic… Story…" line, and use the reworded F1–F8 / open-questions text.
2. **Create the 4 remaining Stories** (Phase 1–4): type `Story`, `parent` = SNT25-536, labels
   `snt-orchestrator` + `phase:N`, description = the phase's Description + Exit criteria here.
3. **Create the 27 remaining Tasks**: type `Task`, `parent` = SNT25-536, summary with the
   `T-x` prefix, description + acceptance criteria from this sheet, labels `snt-orchestrator`
   - `phase:N` + the `owner:*` label(s). Keep the markdown checklist in T0.1's description.
4. **T0.6 and T0.7 → create as Done**: create the Task, then `getTransitionsForJiraIssue` to
   find the "Done" transition id and `transitionJiraIssue` (or pass `transition` at creation).
   All other tasks stay in the default **Backlog** (no transition needed).
5. **Phase links**: link each Task to its phase Story with a `Relates` link
   (`createIssueLink` inwardIssue = Task, outwardIssue = Story).
6. **Dependency links**: for each _Blocked by_ entry, create a `Blocks` link with
   **inwardIssue = the blocker, outwardIssue = the blocked task** (e.g. T0.2 blocked by T0.1 →
   inwardIssue T0.1, outwardIssue T0.2).
7. **Backfill** the real keys into the tables below (`_tbd_` → `SNT25-xxx`) and update statuses.

> Reminder: confirm with the user before bulk-creating (outward-facing). The user may still be
> revising the wording in this sheet — use whatever text is in the file at creation time.

## Conventions (how each issue is built)

- **Hierarchy:** one **Epic** → five phase **Stories** → atomic **Tasks**. Story and Task are
  the same Jira level, so each **Task is parented to the Epic** and tied to its phase by a
  `phase:N` label + a **"relates to"** link to the phase Story.
- **Owner** → `owner:*` labels (`owner:giulia`, `owner:agent`, `owner:pm`, `owner:oh-devs`).
  Shown in the tables as short names.
- **Dependencies** → **"is blocked by"** issue links between Tasks (the _Blocked by_ column).
- **Status:** all created in **Backlog** (project default); T0.6 / T0.7 created already **Done**.
- **Description** = the action. It deliberately omits owner / parent / dependencies — those are
  Jira fields, labels and links, not prose.
- **Acceptance criteria** = the task's _Done when_. Items marked _(proposed)_ were not spelled
  out in PLAN.md and are suggested here for review.
- Every Task summary keeps its `T-x` prefix so this sheet ↔ PLAN.md ↔ Jira stay cross-referable.

## Created in Jira

| Item                              | Key                                                            | Status                   |
| --------------------------------- | -------------------------------------------------------------- | ------------------------ |
| Epic — SNT Pipelines Orchestrator | [SNT25-536](https://bluesquare.atlassian.net/browse/SNT25-536) | In Progress              |
| Story — Phase 0                   | [SNT25-537](https://bluesquare.atlassian.net/browse/SNT25-537) | Backlog                  |
| Story — Phase 1                   | [SNT25-547](https://bluesquare.atlassian.net/browse/SNT25-547) | Backlog                  |
| Story — Phase 2                   | [SNT25-548](https://bluesquare.atlassian.net/browse/SNT25-548) | Backlog                  |
| Story — Phase 3                   | [SNT25-549](https://bluesquare.atlassian.net/browse/SNT25-549) | Backlog                  |
| Story — Phase 4                   | [SNT25-550](https://bluesquare.atlassian.net/browse/SNT25-550) | Backlog                  |
| Phase 0 Tasks T0.0–T0.7           | SNT25-538, SNT25-540–546                                       | Backlog (T0.6/T0.7 Done) |

**Still to create:** the Phase 1–4 Tasks (20), plus the dependency ("is blocked by") links and the Task↔Story ("relates") links. The Phase 0 task table below carries real keys; the Phase 1–4 task tables still show `_tbd_` until those tasks are created.

---

## Epic

| Key       | Type | Summary                    | Labels             |
| --------- | ---- | -------------------------- | ------------------ |
| SNT25-536 | Epic | SNT Pipelines Orchestrator | `snt-orchestrator` |

**Description:**
No-code OpenHEXA static webapp that renders the full SNT stratification pipeline flow as an
interactive map, and lets a non-coding user see the status of — and run — every pipeline from
one screen.

Source of truth (in repo):

- master plan & atomic tasks `knowledge/PLAN.md`;
- product spec `knowledge/PRODUCT_SPEC.md`.

---

## Story — Phase 0 · Foundations & de-risking (SNT25-537)

**Description:** Phase 0 of the orchestrator. Three parallel tracks — workspace setup (A), the
pipeline map (B), and a status-query spike (C) — plus a product-validation gate, all of which
unblock Phase 1.
**Exit criteria:** the product spec is validated; the SNT App Dev workspace has all pipelines
installed and run once; a valid `pipeline_map.json` exists and renders the intended layout; the
status query is confirmed working through the static-webapp proxy.

| Ref  | Key       | Type | Summary                                              | Owner               | Blocked by       | Status   |
| ---- | --------- | ---- | ---------------------------------------------------- | ------------------- | ---------------- | -------- |
| T0.0 | SNT25-538 | Task | T0.0 — Validate the product spec                     | giulia, pm, oh-devs | —                | Backlog  |
| T0.1 | SNT25-540 | Task | T0.1 — Set up the SNT App Dev workspace              | giulia              | —                | Backlog  |
| T0.2 | SNT25-541 | Task | T0.2 — Generate workspace config + cards             | agent, giulia       | T0.1             | Backlog  |
| T0.3 | SNT25-542 | Task | T0.3 — Consolidate the full map content              | giulia, pm          | —                | Backlog  |
| T0.4 | SNT25-543 | Task | T0.4 — Translate the sketch into `pipeline_map.json` | agent               | T0.3             | Backlog  |
| T0.5 | SNT25-544 | Task | T0.5 — Review the rendered layout                    | giulia, pm          | T0.4, T1.2, T1.3 | Backlog  |
| T0.6 | SNT25-545 | Task | T0.6 — Spike: status query through the proxy         | agent, giulia       | —                | **Done** |
| T0.7 | SNT25-546 | Task | T0.7 — If blocked: precise ask to OH devs            | oh-devs             | T0.6             | **Done** |

### T0.0 — Validate the product spec

**Description:** Review `knowledge/PRODUCT_SPEC.md` with the PM — the product/UX sections and
the **"Open questions for the PM"** list — then check technical feasibility with the OH devs
using the **"Technical feasibility"** checklist (items F1–F8). Capture answers back into the
spec and the decision logs in `knowledge/PLAN.md`.
**Acceptance criteria:** the PM's open questions are resolved and every technical-feasibility
item (F1–F8) has a dev ✅/⚠️.

### T0.1 — Set up the SNT App Dev workspace

**Description:** Stand up the dedicated "SNT App Dev" workspace so there is a stable,
fully-populated environment with real pipeline status to build against. Work through the
checklist in order:

- [ ] Create the "SNT App Dev" workspace in OpenHEXA and record its slug (likely `snt-app-dev`).
- [ ] Install every official SNT pipeline (from templates) into the workspace (~20).
- [ ] Set up the DHIS2/other connections, the `SNT_config.json`, and the input data each pipeline needs.
- [ ] Run each installed pipeline at least once, so there is real status + outputs for the board to display.

**Acceptance criteria:** all four boxes ticked — workspace exists (slug shared), all ~20
pipelines installed, a manual A.1 → A.2 run succeeds end-to-end, and each pipeline has at least
one terminal run (ideally `success`).

### T0.2 — Generate workspace config + cards

**Description:** Create `snt_app_dev/workspace_config.json` (pipeline UUIDs, connection slugs,
app id) and `snt_app_dev/pipeline_cards.json` (catalog + parameters), per the schemas and
instructions in `CLAUDE.md`.
**Acceptance criteria:** both files exist, validate against their schemas, and all UUIDs resolve.

### T0.3 — Consolidate the full map content

**Description:** Gather the complete pipeline list and the intended layout into one sketch: for
every pipeline — execution stage (`row`), horizontal track (`col`), `type`
(mandatory/alternative/facultative), mutex `group`, and dependency `edges`. Pull in colleagues'
existing diagrams; fill gaps; validate with the PM.
**Acceptance criteria:** there is one agreed sketch (paper/diagram/table) covering all pipelines.

### T0.4 — Translate the sketch into `pipeline_map.json`

**Description:** Turn the agreed sketch into a schema-valid `pipeline_map.json` (repo root,
shared across all workspaces) and validate it against `pipeline_map_schema.json`.
**Acceptance criteria:** the file validates and every node `id` matches a pipeline function name.

### T0.5 — Review the rendered layout

**Description:** Once the grid + arrows render (Phase 1), sanity-check node positions and edges
against the agreed sketch.
**Acceptance criteria:** the on-screen map matches the agreed flow.

### T0.6 — Spike: status query through the proxy _(Done)_

**Description:** Deploy a throwaway static webapp that queries pipeline last-run status through
the same-origin `/graphql/` proxy under `PIPELINES_READ`, to confirm cross-session status is
feasible before building the board. _Result (2026-06-08):_ deployed `t0-9-status-proxy-spike`
to `snt-testing`; it returned real last-run statuses for all pipelines, and a pipeline triggered
in the OH UI showed as `running` on refresh. Confirmed: cross-session status works through the
proxy under `PIPELINES_READ` alone. The `Workspace` type has no `pipelines` field, so the working
query uses the top-level `pipelines(workspaceSlug:…)` → `items { runs(orderBy:
EXECUTION_DATE_DESC, perPage:1) }`. Documented in `CLAUDE.md`; local mirror at
`snt_testing/status_spike/index.html`. (Deployed artifact keeps its original `t0-9` name.)
**Acceptance criteria:** ✅ met — the test app prints real statuses and live status transitions
appear on refresh.

### T0.7 — If blocked: precise ask to OH devs _(Done)_

**Description:** Contingency — if the spike (T0.6) had shown the status query blocked by the
proxy, file a precise ticket to the OH devs (exact query, required scope, error seen). Not
needed: T0.6 succeeded, so the confirmed working query is documented in `CLAUDE.md` ("Reading
last-run status for all pipelines") instead.
**Acceptance criteria:** ✅ met — status retrieval is working; no OH-devs ticket required.

---

## Story — Phase 1 · Read-only status board (SNT25-547)

**Description:** The first shippable webapp — a read-only status board. The full map renders,
every node shows real cross-session status, and clicking a node opens a read-only detail panel.
No Run button and no dependency locking yet.
**Exit criteria:** deployed to SNT App Dev; statuses match the OpenHEXA UI; reviewed by Giulia + PM.

| Ref  | Key   | Type | Summary                         | Owner         | Blocked by | Status  |
| ---- | ----- | ---- | ------------------------------- | ------------- | ---------- | ------- |
| T1.1 | _tbd_ | Task | T1.1 — Scaffold the app bundle  | agent         | T0.2, T0.4 | Backlog |
| T1.2 | _tbd_ | Task | T1.2 — Render the grid          | agent         | T1.1       | Backlog |
| T1.3 | _tbd_ | Task | T1.3 — Draw the SVG arrows      | agent         | T1.2       | Backlog |
| T1.4 | _tbd_ | Task | T1.4 — Available vs greyed      | agent         | T1.2       | Backlog |
| T1.5 | _tbd_ | Task | T1.5 — Live status layer        | agent         | T1.1, T0.6 | Backlog |
| T1.6 | _tbd_ | Task | T1.6 — Read-only detail sidebar | agent         | T1.4       | Backlog |
| T1.7 | _tbd_ | Task | T1.7 — Deploy + QA              | agent, giulia | T1.5, T1.6 | Backlog |
| T1.8 | _tbd_ | Task | T1.8 — UI review round          | pm, giulia    | T1.7       | Backlog |

### T1.1 — Scaffold the app bundle

**Description:** Create `snt_app_dev/orchestrator/` with `index.html` + `styles.css` + `app.js`,
cloned from the proven split app (`snt_testing/population_transformation_split/`). `app.js`
fetches `./pipeline_map.json` and `./pipeline_cards.json`.
**Acceptance criteria:** the app loads both JSON files and logs the merged node list.

### T1.2 — Render the grid

**Description:** Render the canvas — place each node at its `row`/`col` position from
`pipeline_map.json`.
**Acceptance criteria:** all nodes appear in the intended grid positions.

### T1.3 — Draw the SVG arrows

**Description:** Draw one SVG arrow per `edge` in `pipeline_map.json`, between node centers (no
graph-layout library or CDN).
**Acceptance criteria:** every dependency edge is visible and points the right way.

### T1.4 — Available vs greyed

**Description:** Compute available-vs-greyed: a node is available iff its `id` is present in
`pipeline_cards.json`; otherwise render it greyed-out and unclickable.
**Acceptance criteria:** missing pipelines render greyed; installed ones render active.

### T1.5 — Live status layer

**Description:** For each available node, fetch its latest run via the confirmed status query;
show a status badge + last-run datetime, and a link to that run's page in the OpenHEXA UI.
**Acceptance criteria:** statuses on the board match the OpenHEXA Pipelines view after a reload.

### T1.6 — Read-only detail sidebar

**Description:** Clicking a node opens the read-only sidebar: name, description, parameters
(display only), a link to the pipeline's `README.md` on GitHub, and links to its latest outputs
(datasets / HTML report).
**Acceptance criteria:** every available node shows correct details and working links.

### T1.7 — Deploy + QA

**Description:** Deploy the orchestrator bundle to SNT App Dev via the `update_static_webapp`
MCP tool; mirror all deployed files to `snt_app_dev/orchestrator/`.
**Acceptance criteria:** the deployed URL shows the correct map + real statuses; the local
mirror matches what was deployed.

### T1.8 — UI review round

**Description:** PM + Giulia review the board's look/feel and clarity; collect feedback as
concrete Phase 3 polish tasks.
**Acceptance criteria:** feedback is captured as concrete follow-up tasks.

---

## Story — Phase 2 · Make it runnable (SNT25-548)

**Description:** Make the board interactive — configure and launch pipelines from the sidebar,
poll the run, and refresh that node's status and outputs.
**Exit criteria:** Giulia can run any available pipeline from the board and watch it complete.

| Ref  | Key   | Type | Summary                                      | Owner         | Blocked by | Status  |
| ---- | ----- | ---- | -------------------------------------------- | ------------- | ---------- | ------- |
| T2.1 | _tbd_ | Task | T2.1 — Confirm params aren't stale           | giulia, agent | T0.2       | Backlog |
| T2.2 | _tbd_ | Task | T2.2 — Parameter form + config builder       | agent         | T1.6, T2.1 | Backlog |
| T2.3 | _tbd_ | Task | T2.3 — Run + poll                            | agent         | T2.2       | Backlog |
| T2.4 | _tbd_ | Task | T2.4 — Mutual exclusion (alternative groups) | agent         | T2.3       | Backlog |
| T2.5 | _tbd_ | Task | T2.5 — Missing-pipeline message              | agent         | T1.4       | Backlog |
| T2.6 | _tbd_ | Task | T2.6 — Deploy + QA running                   | agent, giulia | T2.3       | Backlog |

### T2.1 — Confirm params aren't stale

**Description:** Per the `CLAUDE.md` cache caveat: before wiring runs, re-fetch the `@parameter`
decorators for the pipelines to be run and patch any drift into `pipeline_cards.json`.
**Acceptance criteria:** card params match the current GitHub source for each runnable pipeline.

### T2.2 — Parameter form + config builder

**Description:** Generate the input form from each card's params; build the `config` object;
render `DHIS2Connection` params as a dropdown of the workspace's connections.
**Acceptance criteria:** the form produces a valid `config` object for a test pipeline.

### T2.3 — Run + poll

**Description:** Wire the Run button to the `runPipeline` mutation; poll the run; refresh that
node's status and outputs on completion (reuse the proven single-app logic).
**Acceptance criteria:** a real run triggered from the board completes and the node updates.

### T2.4 — Mutual exclusion (alternative groups)

**Description:** Running one node of an `alternative` `group` marks the others in that group
not-current (data-driven via `group`, not hardcoded).
**Acceptance criteria:** running A.3.1 visually supersedes A.3.2, etc.

### T2.5 — Missing-pipeline message

**Description:** When the user attempts to run a greyed/missing pipeline, show a clear "install
this pipeline first" message instead of a cryptic error.
**Acceptance criteria:** the message appears for a deliberately-missing pipeline.

### T2.6 — Deploy + QA running

**Description:** Deploy the runnable version to SNT App Dev and QA by running real pipelines from
the board.
**Acceptance criteria:** Giulia runs several pipelines from the deployed app successfully.

---

## Story — Phase 3 · Dependency locking + polish (SNT25-549)

**Description:** Dependency locking + polish — lock downstream nodes until upstream runs
succeed, add loading/empty/error states, and apply the Phase 1 UI-review feedback.

| Ref  | Key   | Type | Summary                 | Owner     | Blocked by | Status  |
| ---- | ----- | ---- | ----------------------- | --------- | ---------- | ------- |
| T3.1 | _tbd_ | Task | T3.1 — Upstream locking | agent     | T2.3       | Backlog |
| T3.2 | _tbd_ | Task | T3.2 — States & errors  | agent     | T1.7       | Backlog |
| T3.3 | _tbd_ | Task | T3.3 — Aesthetics pass  | agent, pm | T1.8       | Backlog |

### T3.1 — Upstream locking

**Description:** Implement upstream locking — a node unlocks only once every upstream `edge` has
a completed/successful run.
**Acceptance criteria:** downstream nodes stay locked until their prerequisites succeed.

### T3.2 — States & errors

**Description:** Add loading, empty, and error states for status fetch and pipeline runs.
**Acceptance criteria:** _(proposed)_ each state renders a clear message instead of a blank or
broken UI.

### T3.3 — Aesthetics pass

**Description:** Apply the Phase 1 UI-review feedback; polish for desktop-landscape use.
**Acceptance criteria:** _(proposed)_ the agreed review-feedback items are addressed and signed
off by the PM.

---

## Story — Phase 4 · Generalize across workspaces (SNT25-550)

**Description:** Generalize across workspaces — confirm the generic-bundle vs per-workspace-config
separation, prove portability on a second workspace, and document the runbook.

| Ref  | Key   | Type | Summary                                 | Owner         | Blocked by | Status  |
| ---- | ----- | ---- | --------------------------------------- | ------------- | ---------- | ------- |
| T4.1 | _tbd_ | Task | T4.1 — Verify generic/per-ws separation | agent         | T2.6       | Backlog |
| T4.2 | _tbd_ | Task | T4.2 — Deploy to a second workspace     | agent, giulia | T4.1       | Backlog |
| T4.3 | _tbd_ | Task | T4.3 — Document the runbook             | agent, giulia | T4.2       | Backlog |

### T4.1 — Verify generic/per-ws separation

**Description:** Confirm the bundle has zero hardcoded workspace specifics; everything
workspace-specific lives in the per-workspace config + cards files.
**Acceptance criteria:** the same `index.html`/`styles.css`/`app.js`/`pipeline_map.json` work
unchanged in a second workspace.

### T4.2 — Deploy to a second workspace

**Description:** Prove portability by deploying the orchestrator to one more workspace using only
a new `workspace_config.json` + `pipeline_cards.json`.
**Acceptance criteria:** _(proposed)_ the second workspace's orchestrator works with no code
changes — only new config + cards.

### T4.3 — Document the runbook

**Description:** Update `CLAUDE.md` / `README.md` with the "add a new workspace orchestrator"
runbook.
**Acceptance criteria:** _(proposed)_ a new-workspace deploy can be followed step-by-step from
the docs.

---

## Totals

1 Epic + 5 Stories + 28 Tasks = **34 issues**. Created so far: **14** — Epic, all 5 Stories, and
Phase 0 Tasks T0.0–T0.7 (T0.6/T0.7 already Done). To create: **20 Tasks** (Phases 1–4), plus the
"is blocked by" dependency links and the Task↔Story "relates" links.
