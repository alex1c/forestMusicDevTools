# React Native / Expo project notes

This is a guidance template, not a generated starter application.

For a new project:

1. Read the canonical Playbook and bootstrap checklist.
2. Configure the app name, Android package and URL scheme from project
   requirements; never copy another app’s identity.
3. Establish strict TypeScript, tests and lint before feature work.
4. Add the safe-area foundation, theme foundation and onboarding route early.
5. Decide reminder value, screen-by-screen banner placement and the About
   `Other our apps` link before polishing UI.
6. Reserve banner geometry before adding an ad SDK. Plan clean physical
   screenshot masters before enabling production ads; keep DEV screenshot QA
   modes geometrically identical and release-inaccessible.
7. Copy the Android device QA and binary-safe screenshot scripts and record
   the DevTools version used.
8. Plan the release permission allowlist, public privacy URL and expected
   signer before the final release phase.

Do not copy signing secrets, production keystores, package IDs, device
serials or application-specific storage into a new project.
