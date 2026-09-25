# Project Bootstrap Checklist

## Identity and source of truth

- [ ] Project name and local path recorded.
- [ ] GitHub repository created; GitHub is the source of truth.
- [ ] Package/application ID chosen and recorded.
- [ ] Expo/RN URL scheme chosen and verified.
- [ ] Cursor path and Codex/QA path recorded.
- [ ] DevTools version recorded, for example `ForestMusic DevTools: v1.1.0`.

## Foundation

- [ ] React Native/Expo stack and native requirements recorded.
- [ ] Strict TypeScript enabled.
- [ ] Test and lint commands work before feature work.
- [ ] Safe-area/window-inset foundation exists.
- [ ] Theme foundation exists.
- [ ] Onboarding/training route is planned.
- [ ] About screen and active `Other our apps` link are planned.
- [ ] Screen-by-screen BannerSlot plan is recorded.
- [ ] Banner geometry is reserved before ad SDK integration.
- [ ] Expected banner/interstitial/rewarded placements are decided; gameplay
      and onboarding/training treatment is explicit.
- [ ] Natural reminder use case is explicitly accepted or rejected.
- [ ] Privacy policy scope and public URL owner are planned before analytics
      or advertising release.
- [ ] Store icon and screenshot capture strategy are planned.
- [ ] Clean screenshot masters will be captured before production ad IDs.
- [ ] Release permission allowlist and permission-source audit are planned.
- [ ] Other our apps destination is configured and verified.

## QA and safety

- [ ] Copy `scripts/android/android-device-qa.ps1` into the project’s
      scripts directory.
- [ ] Copy the project-local QA README where useful.
- [ ] Confirm the script detects package, version and scheme instead of using
      values from another project.
- [ ] Confirm one Metro on 8081 and `--host lan` behavior.
- [ ] QA distinguishes current-project, other-project and unknown Metro
      owners; never kills an unknown Node process.
- [ ] Copy/configure both the device QA and screenshot capture helpers.
- [ ] No signing secrets or production keystores are in the repository.
- [ ] Establish the first physical Android device QA checkpoint.

## Delivery discipline

- [ ] Use root cause -> minimal fix -> regression test for serious defects.
- [ ] Promote cross-project lessons to ForestMusic DevTools.
- [ ] Keep application-specific lessons in the application repository.
