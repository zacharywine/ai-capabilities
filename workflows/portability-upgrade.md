# Portability Upgrade Workflow (Docs-only)

Use this workflow to normalize prompt-library docs for cross-repo portability without runtime/code changes.

## Scope guardrails
- Docs-only changes.
- No dependencies, CI/runtime/script changes, or broad refactors.
- Preserve existing output-contract/status vocabulary: `PASS` | `FAIL` | `BLOCKED` | `UNKNOWN`.

## Required checks
1. Normalize path references to repo-relative wording unless a fixed path is truly required.
2. Keep canonical command examples toolchain-neutral using repository-resolved placeholders (for example, `<repo-test-command>`).
3. Keep guidance aligned across:
   - `README.md`
   - `CONTRACTS.md`
   - `CONTRACTS_PORTABILITY_CHECKLIST.md`
   - `migration/nextmig.md`

## Output format
1. Assumptions
2. Changes made
3. Unified diff
4. Verification commands + results
5. Risks/open concerns
6. Next steps (max 3)
