# Android QA Checklist

For startup diagnosis, see [Android Device QA](../playbooks/ANDROID_DEVICE_QA.md).
For physical screenshot capture, see
[RuStore Screenshot Workflow](../playbooks/RUSTORE_SCREENSHOTS.md).

## Before startup

- [ ] `git status`, fetch and fast-forward pull completed.
- [ ] `npm test`, typecheck and lint pass.
- [ ] Native changes identified; choose normal mode or `-Build`.
- [ ] A real device is connected and USB debugging is authorized.

## Standard startup

- [ ] Run `.\scripts\android\android-device-qa.ps1`.
- [ ] Use -MetroPort only for a documented project-specific port override;
      default remains 8081.
- [ ] Use `-Build` only for native/config/dependency changes.
- [ ] Use `-Logcat` for bounded app-scoped diagnostics.
- [ ] Script reports `FORESTMUSIC DEVICE QA STARTUP PASS`.
- [ ] Real application UI, not only the development-client screen, is visible.
- [ ] 8081 is owned by this project's Metro; /status and process/root
      identity were checked before reusing an existing listener.
- [ ] Visible UI/bundle identity matches the intended project; launch and PID
      alone are not treated as UI pass.

## Physical UI

- [ ] Home and primary navigation open.
- [ ] Bottom safe area/gesture area leaves controls fully visible.
- [ ] Banner slot is reserved, bottom-attached and non-overlapping.
- [ ] Keyboard/number entry does not hide the save/confirm action.
- [ ] Back behavior is coherent.
- [ ] Dark mode and relevant permission flows do not crash.
- [ ] Game/work area is checked at its densest representative state.
- [ ] Physical screenshots use binary-safe screencap-to-device-file then
      adb pull; Windows PowerShell 5.1 redirection is not used.

## Failure handling

- [ ] Record the exact STOP layer and diagnostic.
- [ ] Diagnose only that layer.
- [ ] Do not switch ports, kill unknown processes or perform destructive
      cleanup as an improvised workaround.
- [ ] For a blank screen after Metro/PID pass: force-stop, relaunch the
      current project's dev-client deep link, watch for Android Bundled,
      then collect bounded app-PID logcat.
- [ ] Do not begin with dependency installation, cache clearing, clean
      prebuild, or app-data deletion.
