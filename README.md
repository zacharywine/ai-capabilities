# ai-capabilities

This repository is a centralized library of reusable AI capabilities, organized around `workflows/` as the primary capability family.

All capability references in this doc are repo-relative paths.

## Purpose

- Provide a single place to discover and run reusable AI capabilities.
- Keep capability selection deterministic when users provide only a goal.
- Standardize defaults, output contracts, and handoff patterns across capabilities.

## What belongs here

- Reusable workflow definitions that are safe to apply across projects.
- Deterministic routing/selection rules for goal-only requests.
- Execution and verification patterns for delivery, debugging, QA, testing, performance, and refactors.

## What does not belong here

- Project-specific one-off notes that do not generalize.
- Unscoped ad-hoc instructions without defined inputs/outputs.
- Capability docs that skip verification/reporting expectations.

## Primary content type (`workflows/`)

`workflows/` is the default capability family for day-to-day engineering execution.

### Goal-only router (workflow-first deterministic match)

When input is only a goal (no explicit capability/workflow name), route by first matching rule:

| First-match rule (in order) | Route to |
|---|---|
| Fix-now failure intent (debug/fix broken behavior, incident, regression with known failing behavior) | `workflows/debug-fix.md` |
| Plan-only artifact intent (milestones, sequencing, roadmap, delivery plan; no build request) | `workflows/plan-delivery.md` |
| Code-change intent (implement/build/change delivery, with or without a short plan) | `workflows/implement-change.md` |
| Measurement/profile/baseline intent (measure, profile, benchmark, establish baseline) | `workflows/investigate-performance.md` |
| Behavior-preserving cleanup/refactor-only intent | `workflows/implement-change.md` (Refactor-safe mode) |
| Static/read-only quality review intent | `workflows/static-audit.md` |
| Test-scope/coverage/strategy intent | `workflows/test-strategy.md` |
| Ambiguous high-level outcome only (no clear fix vs measure vs plan vs build verb) | `workflows/plan-delivery.md` (run 3-line triage: scope, risk, intent; then choose workflow) |

If multiple rules match, use this tie-break sentence: fix-now verbs beat measurement verbs, and measurement/profile/baseline verbs beat planning verbs.

### Input precedence (hybrid model)

Resolve inputs in this order: **explicit user input > router inference > capability defaults**.

- `Goal` is required.
- `Constraints` and `Definition of done` are optional and must default deterministically when omitted.

### Defaulting rule

When optional fields are inferred, list them under `Assumptions` as: `Inferred defaults applied: ...`.

### Minimal Input Contract (standard)

- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`
- All optional fields must resolve to explicit defaults in the assistant output.

### Quick routing examples (goal-only → workflow file)

Ordered to reflect first-match routing; keep only the first hit.

| Goal-only user input | Expected route |
|---|---|
| "Checkout throws 500 after coupon apply; fix it." | `workflows/debug-fix.md` |
| "Give me milestones to ship account deletion safely." | `workflows/plan-delivery.md` |
| "Plan and build dark mode end-to-end." | `workflows/implement-change.md` |
| "Measure API latency regression from last release." | `workflows/investigate-performance.md` |
| "Refactor cart totals module without changing behavior." | `workflows/implement-change.md` (Refactor-safe mode) |
| "Add bulk invite support for team admins." | `workflows/implement-change.md` |
| "Create a delivery roadmap for SOC2 logging work." | `workflows/plan-delivery.md` |
| "Do a read-only QA pass on signup flow." | `workflows/static-audit.md` |
| "Define test coverage for the new billing webhook." | `workflows/test-strategy.md` |
| "What is the smallest safe test plan for this auth refactor?" | `workflows/test-strategy.md` |
| "Review this PR for security and correctness risks (read-only)." | `workflows/static-audit.md` |
| "Broken mobile nav on iOS after yesterday's merge." | `workflows/debug-fix.md` |
| "Improve dashboard responsiveness and capture baseline." | `workflows/investigate-performance.md` |
| "Make onboarding better for enterprise users." | `workflows/plan-delivery.md` (ambiguous high-level outcome only) |

Ambiguous inputs resolved by tie-break (still first-match):

| Ambiguous goal-only input | Why ambiguous | Tie-break outcome |
|---|---|---|
| "Checkout is slow; profile it and make it fast." | Contains both measurement and fix-now verbs. | `workflows/debug-fix.md` (fix-now verbs beat measurement verbs). |
| "Auth got slower after deploy; establish baseline and roadmap." | Contains both measurement and planning verbs. | `workflows/investigate-performance.md` (measurement verbs beat planning verbs). |
| "Plan milestones, then implement retry logic for webhook failures." | Contains planning and code-change verbs. | `workflows/implement-change.md` (code-change intent, with short plan allowed). |

Fallback example trigger: `"Make checkout better."`

### Output schema precedence

The 7-heading schema is the default baseline when a workflow does not define a workflow-local output contract.
If a workflow defines a workflow-local output contract, follow that contract instead.
Workflow-local output contracts override this README schema.

Default heading order:

1. `Assumptions`
2. `Scope`
3. `Plan`
4. `Changes made`
5. `Verification`
6. `Risks`
7. `Next steps`

### Solo-dev entry point (start here)

If you only use one starting capability, use this decision path:

When goal-only routing applies, always follow router-first rules above.

1. New feature or change request → `workflows/implement-change.md`
2. Bug report or failing behavior → `workflows/debug-fix.md`
3. Unsure what tests are enough → `workflows/test-strategy.md`
4. Want an independent quality pass → `workflows/static-audit.md`
5. Slow page/API/system path → `workflows/investigate-performance.md`
6. Cleanup without behavior changes → `workflows/implement-change.md` (Refactor-safe mode)

Default sequence for solo work:

`workflows/implement-change.md` → `workflows/test-strategy.md` → `workflows/static-audit.md`

### Capability composition guide (solo-dev)

- Feature: `workflows/implement-change.md` → `workflows/test-strategy.md` → `workflows/static-audit.md`
  - Handoff artifact: patch summary + changed files + known risks.
  - Stop condition: tests pass, QA findings are resolved/accepted.
- Bug: `workflows/debug-fix.md` → `workflows/test-strategy.md` → `workflows/static-audit.md`
  - Handoff artifact: root cause, fix diff, and repro/regression checks.
  - Stop condition: bug no longer reproduces; no open high-severity QA issues.
- Perf: `workflows/investigate-performance.md` → `workflows/plan-delivery.md` (if needed) → `workflows/implement-change.md` → `workflows/static-audit.md`
  - Handoff artifact: baseline metrics, bottleneck notes, target metric, and change plan.
  - Stop condition: target metric hit (or tradeoff documented) and QA sign-off.
- Refactor: `workflows/implement-change.md` (Refactor-safe mode) → `workflows/test-strategy.md` → `workflows/static-audit.md`
  - Handoff artifact: refactor scope, invariants, and safety check list.
  - Stop condition: behavior unchanged, safety checks pass, QA clean.

Typical chained workflows (mixed intent):

`workflows/plan-delivery.md` → `workflows/implement-change.md` → `workflows/test-strategy.md` → `workflows/static-audit.md`

### Kickoff template (copy/paste)

Use this when starting any workflow capability:

```text
Goal: <what outcome you want>
Constraints: <time, scope, risk, non-goals>
Definition of done: <concrete checks and artifacts>
```

### When stuck

Use this fast recovery loop: `workflows/debug-fix.md` → `workflows/static-audit.md`.

## Additional content types (agents, instructions, evals, tools)

Planned expansion areas beyond `workflows/`:

- `agents/` for role-scoped, long-running execution personas.
- `instructions/` for reusable policy and style overlays.
- `evals/` for capability quality, routing regressions, and output-contract acceptance checks.
- `tools/` for automations and helper scripts tied to capabilities.

These should adopt the same deterministic routing and explicit input/output expectations.

## Repo map

- `workflows/` — primary capabilities for delivery and engineering workflow execution.

## Consume in another repo (60-second setup)

- Sync/copy this repo's shared library files: `workflows/`, `README.md` (router/source defaults), `CONTRACTS.md` (workflow-local output contracts), and `evals/` (drift fixtures).
- Treat source-of-truth in this order: workflow doc contract text (`workflows/*.md`) -> `CONTRACTS.md` matrix -> `README.md` router/defaults -> `evals/*.yaml` fixtures.
- In target repos, keep repo-specific policy/infrastructure instructions in that target repo (for example local AGENTS/CI/docs), not in this shared library.
- For updates, prefer upstream fast-forward syncs; keep local overrides minimal and isolated to a small compatibility patch so upstream contract/routing changes can be pulled cleanly.

### Capability index

- `CONTRACTS.md` — matrix of workflow output contracts (required heading order, status vocabulary, and README-schema override behavior).
- `CONTRACTS_PORTABILITY_CHECKLIST.md` — portability consistency checklist for workflow docs and contracts.
- `evals/` — lightweight routing and contract-drift eval fixtures for workflows-first behavior.

### `workflows/`
- `workflows/plan-delivery.md` — plan scoped delivery work into clear, executable steps. Output: ordered implementation plan with risks/dependencies.
- `workflows/implement-change.md` — implement a feature with small, verifiable increments. Output: patch summary + verification report.
- `workflows/debug-fix.md` — triage, isolate, and fix a defect safely. Output: root-cause summary + fix + regression checks.
- `workflows/test-strategy.md` — define targeted, risk-based test coverage for a change. Output: prioritized test matrix and execution scope.
- `workflows/static-audit.md` — run a structured QA audit and report findings with evidence. Output: issue list with severity, repro, and evidence.
- `workflows/investigate-performance.md` — investigate performance issues and propose/verify improvements. Output: bottleneck analysis + measured before/after impact.
- Refactor-safe requests also route to `workflows/implement-change.md` (`Mode: refactor-safe`).

### Conventions for new workflows/capabilities

- Add reusable capabilities under `workflows/`.
- Use lowercase kebab-case filenames (example: `workflows/<new-workflow>`).
- Keep each capability self-contained with:
  1. purpose,
  2. required inputs,
  3. execution steps,
  4. verification/reporting format.
- Prefer adding new files in `workflows/`; avoid placing capability files at repo root unless they are top-level registry/governance docs.
