# Investigate Performance Workflow

## Router-first
- **Use when:** goal is to measure slowness/regression and choose the safest optimization path.
- **Do not use when:** goal is general feature delivery without performance evidence.
- **Typical chained workflows:** `workflows/plan-delivery.md` -> `workflows/implement-change.md`.
- **Required mode/inputs:** baseline metrics and collection commands must be provided or captured.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=investigate`; `Scope=endpoint/page/workflow implied by Goal`; `Constraints=baseline and comparison must use same method/environment`; `Definition of done=baseline metrics, hotspot evidence, ranked options, and route decision`.

## Required flow
1. **Baseline capture**
   - Record environment and baseline metrics with exact command(s).
2. **Hotspot evidence**
   - Identify top contributors using profiler/trace/log evidence.
3. **Ranked options**
   - Rank candidate optimizations by impact/risk/effort.
4. **Decision gate**
   - If fix is approved, route to `workflows/implement-change.md`.
   - If issue is failing behavior, route to `workflows/debug-fix.md`.
   - If blocked, return blocker and next exact command.

## Verification contract
Use this table for every measured or planned check:

| Command | Metric | Status | Evidence |
|---|---|---|---|
| `<exact command>` | `<latency/throughput/cpu/etc>` | `PASS|FAIL|BLOCKED|UNKNOWN` | `<key output>` |

## Output contract
Return in this order:
1. Baseline
2. Hotspots
3. Ranked options
4. Decision + next command(s)
