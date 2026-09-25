# ForestMusic — Rewarded Help and Game Release Lessons

Canonical cross-project rules extracted from the physical Android QA and RuStore release of **Переливайка**.

These rules supplement `FORESTMUSIC_DEV_PLAYBOOK.md`, `ADS_AND_BOTTOM_LAYOUT.md`, `ANDROID_DEVICE_QA.md` and the RuStore release checklist.

## 1. Rewarded ad lifecycle

For Yandex Mobile Ads / React Native, do not make application UI recovery depend only on:

```ts
await ad.show()
```

A real Android device demonstrated that the native rewarded ad can close while the JS flow waiting on `show()` does not settle as expected. This can leave a modal permanently disabled.

Canonical model:

- verified reward callback is the **only** grant path;
- dismissal callback settles the UI flow but never grants;
- failed-to-show/load paths settle without granting;
- settlement is idempotent;
- reward grant is once-only even if callbacks repeat;
- add a conservative fail-safe so a broken native lifecycle cannot leave the app permanently locked;
- a fail-safe never invents a reward and never revokes a reward already verified;
- one rewarded request maps to exactly one reward action.

After every terminal path, loading/busy state must clear and Android Back/cancel must become usable again.

Physical-device QA must include: successful reward, dismiss/no reward, no-fill/failure where practical, and immediate interaction with the app after the ad closes.

## 2. Rewarded game help

Rewarded help must be voluntary and understandable before the ad starts.

Preferred ForestMusic game pattern:

- give useful free help first;
- after free help is exhausted, explicitly offer a rewarded pack rather than showing an ad automatically;
- one ad should unlock a useful pack (for example several hints), not force an ad before every hint;
- consume a hint credit only when a valid hint is actually produced;
- ad failure, solver cutoff, dismissal or internal error consumes no help credit;
- rewarded assistance must never be required to make a generated puzzle solvable.

For an extra-tube/extra-slot reward:

- maximum entitlement must be explicit (normally one per puzzle);
- verified callback only;
- Restart and Undo must not duplicate or revoke an earned entitlement;
- persistence must restore it exactly once;
- solver/hint logic must operate on the actual assisted board;
- history snapshots must remain dimensionally compatible after adding the assistance.

Help state belongs to a stable puzzle identity, not to a screen render. Restart/Undo must not be usable to farm initial free help.

## 3. Synchronous solver work on React Native

A `setTimeout(..., 0)` / UI yield before a synchronous solver does **not** move that solver off the JS thread.

For JS-thread search:

- use measured bounded FAST and STRONG passes;
- state/depth limits should normally terminate well before wall-clock timeout;
- do not use huge fallback budgets merely to avoid returning a cutoff;
- preserve the distinction between `found`, `cutoff`, `unsolvable`, `already_solved` and illegal-guard states;
- never invent a hint when a solver-confirmed continuation was not found;
- a failed/cutoff search must not consume a paid/free hint credit;
- benchmark deterministic representative HARD/EXPERT states and the densest assisted board;
- desktop timing is informational only: the worst representative hint path is a required physical-Android checkpoint.

## 4. Product rename and native rebuild

Changing Expo/app display name in source does not change the launcher label of an already installed native APK.

After changing a native display name, icon, permissions, native dependency or config:

1. regenerate native project when the workflow requires it;
2. rebuild the native APK/dev client;
3. reinstall it;
4. verify the installed label/version on the device.

Metro reload is not evidence that native metadata changed. If source says the new name but the phone still shows the old one, first suspect a stale native build rather than changing product identity again.

Never rename stable technical identities just because the product display name changed: package, URL scheme, persistence keys and deterministic seed namespaces remain stable unless migration is explicitly designed.

## 5. Screenshot mode after ads are integrated

If production ads are already integrated, use a DEV-only Screenshot QA Mode.

It must:

- suppress third-party banner requests/creative;
- preserve the exact banner-reserved geometry;
- preserve safe-area and footer/control positions;
- suppress automatic interstitials during capture;
- be unreachable in release;
- never fake advertiser content.

Keep untouched physical-device source captures separately from final store images.

For RuStore portrait media, verify the requirements shown by the current console. The verified 2026 workflow accepted/recommended **9:16, 1080×1920 PNG**. Do not stretch UI to reach the store size. If the physical master has a different aspect ratio, crop/fit deliberately without cutting important UI; recapture when conversion makes the product look worse.

Screenshot-only temporary source edits must be restored before finishing. If a tracked screenshot-mode commit is made after the final AAB source SHA, the previous AAB is no longer the final artifact and must be rebuilt.

## 6. Release artifact/source identity

A final AAB is tied to an exact clean source SHA.

Required release record:

- HEAD and origin/main;
- package;
- versionName/versionCode;
- exact AAB filename and byte size;
- AAB SHA-256;
- expected signer certificate SHA-256;
- final manifest permission audit.

Any tracked source commit after the AAB build invalidates the “final from this SHA” claim, even if the commit is described as DEV-only.

Untracked/ignored screenshots may be produced after the AAB only when production source and AAB bytes remain unchanged; re-check the AAB checksum afterwards.

## 7. Local production signing

Keep application signing outside the repository, for example:

```text
D:\secure\android-signing\<app>\
    <app>-release.jks
    keystore.properties
```

Automation (Codex/Cursor/local scripts) may read the local properties file to configure generated Gradle files, but must never print passwords, copy them into tracked source or require manual password transcription into the repository.

Verify the certificate fingerprint on the **produced AAB**, not only on the keystore.

Generated `android/` may be modified locally for signing when it is intentionally gitignored; do not create a source commit solely for local signing.

## 8. RuStore PEPK and upload certificate

When RuStore requests managed signing:

- use the existing production keystore and alias;
- use only the encryption key currently shown by RuStore for that application;
- generate PEPK with `--include-cert`;
- export the upload certificate separately as PEM;
- never upload the JKS itself;
- enter private passwords interactively and never paste them into reports/chat.

PEPK ZIP and upload-certificate PEM are different artifacts and must be uploaded to their corresponding RuStore fields.

## 9. Store-detected integrations

A RuStore integration label is evidence to investigate, not sufficient proof that the app actively serves that network.

If RuStore reports an unexpected network such as AdMob:

- inspect the release dependency tree;
- inspect the merged/final AAB manifest;
- distinguish Google advertising identifier/install-referrer libraries from the full Google Mobile Ads serving SDK;
- identify adapters/transitive SDKs before removing anything.

Do not break a working analytics/ads stack merely to make an automatically detected label disappear.

## 10. Physical release gate for game assistance

Before the first store upload of a game with rewarded assistance, physically verify on the intended release candidate:

- free hint(s);
- rewarded hint offer does not auto-open;
- successful rewarded closes and returns control to the game;
- failed/dismissed rewarded does not grant;
- extra assistance is granted exactly once;
- Undo after assistance;
- Restart after assistance;
- save/leave/restore after assistance;
- densest normal layout;
- densest assisted layout;
- Android Back after rewarded closes;
- bottom controls remain above banner and system navigation.

Automated tests do not replace this checkpoint.
