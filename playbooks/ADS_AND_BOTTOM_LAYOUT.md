# Ads and Bottom Layout

## Default philosophy

ForestMusic apps generally reserve a small banner on suitable user-facing
screens. This includes Home, lists, statistics, Settings, Reminder/Notifications,
About and informational screens. Training/onboarding is not an ad surface;
keep its normal geometry if needed, but do not request a real banner or show an
interstitial there. An exception on other screens is a product decision, not
an accidental omission.

Onboarding/training should be replayable and must not become an ad surface.

## Reserve geometry before the SDK

Use a real `BannerSlot` (or equivalent reserved component) during UI work
before integrating advertising. It is a geometry contract, not fake ad
content. This protects lists, CTAs, bottom navigation, keyboards and safe-area
spacing from late advertising changes.
For dense game/work layouts, include the reserved slot in the vertical budget
before fitting the board and controls; do not append it after the content has
already been sized.

## Bottom relationship

The intended order is:

```text
CONTENT
  ↓
BANNER
  ↓
BOTTOM SAFE AREA / WINDOW INSET
  ↓
ANDROID SYSTEM NAVIGATION / GESTURE AREA
```

The banner must be visually attached to the bottom of the usable app area. It
must not float above the bottom, overlay content, hide under system navigation,
or push a critical control below the viewport.

## Work/game areas

Dense work areas need an explicit decision. A banner is not automatically
correct if it makes the interaction unusable. Any sticky work-area banner
requires the board, controls and keypad to be measured against its reserved
height.

## Safe area and physical verification

Use actual safe-area/window insets and content sizing. Do not use device-
specific `translateY`, `-32`, `+40` or similar repairs. Verify on a physical
Android phone that the last control row is visible and tappable, including
gesture navigation. A correct inset alone is insufficient if the parent layout
is too tall.

## Reminder screens

Reminder settings normally receive the standard bottom banner. The schedule
must remain configurable, cancellable and reconciled with product state; a
completed action should suppress a no-longer-useful reminder when the product
logic supports that behavior.

## Future SDK integration

The reserved slot may later become adaptive or SDK-backed, but the content,
safe area and interaction contract must remain explicit and physically tested.

## Screenshot QA geometry

When an app implements DEV Screenshot QA Mode, it must preserve the same
reserved banner height, footer position and safe-area relationship while
rendering neutral app-owned empty space. Do not mount/request third-party ads
in this mode; suppress automatic interstitials during capture. The mode must
be guarded by __DEV__ (or equivalent) and unreachable in release. Never
collapse the banner slot to make a screenshot fit.
