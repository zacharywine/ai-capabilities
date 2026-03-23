# Contracts Portability Checklist

Use this checklist for docs-only portability passes across workflow docs.

## Status vocabulary
Use only: `PASS` | `FAIL` | `BLOCKED` | `UNKNOWN`.

## Checklist
- [ ] README path references are repo-relative and do not assume any legacy docs-folder root.
- [ ] `workflows/test-strategy.md` canonical examples are toolchain-neutral (`<repo-...-command>` placeholders), without hardcoded ecosystem defaults.
- [ ] `CONTRACTS.md` workflow contract matrix remains unchanged unless a workflow-local output contract actually changed.

## Report format (docs-only)
1. Assumptions
2. Changes made
3. Unified diff
4. Verification commands + results
5. Risks/open concerns
6. Next steps (max 3)
