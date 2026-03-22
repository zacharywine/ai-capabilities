# Implement Change Workflow

## Router-first
- **Use when:** goal is implementing a scoped change with verification and rollback notes.
- **Do not use when:** goal is plan-only output or static-only audit.
- **Typical chained workflows:** `workflows/test-strategy.md` -> `workflows/static-audit.md`.
- **Required mode/inputs:** none.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=implement`; `Scope=minimal change set inferred from Goal`; `Constraints=preserve stated invariants and non-goals`; `Definition of done=scoped change implemented with verification results and next steps`.

## Refactor-safe mode (merged from legacy refactor workflow)
Use when request is behavior-preserving cleanup.
- Keep public API/schema/behavior unchanged unless explicitly scoped.
- Work in small reversible slices.
- Stop and request approval if contract/API/schema/security behavior drift is required.

## Required flow
1. **Assumptions**
   - State explicit assumptions only when requirements are incomplete.
2. **Scope / Invariants / Non-goals**
   - Define exact change scope, must-hold invariants, and explicit non-goals.
3. **Smallest safe implementation plan**
   - List minimum implementation steps and rollback command.
4. **Code change summary**
   - Summarize touched files and what changed.
5. **Verification commands / results**
   - List only commands actually run.
   - Report `PASS|FAIL|BLOCKED|UNKNOWN` with reason.
   - If not runnable, mark `UNKNOWN` and state exact command you would run.
6. **Risks**
   - List key risks plus mitigation/monitoring.
7. **Next steps**
   - Up to 3 concrete follow-ups; include exact commands when relevant.
