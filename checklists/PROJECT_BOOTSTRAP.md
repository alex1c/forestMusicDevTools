# Project Bootstrap Checklist

## Identity and source of truth

- [ ] Project name and local path recorded.
- [ ] GitHub repository created; GitHub is the source of truth.
- [ ] Package/application ID chosen and recorded.
- [ ] Expo/RN URL scheme chosen and verified.
- [ ] Cursor path and Codex/QA path recorded.
- [ ] DevTools version recorded, for example `ForestMusic DevTools: v1.0.0`.

## Foundation

- [ ] React Native/Expo stack and native requirements recorded.
- [ ] Strict TypeScript enabled.
- [ ] Test and lint commands work before feature work.
- [ ] Safe-area/window-inset foundation exists.
- [ ] Theme foundation exists.
- [ ] Onboarding/training route is planned.
- [ ] About screen and active `Other our apps` link are planned.
- [ ] Screen-by-screen BannerSlot plan is recorded.
- [ ] Natural reminder use case is explicitly accepted or rejected.

## QA and safety

- [ ] Copy `scripts/android/android-device-qa.ps1` into the project’s
      scripts directory.
- [ ] Copy the project-local QA README where useful.
- [ ] Confirm the script detects package, version and scheme instead of using
      values from another project.
- [ ] Confirm one Metro on 8081 and `--host lan` behavior.
- [ ] No signing secrets or production keystores are in the repository.
- [ ] Establish the first physical Android device QA checkpoint.

## Delivery discipline

- [ ] Use root cause -> minimal fix -> regression test for serious defects.
- [ ] Promote cross-project lessons to ForestMusic DevTools.
- [ ] Keep application-specific lessons in the application repository.
