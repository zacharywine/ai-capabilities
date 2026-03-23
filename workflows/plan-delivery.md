# Plan Delivery Workflow (Plan Mode Only)

## Router-first
- **Use when:** goal is artifact-only planning output (milestones, dependencies, risks, verification strategy).
- **Do not use when:** goal requires implementation or any file edits.
- **Typical chained workflows:** `workflows/implement-change.md` -> `workflows/test-strategy.md`.
- **Required mode/inputs:** plan mode only; no file edits.
## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=plan`; `Scope=smallest deliverable scope inferred from Goal`; `Constraints=no file edits and no mutating commands`; `Definition of done=milestone plan with dependencies, risks, and verification strategy`.

## Required flow
1. **Assumptions**
2. **Milestone Slices** (goal, scope in/out, owner, effort, rollback note)
3. **Dependencies** (`ready|blocked|unknown` + next action for non-ready)
4. **Risk Register** (likelihood, impact, mitigation, contingency trigger)
5. **Verification Strategy** (checks/evidence/owner; use `PASS|FAIL|BLOCKED|UNKNOWN` for command reporting)
6. **Exit Criteria**

## Response constraints
- No file edits and no mutating commands.
- Keep output concise and practical (KISS).
- Ask one focused question only for critical architecture/security/data ambiguity; otherwise proceed with assumptions.
