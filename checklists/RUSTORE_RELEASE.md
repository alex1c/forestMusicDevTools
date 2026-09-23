# RuStore Release Checklist

## Quality gate

- [ ] Tests pass.
- [ ] Typecheck passes.
- [ ] Lint passes.
- [ ] Native build/install verification passes.
- [ ] Physical Android QA passes, including bottom safe area and banners.
- [ ] Notifications/reminders are tested if present.
- [ ] Ads and AppMetrica initialization are tested if integrated.

## Product and store honesty

- [ ] Permissions match the merged release manifest and real use.
- [ ] RuStore data declarations match actual data handling.
- [ ] Privacy policy URL returns HTTP 200 and matches the app.
- [ ] `Other our apps` link resolves to the configured developer page.
- [ ] No false AdMob/advertising dependency inference remains unexplained.
- [ ] Notification permission and cancellation behavior are coherent.

## Assets and metadata

- [ ] Master icon and Android derivatives are correct.
- [ ] Store screenshots are exactly 1080x1920, 9:16, without distortion.
- [ ] App name, package, version and versionCode are confirmed.
- [ ] AAB package/version are verified with bundletool or equivalent.

## Signing and artifacts

- [ ] Production keystore was created and retained by the user.
- [ ] No keystore/password/PEPK secrets are committed.
- [ ] Release alias and certificate SHA-1/SHA-256 are recorded and match.
- [ ] AAB SHA-256 is recorded.
- [ ] If PEPK is required, use the existing production keystore and the
      certificate/encryption parameters from RuStore.
- [ ] Final release artifacts are stored outside the source repository where
      appropriate.
