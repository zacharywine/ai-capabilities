# Capability & Workflow Contracts Matrix

This matrix captures each capability/workflow-local output contract and status vocabulary.

| Capability / workflow doc | Required headings/order | Status vocabulary | Overrides README schema? |
|---|---|---|---|
| `workflows/plan-delivery.md` | 1) Assumptions → 2) Milestone Slices → 3) Dependencies → 4) Risk Register → 5) Verification Strategy → 6) Exit Criteria | Dependencies keep planning axis (`ready`, `blocked`, `unknown`); command/check reporting uses `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/implement-change.md` | 1) Assumptions → 2) Scope / Invariants / Non-goals → 3) Smallest Safe Implementation Plan → 4) Code Change Summary → 5) Verification Commands / Results → 6) Risks → 7) Next Steps | `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/debug-fix.md` | 1) Reproduction recipe → 2) Expected vs actual → 3) Root-cause hypothesis list → 4) Minimal fix → 5) Targeted validation → 6) Regression guard → 7) Next steps (max 3) | `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/static-audit.md` | Severity groups in exact order: 1) `critical` → 2) `high` → 3) `medium` → 4) `low`; then brief Summary | Severity axis unchanged; if execution evidence is reported, include status `PASS`, `FAIL`, `BLOCKED`, or `UNKNOWN` with evidence | Yes |
| `workflows/test-strategy.md` | Ordered sections 1-5: Test pyramid mapping → Critical paths → Edge and error cases → Minimal suite for touched area → Command matrix with expected signals; plus unknown/blocked handling | `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/investigate-performance.md` | 1) Baseline → 2) Hotspots → 3) Ranked options → 4) Decision + next commands | Command/check evidence rows use `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |


## Portability references
- `CONTRACTS_PORTABILITY_CHECKLIST.md` is the docs-only consistency checklist for capability/workflow portability passes.
