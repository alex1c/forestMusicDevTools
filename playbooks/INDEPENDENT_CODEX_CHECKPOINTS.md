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
