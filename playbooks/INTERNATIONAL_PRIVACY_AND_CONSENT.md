# International privacy and consent — ForestMusic

## Purpose
Reusable **implementation checklist**, not a ready-made legal guarantee. Applies to Shift Calendar Google Play and subsequent international apps. Current policies must be verified per release, country, distribution channel, and actual embedded SDK versions.

## Reuse map
- Canonical policy publishing workflow: [PRIVACY_POLICY_CENTER.md](PRIVACY_POLICY_CENTER.md) using `alex1c/ForestMusicLanding`; review existing Hexonica EN/RU legal page as a formatting baseline, not as a substitute for an app-specific data inventory.
- Consent reference implementation: `alex1c/bloodPressureDiaryRuStore/src/privacy/` including consent context, persisted decisions, SDK initialization gate, consent revocation and tests. Its 1.1.1 Google Play implementation *does not* restore persisted grants on cold start and explicitly does not contain a Google-certified CMP; assess product UX and jurisdictional requirements before reuse.
- Store separation: `alex1c/bloodPressureDiaryRuStore/src/config/{active-store,store}.ts`.
- Release verification: [GOOGLE_PLAY_PUBLICATION.md](GOOGLE_PLAY_PUBLICATION.md) and [../checklists/GOOGLE_PLAY_RELEASE.md](../checklists/GOOGLE_PLAY_RELEASE.md).

## Non-negotiable audit first
1. Record app package, release variants, age/target audience, locales, regions, accounts, SDKs and mediation adapters. Do not copy disclosures from another app without mapping real data flows.
2. Create a data inventory distinguishing (a) device-only shift cycles, notes, salary estimates, exports/backups and (b) transmitted identifiers, IP, diagnostics, events and ad requests. Review analytics allowlist for sensitive content.
3. For each SDK, document **initialization time**, automatic native initialization, pre-consent requests, purposes, identifiers, data recipients, retention/deletion capabilities and actual network behavior. User-requested PDF/image share is distinct from developer data collection.
4. Check current Google Play User Data, Ads, Data Safety and target-audience policies alongside GDPR/ePrivacy, UK GDPR/PECR, Swiss FADP, US state privacy laws and other marketed jurisdictions. Any regional consent/opt-out design must reflect actual laws and vendor requirements. Do not label a custom first-party switch as certified CMP.
5. If Google AdMob/Google demand/mediation is enabled, reassess applicable Google-certified CMP / TCF requirements; do not assume Yandex-only consent handling covers Google inventory. Check location and cross-border data transfer requirements with competent advice where material.

## Recommended consent architecture
- Separate advertising availability, personalization, SDK processing and application analytics purposes; no pre-ticked boxes; allow refusal and revisit/withdrawal without blocking essential offline shift calendar functions.
- Default to optional SDKs disabled until a verified lawful and vendor-supported mode has been determined. Disable native auto-initialization and test pre-consent network behavior, not only JS flags.
- Define persisted vs session-only consent intentionally: a persisted, valid, revocable choice can be restored when permitted, after checking its version/scope. Do not force a dialog on every launch without a reason.
- Pass Yandex user-consent signals according to **current React Native SDK documentation** when decision is known and on later starts; withdrawal blocks new loads/events and invalidates in-flight UI operations, while vendor server retention must be described honestly.
- Plan a real CMP integration if the chosen countries/vendors demand it. No dark patterns; consent status should never be misrepresented.

## Publication model
- Use `alex1c/ForestMusicLanding/public/privacy/<app-slug>/` with an EN canonical page and locale translations. Follow [PRIVACY_POLICY_CENTER.md](PRIVACY_POLICY_CENTER.md) for the manual reviewed deployment.
- Adapt a **structure**, not another company's text: developer identity, effective date, scope/store differences, local data, vendor data, lawful choices, retention, deletion request path, children, cross-border transfers, rights, contact and updates.
- Validate displayed policy against the precise Play AAB + RuStore flavor, Google Play Data Safety, settings links, and ad consent text. Do not assert deletion from Yandex systems unless supported.
- Publish policy before app submission. Record links, versions, file hashes, evidence and review date.

## Acceptance checklist
- [ ] SDK / data-flow matrix supported by implementation or current vendor docs.
- [ ] Verified pre-consent behavior and valid withdrawal UX on physical device.
- [ ] Separate analytics/advertising choices; appropriate regional mechanisms.
- [ ] Explicit AdMob/CMP decision for any mediated Google demand.
- [ ] Actual privacy URLs 200, accurate EN and supported translations.
- [ ] Play Data Safety, ad declaration, content rating and store flavor agree with AAB.
- [ ] No copied competitor legal text, no invented vendor practices, no false legal guarantees.

## Owner policy — 2026-11-27
Free apps are advertising-supported. Enable ads by default only where verified lawful and vendor-supported; no unnecessary universal ad-off switch. Non-personalized ads are not inherently consent-free. Required refusal and withdrawal must remain accessible. Unknown jurisdiction fails closed; no GPS, and language/SIM/timezone/store alone are insufficient. No external GeoIP or paid CMP without owner approval. First-party consent is not certified.
Persist grants before SDK activation; revoke immediately, fail closed on write errors, reject missing or mismatched versions, guard stale callbacks, explicitly deny pending consent where supported. Audit embedded ads telemetry separately. Inventory local storage, notifications, backup and device transfer; disclose export and vendor deletion limits. Device/network evidence is mandatory before release; logcat alone is not network proof.
