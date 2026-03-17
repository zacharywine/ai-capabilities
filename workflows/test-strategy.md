# Test Strategy Prompt

## Router-first
- **Use when:** goal is defining minimal, risk-based test coverage for a scoped change.
- **Do not use when:** goal is implementing code changes or performing static QA findings.
- **Typical chained prompts:** `workflows/feature-delivery.md` -> `workflows/qa-audit.md`.
- **Required mode/inputs:** touched area and critical user path(s).

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=strategy`; `Scope=touched area and critical path(s) inferred from Goal`; `Constraints=smallest risk-based suite that protects changed behavior`; `Definition of done=layered test plan, command matrix, and expected pass/fail signals`.

Use this when defining tests for any change. Keep scope tight and focus on risk.
## 1) Test pyramid mapping
Map tests to the smallest layer that gives confidence:

- **Unit (base, most tests):** pure logic, transforms, validators, utilities.
- **Integration (middle):** module + framework boundaries (API handlers, DB/repo layer, component + state, service wiring).
- **E2E/Smoke (top, few tests):** one or two user-critical journeys proving production wiring.

Rule: start at unit/integration; only add E2E when risk cannot be covered below.

## 2) Critical paths
List the must-not-break flows for the touched area:

- Happy path (primary user/business outcome).
- Authn/authz or permission gate (if present).
- Data write/read consistency path.
- External dependency boundary (payment, email, cache, queue, third-party API).

For each path, define: **entry point -> key assertion -> failure signal**.

## 3) Edge and error cases
Cover high-value non-happy paths only:

- Boundary inputs (empty, null, min/max, malformed).
- State conflicts (duplicate submit, stale version, race/retry).
- Dependency failures (timeout, 4xx/5xx, partial response).
- Security-relevant rejects (unauthorized, forbidden, invalid token).

Assert both behavior and observability (status code/message/log/metric) as appropriate.

## 4) Minimal suite for touched area
Pick the smallest safe suite that still protects changed behavior.

- Run tests directly related to changed files/modules first.
- Add one nearby integration test if crossing boundaries.
- Add/keep one smoke/E2E only for a critical user path.
- Avoid broad/full-suite runs unless change risk is wide.

Output a short rationale: why this suite is sufficient for this change.

## 5) Command matrix with expected signals
Document planned commands before running:

| Layer | Command | Scope | Expected pass signal | Expected fail signal |
|---|---|---|---|---|
| Unit | `<unit command>` | touched functions/modules | all tests pass; no regressions | assertion/coverage failure in touched logic |
| Integration | `<integration command>` | boundary behavior | endpoint/component contract passes | contract/status/data mismatch |
| Smoke/E2E | `<smoke command>` | critical user flow | journey completes with key UI/API checks | broken flow, timeout, key assertion failure |
| Static checks | `<lint/type command>` | touched area or repo standard | zero lint/type errors | lint/type error tied to change |

When reporting results, use one of:

- **PASS:** command/check executed and expected pass signal observed.
- **FAIL:** command/check executed and failed.
- **BLOCKED:** command/check could not run due to an external dependency or approval prerequisite.
- **UNKNOWN:** command/check could not be executed or confirmed in the current environment.

## UNKNOWN / BLOCKED guidance (required when blocked)
If execution is blocked (missing tool, CI-only secret, network restriction, unavailable service):

1. Mark the command **BLOCKED** (external prerequisite) or **UNKNOWN** (result not verifiable yet).
2. State exact blocker in one line.
3. Provide the exact command you would run in a normal environment.
4. State expected pass/fail signal so reviewers can validate quickly.

Example:

- `BLOCKED - <repo-e2e-command>`
  Blocker: test DB container not available in this environment.
  Would run: `<repo-service-start-command> && <repo-e2e-command>`.
  Expected signal: checkout flow completes; order persisted; no uncaught errors.
