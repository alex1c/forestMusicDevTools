# ForestMusic Privacy Policy Center — reusable workflow

Status: deployed and visually verified 2026-10-14 for Hexonica (EN/RU). This is the canonical privacy-page publishing workflow for **new and existing** ForestMusic apps across RuStore and Google Play.

## Architecture (one deployment for the whole portfolio)

- App source remains in its own repository (for example `alex1c/conectCellsRustore`). App builds and releases are separate.
- **All privacy policy pages** live in `alex1c/ForestMusicLanding`, served from `https://forest-music.ru/privacy/`.
- The landing repository has **one** manual GitHub Actions workflow: `.github/workflows/deploy-timeweb.yml`. No per-app Actions, SSH keys, server users or GitHub Secrets are needed.
- Timeweb hosts static files under `/var/www/forest-music.ru/public`. Deployment builds Vite `dist/`, validates Apache DocumentRoot, creates a backup and uses rsync **without --delete**.
- Server-owned sections `apps/`, `web/`, `experience/`, `.well-known/` are excluded from rsync; do not change their permissions or overwrite them.
- Secrets are already configured in the **ForestMusicLanding** repository: `DEPLOY_SSH_HOST`, `DEPLOY_SSH_USER`, `DEPLOY_SSH_PRIVATE_KEY`, `DEPLOY_DEST_PATH`. Never copy their values to app repositories, logs or docs.

## URL convention

- Directory: `https://forest-music.ru/privacy/`
- English canonical app policy: `https://forest-music.ru/privacy/<app-slug>/`
- Russian translation (when provided): `https://forest-music.ru/privacy/<app-slug>/ru.html`
- Working example: `https://forest-music.ru/privacy/hexonica/` and `https://forest-music.ru/privacy/hexonica/ru.html`.
- In the app, prefer the **direct app policy URL**, not the policy directory. Keep store listing Privacy Policy URL consistent. Localized in-app links may point to translated pages.

## New app or existing-app update

1. Identify the app's real `applicationId`, stores, SDKs, ads/analytics, consent defaults, data stored locally, data actually transmitted/shared, retention, deletion/contact processes and languages. **Audit app code and SDK behavior first.** Do not reuse another app's disclosures blindly; especially distinguish health data and local-only data.
2. In `ForestMusicLanding`, create/update `public/privacy/<app-slug>/index.html` (EN) and `ru.html` if needed. Follow existing Hexonica structure and shared `public/privacy/privacy.css`; link the app in `public/privacy/index.html`. Include publisher, contact, effective date, data practices, third-party SDKs, user choices, deletion and store coverage as applicable. Do not publish placeholders for unaudited apps.
3. Validate URLs and navigation locally; run the landing repo's `npm ci`, `npm run typecheck`, `npm run lint`, `npm run build`. Confirm new policy files exist in `dist/privacy/`. Commit and push to `ForestMusicLanding/main`.
4. In GitHub, open `https://github.com/alex1c/ForestMusicLanding/actions/workflows/deploy-timeweb.yml` → **Run workflow** → branch `main` → confirmation **`deploy`**. This is manual, not automatic on push. Do not run while other production changes are unreviewed.
5. Require green **Build and deploy** including remote backup, rsync dry-run/exclusions, rsync and post-deploy HTTP checks. Check the new EN/RU URLs in a browser on desktop and mobile; verify CSS/fonts, directory link and relevant existing site paths. If red, inspect the failing step; rsync may have partially copied files. Do not blindly rerun or use destructive rollback.
6. In the **app repository**, update the privacy URL constant, Settings/About, consent screen and store listing to the published **direct** policy URL. Verify real-device links and locale fallback. Run app tests, independent Codex audit and Android QA before releasing. Record the landing SHA, app SHA and live URL in the release report.
7. When changing SDKs, data flows, consent or store distribution in an **existing app**, re-audit and update its policy **before** the app release; publish the site first, then ship the app. Keep the effective date accurate.

## Deployment incident and guardrails (2026-10-14)

- First Actions run failed with rsync exit 23: Vite `dist/` contained `apps/`, `web/`, `experience/` and the unprivileged deploy user could not write server-owned trees.
- Fix in `ForestMusicLanding` SHA `2e2f5b613da27afbaa2cd28022167a891d9e27e6`: `.github/deploy/rsync-exclude.txt`, rsync `-rltz --omit-dir-times --chmod=D755,F644`, **no --delete**, dry-run exclusion verification, backup and HTTP smoke checks.
- Manual workflow run #2 succeeded; user visually verified directory, EN and RU Hexonica policies.
- Never run `rsync --delete`, `git reset --hard` against the Apache DocumentRoot, recursive chmod/chown of shared site trees, or modify DNS/SSL/Apache for ordinary policy updates.
- Production website publishing is **not** an Android AAB/APK deployment.

## Definition of done

Policy content accurately matches the current app and SDKs; EN direct URL returns 200 (and RU if offered); directory lists the app; manual deploy succeeds; app/store URLs updated; consent/privacy links tested; audit and release gates satisfied.
