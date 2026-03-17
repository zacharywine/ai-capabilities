# Refactor-Safe Prompt

## Router-first
- **Use when:** goal is behavior-preserving cleanup or structural refactor with explicit invariants.
- **Do not use when:** goal includes intentional API/schema/behavior changes.
- **Typical chained prompts:** `workflows/test-strategy.md` -> `workflows/qa-audit.md`.
- **Required mode/inputs:** explicit in-scope/out-of-scope declaration before edits.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=refactor`; `Scope=smallest behavior-preserving internal cleanup implied by Goal`; `Constraints=no API/schema/behavior drift unless scoped exception is explicit`; `Definition of done=slice plan, before/after checklists, verification, and rollback notes with invariants preserved`.

Use this prompt to keep refactors behaviorally identical unless change scope explicitly allows otherwise.
## 1) Invariant behavior contract (must hold)
- Keep all externally visible behavior unchanged unless explicitly scoped.
- Preserve:
  - public API signatures and semantics,
  - data formats (request/response, files, events),
  - side effects (ordering, idempotency, error paths),
  - performance/SLO expectations for unchanged paths.
- If any invariant cannot be preserved, stop and mark as **out of scope** until approved.

## 2) Rename/move boundaries (allowed, with limits)
- Allowed without behavior change:
  - internal symbol renames,
  - file/module moves,
  - code extraction/inlining,
  - dead code removal only if proven unreachable.
- Not allowed unless scoped:
  - changing public names/paths,
  - changing persistence schema or wire contracts,
  - changing auth/security checks,
  - changing retries/timeouts/concurrency semantics.

## 3) No API/contract drift unless scoped
Before editing, declare:
- **In scope:** exact modules/files and intended internal cleanup.
- **Out of scope:** API, schema, protocol, behavior, and config changes.
- **Scoped exceptions (optional):** explicit, minimal, and justified.

If a requested refactor implies contract drift, split it:
1. behavior-preserving refactor slice,
2. separate contract-change slice with explicit approval.

## 4) Smallest-scope refactor slices (required)
- Work in tiny, reversible slices (one concern per slice).
- For each slice, record:
  - objective,
  - touched files,
  - why behavior is invariant,
  - verification command(s).
- Prefer mechanical edits first, semantic edits second.

## 5) Before/after risk checklist (required)
Run this checklist before and after each slice.

### Before
- [ ] Invariants listed and understood.
- [ ] Scope boundaries documented.
- [ ] No contract/API/schema/security drift planned.
- [ ] Rollback path defined (commit/tag or patch revert plan).

### After
- [ ] Public API and contracts unchanged (or scoped exception documented).
- [ ] Tests/checks for touched area pass.
- [ ] Error handling/logging behavior unchanged.
- [ ] Performance-sensitive paths not regressed (or scoped exception documented).
- [ ] Rollback notes updated and validated.

## 6) Rollback notes (required)
For each slice, include:
- exact commit/hash (or patch identifier),
- `git revert <hash>` plan,
- data/backfill reversal steps (if any),
- operational rollback trigger signals.

## 7) Output format (use every time)
Note: This prompt uses a prompt-local output format and overrides the README default schema.

1. **Scope declaration** (in/out/scoped exceptions)
2. **Slice plan** (smallest possible steps)
3. **Risk checklist (before)**
4. **Implementation notes** (what changed, no contract drift)
5. **Verification results** (commands + `PASS|FAIL|BLOCKED|UNKNOWN`)
6. **Risk checklist (after)**
7. **Rollback notes**

## 8) Hard stop conditions
Stop and request explicit approval if you detect:
- contract/API/schema drift,
- security/privacy/auth behavior change,
- migration/backfill requirement,
- multi-module changes that cannot be sliced safely.
