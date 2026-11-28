# Independent Codex checkpoints

Cursor is the primary implementation agent; Codex is the independent reviewer. Use read-only, targeted audits at major boundaries rather than after every small task. Pin baseline and target SHA, inspect actual diffs, revisit previous findings, run lightweight checks and distinguish reported evidence from independently verified results. No implementation, dependency upgrades, commits or expensive builds unless explicitly authorized.

## Default schedule for substantial projects

1. Architecture/infrastructure, monetization and localization foundation, before feature expansion.
2. Main feature expansion, before release preparation.
3. Pre-release source and policy audit, before production build.
4. Lightweight final artifact verification after build.

For small updates use a risk-based checkpoint. Add extra checkpoints for high-risk changes rather than following a rigid count. Cursor fixes findings; Codex rechecks material fixes. Reports include verdict, SHAs, P0/P1/P2 with file/line evidence, actual tests, unresolved blockers and whether the next phase may begin. Separate development blockers from release blockers. Do not mark missing runtime or network evidence as PASS.

## Shift Calendar reference

Checkpoint A after Phase 4, target 0deb408, previous independent baseline 3ed9080. Checkpoint B after Phase 5 feature expansion, C after release preparation, D after AAB. Keep Google Play production ads disabled until applicable release gates are satisfied.

## Regression evidence and acceptance (2026-11-28)

- Reproduce the original defect through production components and callbacks, mocking only external boundaries. Run the same regression before and after the fix; record actual exit codes. Contaminated mocks/timeouts are not proof of the intended failure. Independent reproductions may establish closure separately.
- PASS requires normal process exit 0, not just passing assertion counts. Await asynchronous act scopes, unmount renderers, and drain effects; do not hide warnings or force exit.
- Use one authoritative embedded build identity; conflicting QA flags must fail closed. Validate real resolver behavior, not source strings. Record APK source SHA, full artifact hash and device serial separately.
- UI language, default currency preference and persisted monetary denomination/scale are distinct. Existing salary values use persisted denomination in display, input and backups; never silently reinterpret values. PDF statistics use semantic row IDs and structured quantities/units.
- Recheck live lifecycle and consent immediately before interstitial show after asynchronous loading; stale state must not override newer protection flags.
- Full local reset coordinates all providers, pending writes, stale reads and native scheduling through one shared boundary. Block new conflicting mutations, drain writes and running reschedules before final storage scrub and notification cancellation, invalidate stale restoration, reset provider state, report cleanup failures truthfully, and release the gate safely. Internal reset operations must not deadlock on the gate. Verify normal operation after reset and ready/pending-consent state without restoring old grants.

Checkpoint A final source target c45545d8b9a68a1512405aee087adbf99046ba72: APPROVE WITH CONDITIONS; all original P1 findings closed by independent review. Remaining P2: consent readiness after stale restore and asynchronous act warnings. Phase 5 allowed; physical QA, packet capture (NOT PASS), embedded telemetry, international advertising, legal publication, signing and native artifact checks remain release gates. This is source approval only.
