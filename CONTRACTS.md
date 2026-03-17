# Prompt Contracts Matrix

This matrix captures each prompt-local output contract and status vocabulary.

| Prompt | Required headings/order | Status vocabulary | Overrides README schema? |
|---|---|---|---|
| `migration/nextmig.md` | 1) Assumptions → 2) Gate status → 3) Changes made → 4) Verification commands + results → 5) Risks/open concerns → 6) Migration ledger updates → 7) Next steps (max 3) | `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/delivery-plan.md` | 1) Assumptions → 2) Milestone slices → 3) Dependencies → 4) Risk register → 5) Verification strategy → 6) Exit criteria | Dependencies keep planning axis (`ready`, `blocked`, `unknown`); command/check reporting uses `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/feature-delivery.md` | 1) Assumptions → 2) Scope / Invariants / Non-goals → 3) Smallest Safe Implementation Plan → 4) Code Change Summary → 5) Verification Commands / Results → 6) Risks → 7) Next Steps | `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/debug-fix.md` | 1) Reproduction recipe → 2) Expected vs actual → 3) Root-cause hypothesis list → 4) Minimal fix → 5) Targeted validation → 6) Regression guard → 7) Next steps (max 3) | `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/qa-audit.md` | Severity groups in exact order: 1) `critical` → 2) `high` → 3) `medium` → 4) `low`; then brief Summary | Severity axis unchanged; if execution evidence is reported, include status `PASS`, `FAIL`, `BLOCKED`, or `UNKNOWN` with evidence | Yes |
| `workflows/test-strategy.md` | Ordered sections 1-5: Test pyramid mapping → Critical paths → Edge and error cases → Minimal suite for touched area → Command matrix with expected signals; plus unknown/blocked handling | `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/perf-investigation.md` | Ordered sections 1-5: Baseline Metric Capture → Hotspot Identification → Candidate Optimizations → Minimal Change Patch Plan → Post-Change Comparison Format; then Output Checklist | Command/check evidence rows use `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
| `workflows/refactor-safe.md` | 1) Scope declaration → 2) Slice plan → 3) Risk checklist (before) → 4) Implementation notes → 5) Verification results → 6) Risk checklist (after) → 7) Rollback notes | Verification command reporting uses `PASS`, `FAIL`, `BLOCKED`, `UNKNOWN` | Yes |
