# Contracts Portability Checklist

Use this checklist for docs-only portability passes across capability/workflow files.

## Status vocabulary
Use only: `PASS` | `FAIL` | `BLOCKED` | `UNKNOWN`.

## Checklist
- [ ] README path references are repo-relative and do not assume any legacy docs-folder root.
- [ ] `workflows/test-strategy.md` canonical examples are toolchain-neutral (`<repo-...-command>` placeholders), without hardcoded ecosystem defaults.
- [ ] `CONTRACTS.md` capability/workflow contract matrix remains unchanged unless a capability/workflow-local output contract actually changed.
- [ ] `migration/nextmig.md` portability defaults and verification fallback policy remain aligned with README and contracts.
- [ ] `workflows/portability-upgrade.md` guidance matches this checklist and does not introduce new status terms.

## Report format (docs-only)
1. Assumptions
2. Changes made
3. Unified diff
4. Verification commands + results
5. Risks/open concerns
6. Next steps (max 3)
