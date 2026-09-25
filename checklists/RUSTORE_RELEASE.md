# RuStore Release Checklist

Use the [canonical screenshot workflow](../playbooks/RUSTORE_SCREENSHOTS.md)
for capture, crop, ordering, ad audit and count checks.

For games using rewarded hints/assistance, also apply
[Rewarded Game Release Lessons](../playbooks/REWARDED_GAME_RELEASE.md).


## Release order

1. Core product, safe area and measured layout.
2. Reserve banner geometry; decide reminder and onboarding behavior.
3. Complete functional physical-device QA.
4. Capture clean source screenshot masters before production ads are enabled.
5. Integrate monetization/analytics and validate production configuration.
6. Audit release permissions and native dependencies.
7. Recapture affected images in DEV Screenshot QA Mode if needed; prepare and
   review final 9:16 screenshots.
8. Freeze source at clean HEAD equal to origin/main; record the SHA.
9. Clear DEV/QA environment flags, rerun checks, then build the final AAB.
10. Audit the final AAB manifest, identity, signer and checksum.
11. Complete RuStore metadata/data/signing steps, then upload.

Any source commit after the AAB build, including a DEV-only tooling change,
requires another AAB build from the new final source SHA.

## Quality and product checks

- [ ] Tests, typecheck and lint pass.
- [ ] Native build/install and physical Android QA pass where applicable.
- [ ] The actual app UI is verified; device launch, PID or Metro status alone
      are not UI/gameplay pass.
- [ ] Reminder permission is requested only after an explicit user action.
- [ ] Reminder defaults OFF unless a product exception is documented;
      denied permission does not leave a false ON state.
- [ ] Reminder destination and foreground/background/cold-start routing are
      tested; duplicate navigation is prevented.
- [ ] Reminder notification itself has no advertising and does not trigger an
      immediate interstitial.
- [ ] Privacy URL is public, returns HTTP 200, and matches SDK/data behavior.
- [ ] Other our apps link resolves to the intended developer page.

## Clean screenshot masters and listing media

- [ ] 5–8 strongest screens were considered before production ads.
- [ ] Clean physical-device masters are preserved untouched.
- [ ] If recaptured after ads, DEV Screenshot QA Mode preserves exact banner
      reservation/safe-area geometry, requests no ads, and suppresses
      automatic interstitials; release cannot enter that mode.
- [ ] Final screenshot set has the intended count and filenames; no duplicate.
- [ ] Each image has the intended orientation/aspect ratio, exact dimensions,
      decodes, and has readable unclipped product UI.
- [ ] No screenshot contains third-party ads, DEV UI, Metro, errors, dialogs,
      or accidental keyboard.
- [ ] Contact sheet is reviewed but excluded from store uploads.
- [ ] Listing icon dimensions are validated; launcher icon is physically
      checked.
- [ ] App name/type, primary and optional secondary category, age rating,
      search tags, descriptions, support contact, website, and privacy URL are
      reviewed against the shipped product.

## Release permission audit

- [ ] Generate and inspect the RELEASE merged manifest; do not infer shipped
      permissions from a DEBUG APK.
- [ ] Keep a per-project expected permission allowlist. For every permission,
      record source, purpose, required status, and keep/remove action.
- [ ] Inspect permissions again from the FINAL AAB manifest with bundletool
      or equivalent and compare against the allowlist.
- [ ] Explicitly check SYSTEM_ALERT_WINDOW, READ/WRITE_EXTERNAL_STORAGE,
      RECORD_AUDIO, CAMERA, ACCESS_FINE/COARSE_LOCATION, READ/WRITE_CONTACTS,
      READ_PHONE_STATE, POST_NOTIFICATIONS, RECEIVE_BOOT_COMPLETED, VIBRATE,
      WAKE_LOCK, SCHEDULE_EXACT_ALARM, USE_EXACT_ALARM, and AD_ID.
- [ ] Remove unrelated permissions using source-of-truth Expo blocked
      permissions or a minimal config plugin/manifest rule; regenerate and
      re-audit the release manifest.
- [ ] Explain only sensitive permissions actually present in the final AAB.

| Permission | Source | Purpose | Required? | Action |
|---|---|---|---|---|
| Project-specific | dependency/config/feature | user-visible purpose | yes/no | keep/remove |

## Production configuration and integrations

- [ ] Production ad IDs and analytics keys pass existing release validation.
- [ ] No demo/placeholder production IDs or unintended ad SDKs are present.
- [ ] Resolve SDK identity from the dependency tree, manifest, native
      dependencies and final AAB, not only the store's integration label.
- [ ] Investigate an unexpected store-detected integration before changing
      dependencies; it may be an adapter, signature-based detection or a
      false/over-broad classification.
- [ ] Analytics uses coarse product metadata, never puzzle entries, answers,
      personal notes or arbitrary free text.
- [ ] RuStore data declarations match actual ads, analytics, identifiers,
      notifications, local/cloud storage and account behavior; distinguish
      local app data from third-party processing.

## Final source and AAB gate

- [ ] Tracked source tree is clean; HEAD equals origin/main; source SHA is
      recorded.
- [ ] Clear development-only environment flags before release build.
- [ ] Re-run tests, typecheck, lint and required catalog/project checks.
- [ ] Build with the external production keystore; no debug-signing fallback.
- [ ] Run bundletool validation where available.
- [ ] From the FINAL AAB verify package, versionName, versionCode, minSdk,
      targetSdk and complete permission set.
- [ ] Verify the AAB is signed by the expected alias and certificate SHA-1 /
      SHA-256. Never record passwords.
- [ ] Search the release output for known DEV-only controls; distinguish dead
      literal strings from reachable DEV behavior.
- [ ] Calculate SHA-256 after final verification. Write
      <artifact>.aab.sha256 and verify the sidecar matches byte-for-byte.
- [ ] Record filename, exact size in bytes, MiB, SHA-256 and source SHA.
- [ ] If any source change happens after build, rebuild and repeat the audit.

## Signing, secrets and local artifacts

- [ ] Private JKS, signing.properties, PEPK ZIP and upload certificate PEM
      stay outside the repository, under the project's secure signing path.
- [ ] Never print signing.properties or put passwords in commands, reports,
      screenshots, chat or logs.
- [ ] Generate PEPK only if RuStore requests it. Use the current encryption
      key shown for that app and the existing production key; enter passwords
      interactively. PEPK and PEM are separate outputs.
- [ ] Confirm uploaded screenshot count and filenames before continuing in
      RuStore; never upload the contact sheet.
- [ ] Keep expected local release artifacts under release-artifacts where
      appropriate. QA scripts may allow recognized artifacts, but must still
      block tracked source edits and unexpected files.
