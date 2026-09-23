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
3. Copy [android-device-qa.ps1](scripts/android/android-device-qa.ps1) to
   `<project>/scripts/android-device-qa.ps1` or the project’s chosen `scripts/`
   location, preserving the project-root inference or passing `-ProjectPath`.
4. Work from [the canonical Playbook](playbooks/FORESTMUSIC_DEV_PLAYBOOK.md).
5. Use [the Android QA guide](playbooks/ANDROID_DEVICE_QA.md) before physical
   device validation.

Projects may record the exact version used, for example:
`ForestMusic DevTools: v1.0.0`.

## Normal Android startup

```powershell
.\scripts\android-device-qa.ps1
```

Use `-Build` after native/config/dependency changes and `-Logcat` for a
bounded PID-filtered diagnostic snapshot. The script is intentionally safe:
uncertain Git, device, Metro or install state stops with a diagnostic instead
of destructive recovery.

## Repository map

- [Playbook](playbooks/FORESTMUSIC_DEV_PLAYBOOK.md): full operational rules.
- [Android QA](playbooks/ANDROID_DEVICE_QA.md): startup layers and fallback
  diagnostics.
- [Ads and bottom layout](playbooks/ADS_AND_BOTTOM_LAYOUT.md): banner slots,
  safe area and physical layout rules.
- [Project bootstrap](checklists/PROJECT_BOOTSTRAP.md): new-project setup.
- [UX/ads checklist](checklists/UX_ADS_CHECKLIST.md): screen-by-screen review.
- [Android QA checklist](checklists/ANDROID_QA.md): repeatable device QA.
- [RuStore release checklist](checklists/RUSTORE_RELEASE.md): release gate.
- [React Native/Expo template notes](templates/react-native-expo/README.md).

## Version

This repository starts at version `1.0.0`; see [VERSION](VERSION).
