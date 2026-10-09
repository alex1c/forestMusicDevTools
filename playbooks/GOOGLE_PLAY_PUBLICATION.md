# Google Play — ForestMusic publication playbook

Status: lessons captured 2026-10-09 from the first closed-test submission of **Дневник давления**. Policies and Console screens can change: verify current official Google Play requirements before each submission.

## Mandatory gate 0 — eligibility BEFORE coding/building/store listing

- Identify developer account type: **personal** or **organization**.
- Evaluate the actual app features, content declarations, sensitive permissions, and category. Google Play Console Requirements can require an **organization account** for certain categories such as medical/health apps, financial services, VPN and government apps. Do not assume that a personal journal with medical data is exempt; obtain clarification first.
- For health-related features, complete the Health apps declaration truthfully; do **not** change categories/declarations just to evade restrictions.
- Validate access to closed/production tracks, age targeting, Families policy, advertising suitability, local laws, and relevant SDK policies.
- **STOP** and request a policy ruling if organization-account eligibility is uncertain. Do this before localization, AAB creation, screenshots, tester recruitment or review submission.
- If a rejection occurs, read the *exact policy and affected area* in the notification; technical PASS does not imply policy approval.

### Actual incident — BP Diary (October 2026)

- Package: `com.calculatorplatform.bpdiary`. Personal Play developer account, Google Play closed-alpha candidate `1.1.1` / versionCode `5`.
- Valid signed AAB uploaded, accepted by Console; testing release drafted and sent for review.
- Health declarations included **disease progression tracking** and **medication/treatment management**.
- Google **rejected the submission**: **“Violation of Play Console Requirements”**, affected area **Developer Account**. Their notice said some app types, including medical health apps, must be distributed via an organization account (for new accounts since 2024-08-31).
- A clarification/appeal was submitted; **no favorable decision or reinstatement is recorded**. Do not represent the app as approved.
- General lesson: a correctly signed, successfully processed AAB can still fail at account-policy review; preflight category/account eligibility first.
- Do not label all health/fitness apps as automatically organization-only; classification depends on features and current rules. Store review makes the ultimate determination.
- Ancillary experience: Yandex advertising also queried a medical-activity license; **ad-network classification is separate from Google Play policy**.

## Gate 1 — Google Play flavor and functionality

- Confirm package name and app title. For games like Hexonica, confirm appropriate Google Play category and declarations from actual gameplay.
- Inspect `APP_STORE=googleplay` behavior versus `rustore`: ads/analytics SDKs, store links, cross-promotions, in-app purchases, consent flow, required disclosures. Never silently assume RuStore behavior is Play-compliant.
- If Yandex Mobile Ads/AppMetrica is shipped: verify provider's current Google Play distribution and privacy requirements, initialize optional SDKs only as allowed by user consent, validate ads ID/permission and Data Safety categories. Google Play does not require AdMob solely because the store is Google Play; verify SDK suitability.
- Test offline and first launch, refusal/withdrawal of optional consent, ads/no-ads, app restart and local data persistence.
- Ensure privacy policy URL is publicly reachable, matches actual data flows and contact/deletion mechanisms. Health or other sensitive data kept locally should not be claimed as collected merely due to local storage, but investigate SDK/transmission behavior separately.
- Don't blindly copy BP Diary's Data Safety five groups into another app; perform an app-specific inventory of app + SDK data practices.
- Check current target API policy, native permissions, 64-bit support, app compatibility, content ratings, target audience, screenshots and translations.

## Gate 2 — security, signing, AAB, independent QA

- Confirm clean tracked Git tree, exact branch/HEAD SHA and independent Codex checkpoint per [Independent Review](../checklists/INDEPENDENT_REVIEW.md).
- Confirm `applicationId`, `versionName`, incremented `versionCode`, `targetSdkVersion`, production build type and actual `APP_STORE=googleplay` config.
- Use a dedicated secure signing configuration. Existing RuStore JKS **may** be reused as **upload key**, but Google Play App Signing may re-sign distributed APKs with a **different app-signing certificate**. Decide cross-store certificate/update compatibility *before first enrollment*. Never commit passwords/JKS or paste secrets into reports.
- Sign **release AAB**, not debug. Check artifact identity, manifest and permissions, certificate SHA-256, file SHA-256, bundle format, package/version via bundletool where available. If bundletool unavailable, report that limitation rather than claiming a complete bundletool check.
- Run typecheck, lint, tests, physical Android QA (ads + navigation + saves + consent), independent review; record results and blockers.
- Mapping/deobfuscation warning may be nonblocking, but if R8/ProGuard minification is enabled, archive/upload `mapping.txt` to make crashes and ANRs diagnosable.
- Keep release artifacts out of Git unless explicitly intended.

