# Feature Delivery Prompt

## Router-first
- **Use when:** goal is implementing a scoped change with verification and rollback notes.
- **Do not use when:** goal is migration orchestration, plan-only output, or static-only audit.
- **Typical chained prompts:** `workflows/test-strategy.md` -> `workflows/qa-audit.md`.
- **Required mode/inputs:** none.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=implement`; `Scope=minimal change set inferred from Goal`; `Constraints=preserve stated invariants and non-goals`; `Definition of done=scoped change implemented with verification results and next steps`.

## 1) Assumptions
- State explicit assumptions up front when requirements are incomplete.
- Keep assumptions minimal and practical to avoid scope creep.

## 2) Scope / Invariants / Non-goals
- **Scope:** Define exactly what is changing.
- **Invariants:** List constraints that must remain true (behavioral, security, data, API compatibility, etc.).
- **Non-goals:** State what is intentionally out of scope.

## 3) Smallest Safe Implementation Plan
- Describe the minimum set of steps to deliver safely.
- Prefer surgical changes over broad refactors.
- Include an explicit **rollback note**:
  - What to revert, and the exact command(s) to revert safely.

## 4) Code Change Summary
- Summarize files touched and what changed in each.
- Keep this factual and concise.

## 5) Verification Commands / Results
- List only commands that were actually run.
- Report result per command/check: `PASS`, `FAIL`, `BLOCKED`, or `UNKNOWN` with reason.
- **No invented commands:** do not claim commands/checks that were not executed.
- If a check could not run, mark `UNKNOWN` and state exactly what would be run and why.

## 6) Risks
- List key technical/product risks introduced by the change.
- Include mitigation or monitoring notes for each risk.

## 7) Next Steps
- Provide up to 3 concrete follow-ups.
- Include exact commands when relevant.
