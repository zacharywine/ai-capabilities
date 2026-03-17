# Prompt Library

This folder organizes reusable prompt templates by use case.

All prompt references in this doc use paths relative to `docs/prompts/`.

## Goal-only router (deterministic first match)

When input is only a goal (no explicit prompt name), route by first matching rule:

| First-match rule (in order) | Route to |
|---|---|
| Mentions Next.js migration, cutover, or Express/Vite migration slice work | `migration/nextmig.md` |
| Asks to debug a defect, failure, incident, broken behavior, or a performance defect with known failing behavior | `workflows/debug-fix.md` |
| Asks for plan-only output, milestones, sequencing, or delivery roadmap | `workflows/delivery-plan.md` |
| Asks for both a plan and implementation in one request | `workflows/feature-delivery.md` (start with a short `Plan` section) |
| Asks for a static/read-only quality review or audit findings | `workflows/qa-audit.md` |
| Asks to define test scope/coverage/strategy for a change | `workflows/test-strategy.md` |
| Asks to diagnose or measure slowness, regressions, throughput, latency, or establish a performance baseline | `workflows/perf-investigation.md` |
| Asks for behavior-preserving cleanup/refactor only | `workflows/refactor-safe.md` |
| If the goal is ambiguous (high-level outcome only, no clear plan vs build vs debug intent) | `workflows/delivery-plan.md` (run a 3-line triage preamble: scope, risk, intent; then select the best-fit workflow) |
| Default for implementation/change delivery requests | `workflows/feature-delivery.md` |

If multiple rules match, use the first row only.
Tie-break (only after the ambiguous-goal triage row is considered): if the user asks for baseline/measurement, route to `workflows/perf-investigation.md`; if the user asks to fix a concrete failing behavior (including performance), route to `workflows/debug-fix.md`.

## Input precedence (hybrid model)

Resolve inputs in this order: **explicit user input > router inference > prompt defaults**.

- `Goal` is required.
- `Constraints` and `Definition of done` are optional and must default deterministically when omitted.

## Defaulting rule

When optional fields are inferred, list them under `Assumptions` as: `Inferred defaults applied: ...`.

## Minimal Input Contract (standard)

- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`
- All optional fields must resolve to explicit defaults in the assistant output.

### Quick routing examples (goal-only → prompt file)

Ordered to reflect first-match routing; keep only the first hit.

| Goal-only user input | Expected route |
|---|---|
| "Migrate auth pages from Express/Vite to Next.js this sprint." | `migration/nextmig.md` |
| "Checkout throws 500 after coupon apply; fix it." | `workflows/debug-fix.md` |
| "Give me milestones to ship account deletion safely." | `workflows/delivery-plan.md` |
| "Plan and build dark mode end-to-end." | `workflows/feature-delivery.md` |
| "Do a read-only QA pass on signup flow." | `workflows/qa-audit.md` |
| "Define test coverage for the new billing webhook." | `workflows/test-strategy.md` |
| "Measure API latency regression from last release." | `workflows/perf-investigation.md` |
| "Refactor cart totals module without changing behavior." | `workflows/refactor-safe.md` |
| "Add bulk invite support for team admins." | `workflows/feature-delivery.md` |
| "Create a delivery roadmap for SOC2 logging work." | `workflows/delivery-plan.md` |
| "Broken mobile nav on iOS after yesterday's merge." | `workflows/debug-fix.md` |
| "Improve dashboard responsiveness and capture baseline." | `workflows/perf-investigation.md` |
| "Make the onboarding flow better for enterprise users." | `workflows/delivery-plan.md` (ambiguous triage row matches before default) |

Ambiguous inputs resolved by tie-break (still first-match):

| Ambiguous goal-only input | Why ambiguous | Tie-break outcome |
|---|---|---|
| "Checkout is slow; profile it and make it fast." | Could be perf investigation or defect fix. | `workflows/debug-fix.md` (asks to fix concrete failing behavior). |
| "Auth got slower after deploy; establish baseline first." | Could be defect triage or perf work. | `workflows/perf-investigation.md` (explicit baseline/measurement ask). |
| "Plan and implement fixes for search latency regression." | Could match plan-only, plan+build, and perf/debug intents. | `workflows/feature-delivery.md` (plan+implementation rule appears before perf/refactor/default). |

Fallback example trigger: `"Make checkout better."`

## Output schema precedence

The 7-heading schema is the default baseline for prompts that do not define their own output contract.
If a prompt defines a prompt-local output contract, follow that contract instead.
Prompt-local output contract overrides README schema.

Default heading order:

1. `Assumptions`
2. `Scope`
3. `Plan`
4. `Changes made`
5. `Verification`
6. `Risks`
7. `Next steps`

## Solo-dev entry point (start here)

If you only use one starting prompt, use this decision path:

When goal-only routing applies, always follow router-first rules above.

1. Next.js migration slice work → `migration/nextmig.md`
2. New feature or change request → `workflows/feature-delivery.md`
3. Bug report or failing behavior → `workflows/debug-fix.md`
4. Unsure what tests are enough → `workflows/test-strategy.md`
5. Want an independent quality pass → `workflows/qa-audit.md`
6. Slow page/API/system path → `workflows/perf-investigation.md`
7. Cleanup without behavior changes → `workflows/refactor-safe.md`

Default sequence for solo work:

`workflows/feature-delivery.md` → `workflows/test-strategy.md` → `workflows/qa-audit.md`

## Prompt chaining quick guide (solo-dev)

- Feature: `workflows/feature-delivery.md` → `workflows/test-strategy.md` → `workflows/qa-audit.md`
  - Handoff artifact: patch summary + changed files + known risks.
  - Stop condition: tests pass, QA findings are resolved/accepted.
- Bug: `workflows/debug-fix.md` → `workflows/test-strategy.md` → `workflows/qa-audit.md`
  - Handoff artifact: root cause, fix diff, and repro/regression checks.
  - Stop condition: bug no longer reproduces; no open high-severity QA issues.
- Perf: `workflows/perf-investigation.md` → `workflows/delivery-plan.md` (if needed) → `workflows/feature-delivery.md` → `workflows/qa-audit.md`
  - Handoff artifact: baseline metrics, bottleneck notes, target metric, and change plan.
  - Stop condition: target metric hit (or tradeoff documented) and QA sign-off.
- Refactor: `workflows/refactor-safe.md` → `workflows/test-strategy.md` → `workflows/qa-audit.md`
  - Handoff artifact: refactor scope, invariants, and safety check list.
  - Stop condition: behavior unchanged, safety checks pass, QA clean.

Typical chained prompts (mixed intent):

`workflows/delivery-plan.md` → `workflows/feature-delivery.md` → `workflows/test-strategy.md` → `workflows/qa-audit.md`

### Kickoff template (copy/paste)

Use this when starting any workflow prompt:

```text
Goal: <what outcome you want>
Constraints: <time, scope, risk, non-goals>
Definition of done: <concrete checks and artifacts>
```

### When stuck

Use this fast recovery loop: `workflows/debug-fix.md` → `workflows/qa-audit.md`.

## Structure

- `migration/` — prompts for migration/cutover work.
- `workflows/` — prompts for delivery and engineering workflow execution.

## Prompt index

- `CONTRACTS.md` — matrix of prompt output contracts (required heading order, status vocabulary, and README-schema override behavior).

### `migration/`
- `migration/nextmig.md` — orchestrates Express + Vite → Next.js migration slices with gates, verification, and ledger updates. Output: migration slice plan + checklist + ledger update notes.

### `workflows/`
- `workflows/delivery-plan.md` — plan scoped delivery work into clear, executable steps. Output: ordered implementation plan with risks/dependencies.
- `workflows/feature-delivery.md` — implement a feature with small, verifiable increments. Output: patch summary + verification report.
- `workflows/debug-fix.md` — triage, isolate, and fix a defect safely. Output: root-cause summary + fix + regression checks.
- `workflows/test-strategy.md` — define targeted, risk-based test coverage for a change. Output: prioritized test matrix and execution scope.
- `workflows/qa-audit.md` — run a structured QA audit and report findings with evidence. Output: issue list with severity, repro, and evidence.
- `workflows/perf-investigation.md` — investigate performance issues and propose/verify improvements. Output: bottleneck analysis + measured before/after impact.
- `workflows/refactor-safe.md` — perform low-risk refactors with explicit safety checks. Output: behavior-preserving refactor plan + validation notes.

## Conventions for new prompts

- Put migration-focused prompts in `migration/`; put day-to-day delivery/playbook prompts in `workflows/`.
- Use lowercase kebab-case filenames (example: `workflows/api-contract-audit.md`).
- Keep each prompt self-contained with:
  1. purpose,
  2. required inputs,
  3. execution steps,
  4. verification/reporting format.
- Prefer adding new files in these subfolders; avoid placing prompt files directly under `docs/prompts/`.
