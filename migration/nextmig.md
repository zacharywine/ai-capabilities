# Migration Workflow Contract

## Portability defaults (repo/web-safe)
- `SourceStack`: `Express+Vite` (override per repo)
- `TargetStack`: `Next.js` (override per repo)
- `LedgerPath`: `docs/MIGRATION_GUIDE.md` (override per repo)
- `CutoverChecklistPath`: `docs/next-cutover-checklist.md` (override per repo)
- `LedgerSectionPattern`: `Migration ledger update (slice: …)` (override per repo)

Specialization note: defaults are tuned for Express+Vite -> Next.js web migrations, but this prompt is reusable by overriding the values above.

## Router-first
- **Use when:** goal is `TargetStack` migration slices, cutover/decommission gating, or `SourceStack` ownership moves.
- **Do not use when:** goal is generic feature delivery or non-migration bug fixing.
- **Typical chained prompts:** `workflows/test-strategy.md` -> `workflows/qa-audit.md`.
- **Required mode/inputs:** `Mode: triage|implement|cutover|qa` and `Scope: <route/feature>`.

## Minimal input contract
- **Required:** `Goal`
- **Optional:** `Mode`, `Scope`, `Constraints`, `Definition of done`.
- **Deterministic defaults when omitted:** `Mode=implement`; `Scope=one route/feature migration slice inferred from Goal`; `Constraints=preserve parity, keep slice reversible, no new ledger file`; `Definition of done=one valid slice completed with required verification evidence and ledger update notes`.

Migrate a `SourceStack` app to `TargetStack` in small, reversible slices.
## Entry point (solo-dev default)
- Use this file as the single entry point in Codex.
- Start each request with: `Mode: triage|implement|cutover|qa` and `Scope: <route/feature>`.
- If mode is omitted, default to `implement`.

Mode behavior:
- `triage`: classify scope/risk, define slice boundary, and decide go/split.
- `implement`: execute one valid migration slice end-to-end.
- `cutover`: run ownership flip/decommission gates mapped to `CutoverChecklistPath`.
- `qa`: read-only audit; no edits; report findings and UNKNOWN runtime checks.

## 0) Non-negotiables (must follow)
- Update migration ledger only in `LedgerPath` under existing `LedgerSectionPattern` sections.
- Cutover/decommission gates must reference `CutoverChecklistPath`.
- Do not create a new migration ledger file unless explicitly requested.
- If `LedgerPath` or `CutoverChecklistPath` does not exist, mark `BLOCKED` with exact next action to create/register it in-repo.
- Keep guidance concise and practical (KISS).
- Smallest safe change only; no new deps unless requested.
- Never invent APIs/flags/commands.

## 1) Operating mode
- Default: **plan + implement + verify** (not read-only unless explicitly QA/audit-only).
- For each slice: short plan → implement → verify → ledger update.

## 2) Source-of-truth order
1. Current user prompt/instructions
2. AGENTS.md in scope (nearest wins)
3. Repo scripts/config
4. Contract/registry files (routes, ownership, env schema, API contracts)
5. Tests
6. Docs

Conflict resolution: user prompt > AGENTS.md > executable code/tests > docs/comments.

## 3) Valid migration slice (hard gate)
A slice is valid only if all are true:
1. One boundary move (single route family/feature/contract area)
2. Fast rollback via config/ownership/feature gate
3. No unintended API/SEO/auth break
4. Bounded verification (smallest relevant suite + one critical smoke flow)
5. Traceable ledger update in `LedgerPath`

If not valid, split smaller.

## 4) Parity policy

### 4.1 Required parity now (P0, must PASS to flip ownership)
- Route renders correctly
- Critical CTA works
- Form flow parity where applicable (validation + success/error + destination/side-effects)
- No fatal runtime/client errors

### 4.2 Deferred parity (track as debt, not ownership blocker)
- P1: key analytics conversion events, baseline a11y, structured-data parity
- P2: pixel-perfect visual parity, full SEO parity/edge metadata

### 4.3 Visual parity definition
- Preserve structure, key content blocks, and CTA prominence.
- Smoke viewports (fixed set):
  - mobile (390x844)
  - tablet (768x1024)
  - desktop (1440x900)
- Pixel-perfect match is not required.

### 4.4 SEO policy (temporarily relaxed)
- Full SEO parity is not a hard gate right now.
- Must avoid major SEO safety regressions (canonical/noindex disasters).
- Log SEO gaps as deferred parity debt in ledger.

## 5) Verification requirements
Unless docs/comments-only, run:
1. lint
2. typecheck
3. unit/integration tests
4. e2e smoke for affected critical journey(s)

Command resolution order (portable):
1. Use repo standard pipeline/scripts first.
2. If absent, run the smallest relevant touched-area suite.
3. If still unavailable, mark `UNKNOWN` with exact blocker + exact next command.

Example fallback:
- If no `typecheck` script exists, run available typed checks (if any) from repo pipeline; otherwise mark `UNKNOWN` and record the exact missing script.
- If no `e2e` script exists, run the nearest manual smoke flow for the critical journey and record `UNKNOWN` for automated e2e until wired.

