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
| Asks to debug a defect, failure, incident, broken behavior, or a performance defect with known failing behavior | `workflows/debug-fix.md` |
| Asks for plan-only output, milestones, sequencing, or delivery roadmap | `workflows/plan-delivery.md` |
| Asks for both a plan and implementation in one request | `workflows/implement-change.md` (start with a short `Plan` section) |
| Asks for a static/read-only quality review or audit findings | `workflows/static-audit.md` |
| Asks to define test scope/coverage/strategy for a change | `workflows/test-strategy.md` |
| Asks to diagnose or measure slowness, regressions, throughput, latency, or establish a performance baseline | `workflows/investigate-performance.md` |
| Asks for behavior-preserving cleanup/refactor only | `workflows/implement-change.md (refactor-safe mode)` |
| If the goal is ambiguous (high-level outcome only, no clear plan vs build vs debug intent) | `workflows/plan-delivery.md` (run a 3-line triage preamble: scope, risk, intent; then select the best-fit workflow) |
| Default for implementation/change delivery requests | `workflows/implement-change.md` |

If multiple rules match, use the first row only.
Tie-break (only after the ambiguous-goal triage row is considered): if the user asks for baseline/measurement, route to `workflows/investigate-performance.md`; if the user asks to fix a concrete failing behavior (including performance), route to `workflows/debug-fix.md`.

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

### Quick routing examples (goal-only → capability file)

Ordered to reflect first-match routing; keep only the first hit.

| Goal-only user input | Expected route |
|---|---|
| "Checkout throws 500 after coupon apply; fix it." | `workflows/debug-fix.md` |
| "Give me milestones to ship account deletion safely." | `workflows/plan-delivery.md` |
| "Plan and build dark mode end-to-end." | `workflows/implement-change.md` |
| "Do a read-only QA pass on signup flow." | `workflows/static-audit.md` |
| "Define test coverage for the new billing webhook." | `workflows/test-strategy.md` |
| "Measure API latency regression from last release." | `workflows/investigate-performance.md` |
| "Refactor cart totals module without changing behavior." | `workflows/implement-change.md (refactor-safe mode)` |
| "Add bulk invite support for team admins." | `workflows/implement-change.md` |
| "Create a delivery roadmap for SOC2 logging work." | `workflows/plan-delivery.md` |
| "Broken mobile nav on iOS after yesterday's merge." | `workflows/debug-fix.md` |
| "Improve dashboard responsiveness and capture baseline." | `workflows/investigate-performance.md` |
| "Make the onboarding flow better for enterprise users." | `workflows/plan-delivery.md` (ambiguous triage row matches before default) |

Ambiguous inputs resolved by tie-break (still first-match):

| Ambiguous goal-only input | Why ambiguous | Tie-break outcome |
|---|---|---|
| "Checkout is slow; profile it and make it fast." | Could be perf investigation or defect fix. | `workflows/debug-fix.md` (asks to fix concrete failing behavior). |
| "Auth got slower after deploy; establish baseline first." | Could be defect triage or perf work. | `workflows/investigate-performance.md` (explicit baseline/measurement ask). |
| "Plan and implement fixes for search latency regression." | Could match plan-only, plan+build, and perf/debug intents. | `workflows/implement-change.md` (plan+implementation rule appears before perf/refactor/default). |

Fallback example trigger: `"Make checkout better."`

### Output schema precedence

The 7-heading schema is the default baseline for capabilities that do not define their own output contract.
If a capability defines a capability-local output contract, follow that contract instead.
Capability-local output contract overrides README schema.

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
6. Cleanup without behavior changes → `workflows/implement-change.md (refactor-safe mode)`

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
- Refactor: `workflows/implement-change.md (refactor-safe mode)` → `workflows/test-strategy.md` → `workflows/static-audit.md`
  - Handoff artifact: refactor scope, invariants, and safety check list.
  - Stop condition: behavior unchanged, safety checks pass, QA clean.

Typical chained capabilities (mixed intent):

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

## Future content types later (agents, instructions, evals, tools)

Planned expansion areas beyond `workflows/`:

- `agents/` for role-scoped, long-running execution personas.
- `instructions/` for reusable policy and style overlays.
- `evals/` for capability quality, regressions, and acceptance criteria.
- `tools/` for automations and helper scripts tied to capabilities.

These should adopt the same deterministic routing and explicit input/output expectations.

## Repo map

- `workflows/` — primary capabilities for delivery and engineering workflow execution.

### Capability index

- `CONTRACTS.md` — matrix of capability output contracts (required heading order, status vocabulary, and README-schema override behavior).
- `CONTRACTS_PORTABILITY_CHECKLIST.md` — portability consistency checklist for capability docs and contracts.

### `workflows/`
- `workflows/plan-delivery.md` — plan scoped delivery work into clear, executable steps. Output: ordered implementation plan with risks/dependencies.
- `workflows/implement-change.md` — implement a feature with small, verifiable increments. Output: patch summary + verification report.
- `workflows/debug-fix.md` — triage, isolate, and fix a defect safely. Output: root-cause summary + fix + regression checks.
- `workflows/test-strategy.md` — define targeted, risk-based test coverage for a change. Output: prioritized test matrix and execution scope.
- `workflows/static-audit.md` — run a structured QA audit and report findings with evidence. Output: issue list with severity, repro, and evidence.
- `workflows/investigate-performance.md` — investigate performance issues and propose/verify improvements. Output: bottleneck analysis + measured before/after impact.
- `workflows/implement-change.md (refactor-safe mode)` — perform low-risk refactors with explicit safety checks. Output: behavior-preserving refactor plan + validation notes.
- portability-upgrade workflow — archived in this cleanup (removed from active workflow routing).

### Conventions for new workflows/capabilities

- Add reusable capabilities under `workflows/`.
- Use lowercase kebab-case filenames (example: `workflows/<new-workflow>`).
- Keep each capability self-contained with:
  1. purpose,
  2. required inputs,
  3. execution steps,
  4. verification/reporting format.
- Prefer adding new files in `workflows/`; avoid placing capability files at repo root unless they are top-level registry/governance docs.
