# Independent Review Checkpoint

Generic ForestMusic release gate for substantial applications and games.

Green automated tests, static gates, and even a successful physical smoke pass
do **not** replace an independent architectural review when the product contains
lifecycle-sensitive systems.

## When this checkpoint is mandatory

Require an independent Codex (or equivalent outsider) code review before release
preparation when the project includes any of:

- persistence / migrations / save recovery;
- ads (banner / interstitial / rewarded) or analytics SDKs;
- complex navigation / AppState / background timers;
- payments or subscriptions (if used);
- sensitive release configuration (signing, permissions, privacy URLs).

## Canonical order

For substantial ForestMusic apps/games, before RuStore release preparation:

1. Implementation and tooling gates (lint, typecheck, tests, domain audits).
2. **Independent Codex code review** of the release candidate SHA.
3. Remediation of findings (especially Critical / High).
4. **Independent Codex re-review** of the remediation diff.
5. Physical Android QA on the re-reviewed SHA.
6. Release preparation (version bump, signing, screenshots, metadata).
7. Final independent review of **release-only** changes before upload.

## Status language

Use explicit project-status phrases:

| Status | Meaning |
|---|---|
| `INDEPENDENT REVIEW PENDING` | Candidate exists; independent review not yet done. |
| `INDEPENDENT REVIEW FINDINGS` | Review completed; Critical/High remain open. |
| `INDEPENDENT REVIEW PASS` | Critical OPEN = 0 and High OPEN = 0 on re-review. |

A project with unresolved Critical or High independent findings **must not** be
labeled:

- `READY FOR RUSTORE UPLOAD`
- `READY FOR RELEASE`

even if tests, expo-doctor, and physical smoke are green.

## Finding classification on re-review

For every original finding, the independent reviewer must classify:

- `CLOSED`
- `OPEN`
- `PARTIAL`
- `REGRESSED`

Critical and High must be `CLOSED` before release preparation proceeds.

The remediation author must **not** self-declare findings closed.

## Final release-diff review

Immediately before RuStore upload, require a short independent review of
release-only changes:

- version bump;
- signing / keystore usage;
- manifest / permissions;
- production ads / analytics configuration;
- Privacy URL HTTP 200;
- DEV gating removed from release paths;
- release AAB identity / signer / checksum;
- screenshot build configuration.

This is smaller than the full architectural audit but remains mandatory.

## Process lesson

A project can simultaneously have:

- green unit/integration tests;
- green static gates;
- a passing physical smoke session;

and still contain release-blocking lifecycle or persistence defects that only an
independent review surfaces. Treat independent review as a distinct gate, not as
a duplicate of automated testing.
