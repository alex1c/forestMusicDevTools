# ForestMusic DevTools

Canonical development playbook, Android physical-device QA, reusable scripts,
bootstrap rules, layout/monetization guidance and release checklists for
ForestMusic Android/RuStore projects.

## Who uses it

- Cursor: primary implementation, UI and integration work.
- Codex: algorithmic checkpoints where useful, root-cause audits, Android QA
  and release verification.
- GitHub: source of truth for the project and for this DevTools repository.

## Start a new project

For a project such as `myNewAppRuStore`:

1. Reference this repository: <https://github.com/alex1c/forestMusicDevTools>.
2. Follow [PROJECT_BOOTSTRAP](checklists/PROJECT_BOOTSTRAP.md).
3. Copy [android-device-qa.ps1](scripts/android/android-device-qa.ps1) and
   [android-screenshot.ps1](scripts/android/android-screenshot.ps1) to the
   <project>/scripts/android/, preserving project-root inference or passing
   ProjectPath.
4. Work from [the canonical Playbook](playbooks/FORESTMUSIC_DEV_PLAYBOOK.md).
5. Use [the Android QA guide](playbooks/ANDROID_DEVICE_QA.md) before physical
   device validation.

Projects should record the exact version used, for example:
ForestMusic DevTools: v1.2.0.

## Windows PowerShell 5.1 compatibility

Canonical Android helpers are written for **Windows PowerShell 5.1** (Desktop),
the standard ForestMusic QA workstation shell. PowerShell 7 (`pwsh`) is **not**
required and must not be assumed.

Executable `.ps1` helpers under `scripts/` must stay **encoding-safe for PS 5.1**:
prefer ASCII-safe punctuation in script source (plain `-` instead of em/en dashes,
`->` instead of Unicode arrows, ASCII quotes). On many Windows installs,
PowerShell 5.1 loads UTF-8-without-BOM `.ps1` files using a legacy code page; a
single typographic character can turn into mojibake and cascade into
`ParserError` failures before any QA logic runs.

Validate helpers after edits:

```powershell
powershell -NoProfile -File .\scripts\android\validate-ps51-encoding.ps1
```

## Normal Android startup

```powershell
.\scripts\android\android-device-qa.ps1
```

Use `-Build` after native/config/dependency changes and `-Logcat` for a
bounded PID-filtered diagnostic snapshot. Tracked source changes, unexpected
files, or uncertain device/Metro ownership stop with a diagnostic. Recognized
local release artifacts do not block QA. The script never kills an unknown
process.

## RuStore screenshots

Use the canonical screenshot workflow at
playbooks/RUSTORE_SCREENSHOTS.md to capture clean source masters before
production ads, capture device screens without binary redirection, and prepare
reviewed 9:16 assets. The helper only captures the current screen; the
operator chooses and opens the app state.

## Google Play publication (mandatory policy gate)

Before preparing **any** Google Play release (including Hexonica), run the
[Google Play release checklist](checklists/GOOGLE_PLAY_RELEASE.md) and read the
[Google Play publication playbook](playbooks/GOOGLE_PLAY_PUBLICATION.md).
The first BP Diary closed-test AAB passed technical checks but was **rejected**
because its medical declarations required an organization developer account.
Account/category eligibility must therefore be validated **before building**.

## Repository map

- [Playbook](playbooks/FORESTMUSIC_DEV_PLAYBOOK.md): full operational rules.
- [Android QA](playbooks/ANDROID_DEVICE_QA.md): startup layers and fallback
  diagnostics.
- [RuStore screenshots](playbooks/RUSTORE_SCREENSHOTS.md): clean capture,
  binary-safe physical screenshots and final listing asset QA.
- [Ads and bottom layout](playbooks/ADS_AND_BOTTOM_LAYOUT.md): banner slots,
  safe area and physical layout rules.
- [Project bootstrap](checklists/PROJECT_BOOTSTRAP.md): new-project setup.
- [UX/ads checklist](checklists/UX_ADS_CHECKLIST.md): screen-by-screen review.
- [Android QA checklist](checklists/ANDROID_QA.md): repeatable device QA.
- [RuStore release checklist](checklists/RUSTORE_RELEASE.md): release gate.
- [Google Play publication](playbooks/GOOGLE_PLAY_PUBLICATION.md): policy eligibility, App Signing, Data Safety, closed testing, lessons from rejection.
- [Google Play release checklist](checklists/GOOGLE_PLAY_RELEASE.md): mandatory preflight and submission gates.
- [Android screenshot helper](scripts/android/android-screenshot.ps1):
  capture the currently visible device screen.
- [PS 5.1 encoding validator](scripts/android/validate-ps51-encoding.ps1):
  ASCII-safe / parser check for executable helpers.
- [React Native/Expo template notes](templates/react-native-expo/README.md).

## Version 1.2.0

See [VERSION](VERSION). Minor release: mandatory independent Codex review
checkpoint before RuStore release preparation for substantial apps/games
(persistence, ads, analytics, lifecycle). See
[Independent Review Checkpoint](checklists/INDEPENDENT_REVIEW.md).

## Version 1.1.1

See [VERSION](VERSION). Patch release: Windows PowerShell 5.1 encoding-safe
canonical `.ps1` helpers (remove typographic punctuation that broke PS 5.1
UTF-8-without-BOM parsing), plus an encoding/parser validation script and
explicit PS 5.1 support documentation.

## Version 1.1.0

See [VERSION](VERSION). This release consolidates reusable lessons for
project-aware Metro checks, white-screen diagnosis, binary-safe screenshot
capture, early clean masters and deterministic 9:16 assets, DEV Screenshot QA
Mode, reminder permission/deep-link QA, release permission and AAB audits,
RuStore signing, and local release-artifact handling.
