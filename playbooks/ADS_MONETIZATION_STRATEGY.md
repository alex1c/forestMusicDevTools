# Ads monetization strategy — ForestMusic international utility apps

## Goal
Increase sustainable ad revenue without disruptive placements, invalid traffic, policy rejection or losing retention. This is an **experiment-ready policy**, not a blanket instruction to fill every surface with ads.

## Shift Calendar proposed placements
- Small adaptive/sticky bottom banner on all practical user-facing screens including Calendar, Today, Statistics, More/Settings, notifications and stable editing screens, **only** where all controls remain visible and tappable.
- No ads on consent dialogs, privacy/terms documents, onboarding/training, system permission dialogs, in-app error flows or screens where safe layout is impossible.
- Share schedule: do **not** intercept the Share button with an unexpected interstitial. Finish document creation and system share interaction first; consider a full-screen ad only on an eligible natural transition **after** the user finishes or dismisses the share flow.
- Every 10–15 meaningful **cold launches**, make an interstitial **eligible** at the next natural break, *never* immediately on app start/splash or while opening a target action. Persistent counter increments only for a real user-started foreground session, not every activity resume/recomposition. Generate next threshold within range 10–15; deduplicate background/restarts.
- Global coordinator: share and launch-based triggers use the **same** cooldown, frequency cap, consent gate, load/show lifecycle and foreground/screen-safety check. Never stack ads back-to-back; prioritize the finished user action over promotional events.
- Keep first-run, onboarding, protected edit/export, keyboard, system share sheet, background and SDK failure protections. No ads if purpose is denied/invalid or no fill.
- No production ad-block IDs in screenshots, local debug or automated UI tests. Native SDK code must not cause blank reserved space when fill is absent unless layout explicitly requires it.

## Existing reference implementation
- `alex1c/shiftCalendarRuStore/src/ads/{adsSessionPolicy,interstitialController,AdsProvider,BannerAdSlot,adsConfig}.ts(x)`: 90 s and 4 interactions before interstitial; one per session; first-launch and protected-flow exclusions.
- `alex1c/forestMusicDevTools/playbooks/ADS_AND_BOTTOM_LAYOUT.md`: fixed bottom geometry and Android safe area.
- `alex1c/bloodPressureDiaryRuStore/src/ads/`: sticky banners, ad state tests, stale-UI and consent revoke guards.
- Advert IDs / permissions must be verified per **store flavor**. Keep production IDs centralized; never invent units or rely on a unit that does not fill.

## Policy QA and instrumentation
- Confirm against current [Google Play Ads policy](https://support.google.com/googleplay/android-developer/answer/9857753) and [Better ads experiences policy](https://support.google.com/googleplay/android-developer/answer/12271244).
- Tests: launch threshold 9/10/15/16, process restore, session restart, repeated app resume, abandoned share, share success, denied/revoked consent, ad not ready, no fill, SDK error, double-trigger race, protected flow, navigation/keyboard and physical-device safe area.
- Log ad eligibility, request, fill, show, failure and dismiss in a privacy-respecting manner, without shift names, notes, dates, salary or personal identifiers. Revenue and eCPM must come from trustworthy network reporting rather than speculative client-side calculations.
- Measure impressions per DAU, ARPDAU, banner fill, eCPM, retention D1/D7 and complaints by geography + version; compare policies in staged experiments. More requests do **not** guarantee higher ad revenue.

## Release guardrail
No new interstitial behavior ships until end-to-end UX tests and independent Codex audit pass. Every placement is reviewed separately per store and user region. Fix layout rather than hide broken controls.

## Owner decisions and physical-device reference (2026-11-27)
- Free apps are ad-supported by default in verified lawful/vendor-supported modes; no unnecessary global ad-off switch. See INTERNATIONAL_PRIVACY_AND_CONSENT.md for mandatory regional exceptions.
- Prefer standalone internal QA APK with embedded JS, separate QA application ID, demo ads and safe test analytics on OPPO CPH2687RU / Android 16. Avoid emulator/Metro troubleshooting unless needed for coverage. QA overrides must be impossible in production. Retain emulator for additional screen sizes and Android versions.
- Shift Calendar reference: branch feature/google-play-adaptation, commit 938f465 (reported Phase 3.3 PASS). Physical-device notice, demo banners, share interstitial, wipe and restricted-mode refusal verified per project report; 301 tests pass. Network packet capture NOT PASS; demo fill and logcat do not prove production inventory or privacy compliance. Launch threshold runtime coverage must be evidenced separately. Google Play ads remain disabled. Do not generalize the RuStore jurisdiction heuristic to international releases.
- Release checklist: verify developer-domain app-ads.txt HTTPS 200, plain text, complete vendor records, dashboard acceptance; preserve existing records when merging. Canonical ForestMusic URL https://forest-music.ru/app-ads.txt. Verify actual SDK version, Maven repository and resolved mediation graph; SDK upgrade needs native rebuild and runtime regression. Shift Calendar uses Yandex 8.7.0 and filtered https://ads.maven.yandex.net/ per project report.
- Defer optional VPN regional experiments until needed; known mandatory compliance and unresolved network checks remain release gates. Prefer documented vendor-supported mechanisms over custom GeoIP/CMP infrastructure.
