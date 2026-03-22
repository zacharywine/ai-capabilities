# Static Audit Workflow (Read-only)

## Router-first
- **Use when:** goal is a static, read-only QA audit with severity-ranked findings.
- **Do not use when:** goal requires runtime execution or implementation work.
- **Typical chained workflows:** `workflows/debug-fix.md` (for remediation) -> `workflows/test-strategy.md`.
- **Required mode/inputs:** static inspection mode only.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=static-audit`; `Scope=files/PR area implied by Goal`; `Constraints=read-only static inspection, no runtime commands unless explicitly requested`; `Definition of done=severity-ranked findings with evidence and actionable remediation`.

## Guardrails
- Do not edit files.
- Do not run runtime commands unless explicitly requested.

## Output contract
Return findings grouped in this exact order:
1. `critical`
2. `high`
3. `medium`
4. `low`

For each finding include:
- **Title**
- **Location** (file path + lines)
- **Why It Matters**
- **Evidence**
- **Actionable Fix**

If a severity has no findings, include `- none`.
End with a brief summary count by severity.
