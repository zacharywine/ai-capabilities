# Evals

Minimal, human-readable eval fixtures for the workflows-first model.

## What these evals check

1. **Routing correctness** for goal-only inputs against the deterministic router in `README.md`.
2. **Output contract drift** against workflow-local contracts in `CONTRACTS.md`.

## Files

- `evals/routing.yaml` — golden routing cases (`input`, `expected_workflow`, `rationale`).
- `evals/contracts.yaml` — practical output-shape checks per active workflow.

## How to use

- Manual review: inspect each case and compare with current docs.
- Scripted review (optional): parse YAML and assert `expected_workflow` plus required heading/order markers.

These fixtures are intentionally lightweight and portable (no required harness/dependencies).
