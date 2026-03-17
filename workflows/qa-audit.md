# QA Audit Prompt (Static Inspection Mode)

## Router-first
- **Use when:** goal is a static, read-only QA audit with severity-ranked findings.
- **Do not use when:** goal requires runtime execution, implementation, or migration cutover work.
- **Typical chained prompts:** `workflows/debug-fix.md` (for remediation) -> `workflows/test-strategy.md`.
- **Required mode/inputs:** static inspection mode only.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=static-audit`; `Scope=files/PR area implied by Goal`; `Constraints=read-only static inspection, no runtime commands unless explicitly requested`; `Definition of done=severity-ranked findings with evidence and actionable remediation`.

Use this prompt when performing a **static-only QA audit** of a codebase, PR, or file set.
## Mode & Guardrails
- Operate in **static inspection mode only**.
- **Do not edit files.**
- **Do not run the application, tests, scripts, builds, migrations, or any runtime command** unless explicitly requested by the user.
- If runtime verification is requested, do only what was explicitly approved.

## Required Output Format
Note: This prompt uses a prompt-local output format and overrides the README default schema.

Return findings grouped by severity in this exact order:
1. `critical`
2. `high`
3. `medium`
4. `low`

If a section has no findings, include `- none`.

## Finding Template (required for every finding)
For each item, include:
- **Title**: short issue name
- **Location**: file path + line(s)
- **Why it matters**: risk/impact in 1-2 lines
- **Evidence**: concrete code/config observation
- **Actionable fix**: step-by-step instructions to remediate

## Additional Rules
- Prioritize correctness, security, and data integrity issues first.
- Keep recommendations minimal and practical (smallest safe fix).
- Do not speculate beyond available static evidence; mark uncertainty explicitly.
- If execution evidence (commands/checks) is reported, include status per item using `PASS|FAIL|BLOCKED|UNKNOWN` with supporting evidence.
- End with a brief **Summary** that lists total counts by severity.
