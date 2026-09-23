# Android QA Checklist

## Before startup

- [ ] `git status`, fetch and fast-forward pull completed.
- [ ] `npm test`, typecheck and lint pass.
- [ ] Native changes identified; choose normal mode or `-Build`.
- [ ] A real device is connected and USB debugging is authorized.

## Standard startup

- [ ] Run `.\scripts\android-device-qa.ps1`.
- [ ] Use `-Build` only for native/config/dependency changes.
- [ ] Use `-Logcat` for bounded app-scoped diagnostics.
- [ ] Script reports `FORESTMUSIC DEVICE QA STARTUP PASS`.
- [ ] Real application UI, not only the development-client screen, is visible.

## Physical UI

- [ ] Home and primary navigation open.
- [ ] Bottom safe area/gesture area leaves controls fully visible.
- [ ] Banner slot is reserved, bottom-attached and non-overlapping.
- [ ] Keyboard/number entry does not hide the save/confirm action.
- [ ] Back behavior is coherent.
- [ ] Dark mode and relevant permission flows do not crash.
- [ ] Game/work area is checked at its densest representative state.

## Failure handling

- [ ] Record the exact STOP layer and diagnostic.
- [ ] Diagnose only that layer.
- [ ] Do not switch ports, kill unknown processes or perform destructive
      cleanup as an improvised workaround.
