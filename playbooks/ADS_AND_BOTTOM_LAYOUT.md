# Ads and Bottom Layout

## Default philosophy

ForestMusic apps generally reserve a small banner on suitable user-facing
screens. This includes Home, lists, statistics, Settings, Reminder/Notifications,
About, training and informational screens. An exception is a product decision,
not an accidental omission.

## Reserve geometry before the SDK

Use a real `BannerSlot` (or equivalent reserved component) during UI work
before integrating advertising. It is a geometry contract, not fake ad
content. This protects lists, CTAs, bottom navigation, keyboards and safe-area
spacing from late advertising changes.

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
correct if it makes the interaction unusable. CrossMath’s product decision is
a sticky bottom game banner with the board, controls and keypad measured
against the reserved height.

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
