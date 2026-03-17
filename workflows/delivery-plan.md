# Delivery Plan Prompt (Plan Mode Only)

## Router-first
- **Use when:** goal is plan-only output (milestones, dependencies, risks, verification strategy).
- **Do not use when:** goal requires implementation or file edits.
- **Typical chained prompts:** `workflows/feature-delivery.md` -> `workflows/test-strategy.md`.
- **Required mode/inputs:** plan mode only; no file edits.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=plan`; `Scope=smallest deliverable scope inferred from Goal`; `Constraints=no file edits and no mutating commands`; `Definition of done=milestone plan with dependencies, risks, and verification strategy`.

Use this prompt when producing **plan-mode outputs only**.
## Hard rule
- **No file edits occur in this mode.**
- Do not create, modify, delete, or rename files.
- Do not run mutating commands.

## Output structure
Note: This prompt uses a prompt-local output structure and overrides the README default schema.


1. **Assumptions**
   - State explicit assumptions used to build the plan.
   - Keep assumptions testable and limited to unknowns.

2. **Milestone slices**
   - Break work into small, reversible slices.
   - For each slice include:
     - goal,
     - scope in/out,
     - owner,
     - rough effort,
     - rollback note.

3. **Dependencies**
   - List internal/external dependencies per slice.
   - Mark each as: `ready`, `blocked`, or `unknown`.
   - Include next action for non-ready dependencies.

4. **Risk register**
   - Capture key risks with:
     - risk statement,
     - likelihood (`low|med|high`),
     - impact (`low|med|high`),
     - mitigation,
     - contingency trigger.

5. **Verification strategy**
   - Define how completion will be validated per slice:
     - checks/tests to run,
     - acceptance evidence,
     - owner.
   - For command/check reporting, use `PASS|FAIL|BLOCKED|UNKNOWN`.
   - If verification cannot run yet, mark `BLOCKED` (external prerequisite) or `UNKNOWN` (not verifiable yet) and state exact blocker.

6. **Exit criteria**
   - Define objective done conditions for the overall plan:
     - all critical slices complete,
     - required checks pass,
     - high risks mitigated or accepted,
     - handoff/rollback documented.

## Response constraints
- Keep output concise and practical (KISS).
- Prefer smallest safe slice sequence.
- If a critical architecture/security/data ambiguity exists, ask **one** focused question; otherwise proceed with assumptions.
