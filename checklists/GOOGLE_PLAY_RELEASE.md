# Google Play release gate — ForestMusic

Follow [Google Play publication playbook](../playbooks/GOOGLE_PLAY_PUBLICATION.md) and [Independent review](INDEPENDENT_REVIEW.md). Required before **each app's** first Google Play release, including Hexonica.

## 0. Policy/account check — before build and listing

- [ ] Developer account type (personal/organization) recorded.
- [ ] Actual features reviewed against organization-only categories and specialized Play policies.
- [ ] Medical/finance/VPN/government/sensitive cases clarified before committing to launch.
- [ ] Target audience, ads/content and country restrictions classified truthfully.
- [ ] **POLICY ELIGIBILITY: PASS / NEEDS CLARIFICATION / BLOCKED.** Stop if unresolved.

## 1. Source and configuration

- [ ] Exact Git SHA, branch and clean tracked tree recorded.
- [ ] Independent Codex audit complete for substantial app/game.
- [ ] `APP_STORE=googleplay` and store-specific links/SDKs/consent checked.
- [ ] Package/applicationId, title, versionName/versionCode, target SDK and manifest/permissions verified.
- [ ] Data Safety categories assessed from *this app's* code and all third-party SDKs.
- [ ] Public privacy policy accurately describes collection, sharing, opt-out and deletion workflow.
- [ ] Store listing, screenshots, localization, category, content ratings, ads declarations verified.

## 2. Signed production artifact

- [ ] JKS/secure build settings checked locally; no secret exposure.
- [ ] Google Play App Signing vs upload key chosen with cross-store signing implications understood.
- [ ] Production AAB built, inspected (bundletool if available), signed; SHA256, package, version verified.
- [ ] Typecheck, lint, tests, Android device/AVD QA and consent/ads/offline tests PASS.
- [ ] R8 mapping/proguard diagnostics addressed if applicable.

## 3. Closed testing and submission

- [ ] Track, chosen countries, real tester opt-in method and feedback contact configured.
- [ ] Console testing requirement for this account verified (12 opted-in / 14 continuous days in 2026 BP Diary case; recheck).
- [ ] AAB processed, localized release notes complete, warnings analyzed.
- [ ] Publishing overview reviewed; preview checks complete; submitted for review.
- [ ] Approval, live closed-test opt-in link and actual tester enrollments confirmed (draft != live).
- [ ] Review status and any policy rejection recorded before next action.

## 4. Final proof

- [ ] Release report stored in app repo with evidence and outstanding risks.
- [ ] No declaration altered to sidestep policy; no fake tester counts; no secrets in Git.
- [ ] Verdict: READY / BLOCKED / REVIEW PENDING / REJECTED, with next exact action.