## Gate 3 — Console: declarations, listing, closed test

- Create application with correct developer profile, default language, category and support email.
- Complete App content *truthfully*: Privacy policy, Data Safety, ads, target audience, content rating, permissions, health/finance/other specialized declarations as relevant.
- Data Safety must match app + third-party SDK transmissions and consent: if optional data collection, verify both technical opt-out and declaration semantics. Review approximate location from IP and diagnostic SDKs as applicable.
- If a privacy policy offers deletion requests, align Console answers and provide whatever deletion-request URL/process Play requires. A support email alone does not automatically meet all Console fields. Document local-only deletion and data sharing separately.
- For a **new personal developer account**, plan for Google's currently applicable production-access testing requirement (in the 2026 BP Diary setup: **at least 12 testers opted in continuously for 14 days**); verify exact account requirements in Console. The clock is not started by merely preparing the draft release or listing email addresses.
- Set test countries/regions intentionally (avoid “all countries” by habit), tester email lists/Google Groups, feedback contact, opt-in URL. Tester accounts must be eligible and opt in; if using a recruitment service, verify real participation and compliant behavior. Do not promise paid/fake engagement.
- Closed testing release: upload AAB; release name and per-locale release notes; inspect processing results and warnings. Example:
  - `1.1.1 (5) - Closed Alpha`
  - `<ru-RU> ... </ru-RU>` and `<en-US> ... </en-US>` release notes.
- Save release; inspect **Publishing overview** of queued changes; wait for pre-review checks; submit for review. A successful AAB upload does **not** mean the release is published or the tester opt-in period has begun.
- Managed publishing settings determine when approved changes go live; confirm actual closed-track status and opt-in page.
- For a new app, track rejection, review state, test enrollment/retention and required production-access application; never claim production approval prematurely.

## Gate 4 — result and release record

Write `docs/google-play/GOOGLE_PLAY_RELEASE_REPORT.md` in the **app repository** with:
- Store, package, version, versionCode, branch/commit SHA, final AAB path/size/SHA256.
- `APP_STORE`, target SDK, build tools, signing **certificate fingerprint only**; Play App Signing plan; tests and device QA.
- Policy/account eligibility verdict, completed declarations, privacy URL, consent/SDK matrix.
- Review state: DRAFT / SENT_FOR_REVIEW / APPROVED_CLOSED_TEST / REJECTED, with notice reason and follow-up.
- Test track, geography, tester-list method, opt-in URL/status, exact real tester count and 14-day qualifying window if applicable.
- No secrets, no assumptions of approval from Console processing.

## Stop conditions

**BLOCKED** if account-category eligibility is unresolved; declarations contradict code/SDK data flows; signing/key integrity is uncertain; app cannot be tested; or Play flags a blocking policy issue. Do not “solve” medical restrictions by a cosmetic category/name change.

## Starter prompt for Cursor / Codex

> Before preparing this ForestMusic Android project for Google Play, read `forestMusicDevTools/playbooks/GOOGLE_PLAY_PUBLICATION.md` and `forestMusicDevTools/checklists/GOOGLE_PLAY_RELEASE.md`. First audit the Play developer account type against the app's actual category and features (including restricted medical, finance, VPN, government cases). Return POLICY ELIGIBILITY PASS / NEEDS CLARIFICATION / BLOCKED before building. Then independently audit store flavor, manifest, consent, data safety, SDKs, privacy policy, signing/Play App Signing, target SDK, localization, physical Android QA and closed-test requirements. Produce an evidence-based release report. Do not submit or upload without explicit approval.
