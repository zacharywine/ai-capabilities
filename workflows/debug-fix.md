# Debug Fix Workflow

## Router-first
- **Use when:** goal is restoring expected behavior for a concrete failing behavior.
- **Do not use when:** goal is baseline/profile/compare measurement-first investigation.
- **Typical chained workflows:** `workflows/test-strategy.md` -> `workflows/static-audit.md`.
- **Required mode/inputs:** none.
## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=fix`; `Scope=smallest area needed to reproduce and isolate the defect in Goal`; `Constraints=evidence-first, minimal change, no unrelated refactors`; `Definition of done=repro is fixed, targeted verification passes, rollback note included`.

Use this workflow to drive a fast, evidence-first bug fix.
## Required flow

### 1) Reproduction recipe
- Define exact preconditions (env vars, data seed, user role, feature flag).
- List exact commands/actions to reproduce.
- Record observed output/log/error.

Template:
```md
Status: PASS|FAIL|BLOCKED|UNKNOWN
Steps:
1. `<exact command/action>`
2. `<exact command/action>`
Observed:
- `<error/log/output>`
```

### 2) Expected vs actual
- Expected behavior (what should happen).
- Actual behavior (what happens now).
- Impact scope (who/what is affected).

Template:
```md
Expected:
- ...
Actual:
- ...
Impact:
- ...
```

### 3) Root-cause hypothesis list
- Provide a short ranked list (most likely first).
- Each hypothesis must include one confirming check.

Template:
```md
1. Hypothesis: ...
   Check: `<exact command/action>`
2. Hypothesis: ...
   Check: `<exact command/action>`
```

### 4) Minimal fix
- Apply the smallest safe change that resolves the issue.
- No unrelated refactors.
- Note rollback path (exact file/flag/commit to revert).

Template:
```md
Change:
- `<file>`: `<what changed>`
Rollback:
- `git revert <commit_sha>`
```

### 5) Targeted validation
- Run the smallest relevant checks for the touched area.
- Include exact commands and `PASS|FAIL|BLOCKED|UNKNOWN` status.

Template:
```md
| Command | Status | Evidence |
|---|---|---|
| `<exact command>` | PASS|FAIL|BLOCKED|UNKNOWN | `<key output>` |
```

### 6) Regression guard
- Add or update one focused automated check (test/lint/assertion) for this bug path when feasible.
- If not feasible now, record why and the exact follow-up command.

Template:
```md
Guard:
- `<test/assertion added or updated>`
If deferred:
- Reason: ...
- Next command: `<exact command>`
```

## UNKNOWN / BLOCKED handling (mandatory)

For any step marked `UNKNOWN` or `BLOCKED`, include all of:
1. **Reason**: exact blocker or missing signal.
2. **Next command/action**: exact single command or manual action to run next.
3. **Needs**: required environment/input/owner.

Template:
```md
Status: UNKNOWN|BLOCKED
Reason:
- ...
Next command/action:
- `<exact command or action>`
Needs:
- `<env/input/owner>`
```

## Output contract
Note: This workflow uses a workflow-local output contract and overrides the README default schema.


Always return in this order:
1. Reproduction recipe
2. Expected vs actual
3. Root-cause hypothesis list
4. Minimal fix
5. Targeted validation
6. Regression guard
7. Next steps (max 3, exact commands/actions)
