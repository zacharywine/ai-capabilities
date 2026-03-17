# Performance Investigation Prompt

## Router-first
- **Use when:** goal is to investigate and measure performance bottlenecks with before/after evidence.
- **Do not use when:** goal is general feature delivery or behavior-only refactors.
- **Typical chained prompts:** `workflows/delivery-plan.md` -> `workflows/feature-delivery.md`.
- **Required mode/inputs:** baseline metrics and commands must be provided or captured.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=investigate`; `Scope=endpoint/page/workflow named or implied by Goal`; `Constraints=capture baseline and comparison with same method/environment`; `Definition of done=baseline metrics, hotspot analysis, ranked options, and post-change comparison or explicit blocked status`.

Use this template to run a focused performance investigation with measurable evidence.
## 1) Baseline Metric Capture

Define the baseline before any changes.

- Goal and scope (feature, endpoint, job, page, or workflow).
- Environment details (hardware, runtime, build mode, dataset size, config).
- Primary metrics (e.g., latency p50/p95, throughput, CPU, memory, I/O).
- Baseline run plan (sample size, warmup, repetition count, time window).

**Required evidence (for every baseline metric):**

| metric | source | command | status | time window |
|---|---|---|---|---|
| _exact metric name/value_ | _tool/log/dashboard_ | `_exact command used_` | `PASS|FAIL|BLOCKED|UNKNOWN` | _start-end or duration_ |

## 2) Hotspot Identification

Identify where time/resources are spent.

- Use profiling, tracing, logs, query plans, or flamegraphs.
- List top hotspots by contribution (% time, CPU, memory, calls).
- Include confidence level and known uncertainty.

**Required evidence (for each hotspot):**

| metric | source | command | status | time window |
|---|---|---|---|---|
| _hotspot metric/value_ | _profiler/trace/log source_ | `_exact command used_` | `PASS|FAIL|BLOCKED|UNKNOWN` | _start-end or duration_ |

## 3) Candidate Optimizations (Ranked by Impact/Risk)

Produce small, testable options and rank them.

| rank | optimization | expected impact | risk | effort | rationale |
|---|---|---|---|---|---|
| 1 | _small change_ | _High/Med/Low + metric target_ | _Low/Med/High_ | _S/M/L_ | _why this first_ |

Ranking rule:
- Prefer **high impact + low risk + low effort**.
- Defer high-risk changes unless baseline/hotspots demand them.

**Required evidence (for impact estimate):**

| metric | source | command | status | time window |
|---|---|---|---|---|
| _estimated metric delta basis_ | _prior run / benchmark / trace_ | `_exact command used_` | `PASS|FAIL|BLOCKED|UNKNOWN` | _start-end or duration_ |

## 4) Minimal Change Patch Plan

Plan the smallest safe patch first.

- Patch scope: exact files/components to touch.
- Implementation steps (ordered, minimal).
- Safety checks: tests, canary scope, rollback trigger.
- Non-goals: what is explicitly out of scope.

**Required evidence (for readiness/risk controls):**

| metric | source | command | status | time window |
|---|---|---|---|---|
| _guardrail metric or check_ | _test/log/monitor source_ | `_exact command used_` | `PASS|FAIL|BLOCKED|UNKNOWN` | _start-end or duration_ |

## 5) Post-Change Comparison Format

Compare post-change vs baseline in the same environment and method.

| metric | baseline | post-change | delta | significance | source | command | status | time window |
|---|---:|---:|---:|---|---|---|---|---|
| _metric name_ | _value_ | _value_ | _% / absolute_ | _confidence/stat note_ | _tool/log/dashboard_ | `_exact command used_` | `PASS|FAIL|BLOCKED|UNKNOWN` | _start-end or duration_ |

Decision rule:
- Accept only if primary metric improves and guardrails do not regress beyond threshold.
- If inconclusive, rerun with larger sample and document uncertainty.

## Output Checklist

- [ ] Baseline captured with required evidence fields.
- [ ] Hotspots identified with required evidence fields.
- [ ] Optimizations ranked by impact/risk.
- [ ] Minimal patch plan defined.
- [ ] Post-change table completed with baseline vs post-change.