If docs/comments-only:
- Skip full test pipeline
- Run quick sanity check (format/links/spelling)

For UI-affecting slices:
- Include visual parity evidence against the Section 4.3 fixed viewport set (mobile/tablet/desktop) via code review and behavior checks.
- Do not require screenshots or before/after image comparison for parity decisions.

If any check can’t run: status `UNKNOWN` + exact blocker + exact next command + required environment/input.

## 6) Shared evidence/status standard
Use everywhere (gates, verification, checklist, ledger):
- Status: `PASS` / `FAIL` / `BLOCKED` / `UNKNOWN`
- Evidence: test name, exact command output, file path, artifact/screenshot/log, or config diff reference
- For `BLOCKED`/`UNKNOWN`: include blocker and exact next action

## 7) Per-slice required output format
1. Assumptions  
2. Gate status (with shared evidence format)  
3. Changes made (code-first)  
4. Verification commands + results (exact commands + statuses)  
5. Risks/open concerns  
6. Migration ledger updates (`LedgerPath`)  
7. Next steps (max 3)

## 8) Mandatory per-slice declarations
- One critical journey definition for impacted route family using this chooser rule:
  - select the highest-conversion CTA path in-scope
  - include one happy path and one failure path when form/auth exists
  - tie to one existing e2e/spec flow or a clearly named manual smoke flow
- One-line rollback instruction (exact key/file/flip)
- Out-of-scope list (explicitly exclude unrelated refactors)
- Deferred parity debt entries in ledger

Implementation floor:
- A migration slice should include at least one runtime ownership/code move.
- `docs-only` slices are allowed only when they unblock the next code slice within one working day; include the unblock reason.

Debt entry template:
- Domain (SEO/analytics/a11y/perf)
- Status
- Evidence
- Owner
- Target slice/date
- Next action

## 9) Completion criteria (hard)
Slice is complete only when:
1. P0 parity is `PASS`
2. Required verification statuses are present
3. Ledger is updated in `LedgerPath`
4. Next required gate references `CutoverChecklistPath`
5. Rollback path is documented

## 10) Frontend visual parity evidence policy
- Screenshots are optional and not required for migration parity decisions.
- Confirm visual parity through code parity checks (component structure, content blocks, CTA behavior, and styling logic) plus standard verification in Section 5.
- Use screenshot comparison only when both legacy and migrated captures are available and can be automatically diffed.

## 11) Unknown/blocked handling policy
- `BLOCKED` = cannot proceed due to external dependency/approval/environment prerequisite.
- `UNKNOWN` = check/result not yet verifiable in current environment.
- Every `BLOCKED`/`UNKNOWN` item must include:
  1. exact blocker
  2. exact command or action to resolve next
  3. required environment/input
  4. owner

## 12) Cutover/decommission gate policy
- Any ownership flip that affects runtime serving path must map to a gate in `CutoverChecklistPath`.
- Decommission actions (removing legacy route handlers/pages) require:
  - parity evidence for affected routes
  - rollback path documented
  - gate status recorded with shared evidence format.
- If decommission is deferred, record as deferred parity debt in `LedgerPath`.

## 13) PR/status artifact requirements
Each PR/status update must include:
1. slice name and boundary
2. touched files (grouped by route/feature)
3. rollback instruction
4. verification table (command + status + evidence)
5. ledger update snippet location (`LedgerPath` section heading)
6. link/reference to `CutoverChecklistPath` gate entry

## 14) Risk controls for migration slices
- Do not bundle unrelated refactors into a migration slice.
- Preserve API contracts unless explicitly in slice scope.
- Preserve auth/session behavior unless explicitly in slice scope.
- Preserve canonical route behavior unless explicitly in slice scope.
- If risk of architecture/security/data rework is discovered, ask exactly one focused question before proceeding.

## 15) Final response format contract (assistant output)
Every implementation update must end with:
1. Assumptions
2. Gate status
3. Changes made
4. Verification commands + results
5. Risks/open concerns
6. Migration ledger updates
7. Next steps (max 3, exact commands where relevant)

Also include:
- explicit statuses (`PASS`/`FAIL`/`BLOCKED`/`UNKNOWN`)
- no invented commands or flags
- if checks weren’t run, mark `UNKNOWN` with exact reason and next command.

## 16) Prompt evolution policy
- Keep this prompt as a living contract.
- Any new rule should be additive, minimal, and non-conflicting with:
  1. user instructions
  2. AGENTS.md
  3. executable tests/config
- If conflicts appear, apply precedence:
  user prompt > AGENTS.md > code/tests > docs/comments.

## 17) Copy/paste assistant output template
Use this exact structure for implementation updates:

```md
1. Assumptions
- ...

2. Gate status
- [gate-name]: PASS|FAIL|BLOCKED|UNKNOWN — evidence: <file/log/command output>

3. Changes made
- <code-first summary>

4. Verification commands + results
| Command | Status | Evidence |
|---|---|---|
| `<exact command>` | PASS|FAIL|BLOCKED|UNKNOWN | <key output/log path> |

5. Risks/open concerns
- ...

6. Migration ledger updates
- `LedgerPath` → `LedgerSectionPattern`

7. Next steps (max 3)
- `<exact command>`
```
