# RuStore Screenshot Workflow

This is the canonical workflow for authentic ForestMusic listing screenshots.
Plan it during product QA, before production ads are enabled.

## Capture clean masters early

Before production ad integration:

1. Pick 5–8 listing screens that explain the product and its strongest feature.
2. Navigate the app to realistic states on a physical device.
3. Capture the original screen files and keep them untouched in
   release-artifacts/screenshots/.
4. Record the device model and actual pixel dimensions.
5. Check that no debug, error, system-dialog, keyboard, or other transient UI
   appears.

This checkpoint preserves honest ad-free app states. It does not require
final store crops yet. If the app changes materially, recapture only affected
screens.

When ads are already integrated and a clean master is unavailable, use the
app's DEV Screenshot QA Mode if it provides one. It must retain the exact
production banner reservation, bottom safe area, and footer geometry while
preventing third-party ad requests. It must suppress automatic interstitials
during capture and be impossible to enter in release builds. Never collapse
the slot. Set the app-specific documented flag before starting Metro, then
restart Metro after changing the flag; the JS bundle will not reliably change
when only the shell environment is edited.

Example convention:

    $env:EXPO_PUBLIC_SCREENSHOT_QA_MODE = '1'
    npx expo start --dev-client --host lan --port 8081

To restore ordinary DEV ads:

    Remove-Item Env:EXPO_PUBLIC_SCREENSHOT_QA_MODE -ErrorAction SilentlyContinue
    npx expo start --dev-client --host lan --port 8081

Each app owns its implementation and exact banner-slot policy. Do not add a
release Settings switch for screenshot capture.

## Binary-safe physical capture

Prepare the desired screen manually, then capture the current device display:

    .\scripts\android\android-screenshot.ps1 -Name "03-game" -VerifyDimensions

By default, the file is written to
release-artifacts/screenshots/03-game.png. The helper does not launch or
navigate the app, press controls, or interact with ads.

On Windows PowerShell 5.1, do not redirect adb exec-out screencap -p to a
PNG file. PowerShell may corrupt binary output even when the file size looks
plausible. Use the helper or the equivalent safe sequence:

    adb shell screencap -p /sdcard/forestmusic-screen.png
    adb pull /sdcard/forestmusic-screen.png .\release-artifacts\screenshots\screen.png

Read actual source dimensions; do not assume every phone is 1080×2400.

## Prepare store assets

Keep source masters unchanged. Put ordered final assets in
release-artifacts/screenshots-rustore/. For a 9:16 portrait listing, the
canonical target is 1080×1920 PNG. A common 1080×2400 phone master is 9:20:
remove 480 vertical pixels with an individually chosen top/bottom crop.

- Crop at original pixel scale; never stretch or distort.
- Do not blindly center-crop. Preserve the title, product context, board,
  controls, keypad/number bank, selection cards, and important footer content.
- Remove dead space or system chrome only when it does not cut useful app UI.
- If the crop clips important content, recapture at a more useful app state.
- Do not retouch, blur, paint over, clone, or generatively remove an ad.
- Do not add overlays or fake UI.

Generate contact-sheet.png in the final folder for human review. It is a QA
artifact, not a store upload asset. Use ordered, meaningful filenames such as
01-home.png, 02-feature.png, and 03-game.png.

## Final visual and upload QA

For every final image, verify:

- PNG decodes and has the exact intended dimensions and aspect ratio.
- Text and important content remain readable and unclipped.
- No third-party ad, DEV label, test reminder, Metro screen, error overlay,
  permission dialog, or accidental keyboard is visible.
- Sequence tells the product story: identity first, strongest differentiator
  near the beginning, then core modes and retention features.
- Count and filenames match the intended upload set; no duplicate was added.
- The contact sheet contains the current final files and is not uploaded.

Prefer a product-specific crop/content review over a generic center crop. If a
screen cannot meet these checks, recapture it honestly.
