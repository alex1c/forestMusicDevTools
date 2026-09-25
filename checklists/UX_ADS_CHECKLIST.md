# UX and Ads Checklist

For every screen:

| Screen/category | Banner expected? | Reserved early? | Bottom/safe area checked? | Critical interaction fits? | Physical Android checked? |
|---|---:|---:|---:|---:|---:|
| Home | [ ] | [ ] | [ ] | [ ] | [ ] |
| Settings | [ ] | [ ] | [ ] | [ ] | [ ] |
| Reminder/Notifications | [ ] | [ ] | [ ] | [ ] | [ ] |
| About | [ ] | [ ] | [ ] | [ ] | [ ] |
| Training/tutorial | [ ] | [ ] | [ ] | [ ] | [ ] |
| Statistics | [ ] | [ ] | [ ] | [ ] | [ ] |
| Levels/lists | [ ] | [ ] | [ ] | [ ] | [ ] |
| Game/work area | [ ] explicit decision | [ ] | [ ] | [ ] | [ ] |

Onboarding/training must not request a real ad or show an interstitial. Its
reserved geometry, if any, is an explicit UX choice.

## Review questions

- [ ] Is the banner visually attached to the usable bottom?
- [ ] Is the order `content -> banner -> safe area -> system area` correct?
- [ ] Does the banner avoid overlaying the board, keypad, CTA, keyboard or
      bottom navigation?
- [ ] Are important controls fully visible and tappable?
- [ ] Are real window/safe-area insets used instead of magic offsets?
- [ ] Is the `Other our apps` link present where suitable?
- [ ] If reminders exist, are they useful, configurable, cancellable and
      reconciled after state changes?
- [ ] If DEV Screenshot QA Mode exists, does it preserve the exact banner
      reservation and safe-area geometry while suppressing ad requests?
- [ ] Is screenshot mode guarded in DEV code and unreachable in release?
