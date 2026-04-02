# Idea #56: (Improved)
## Version: v3
**Date:** 2026-04-03 05:24
**Status:** improved
## Original Idea
LensLock is a health app that copies the Alarmy model but applies it to contact lens anxiety instead of waking up.
## What Changed and Why
- Reframed the product from an always-on lens management app into a 14-night habit-correction utility for one narrow segment: monthly reusable soft-lens wearers who sometimes fall asleep in lenses. This fixes the market and retention problem by making the product a short, testable behavior-change tool instead of a fake broad health app.
- Cut wear-time math, morning start logs, missed-log reconstruction, weekly summaries, and multi-schedule support from v1. This fixes the biggest product weakness from the critique: too much fragile workflow and too much copy burden for a product whose real job is simply getting users to confirm lens removal before sleep.
- Made the core value a nightly unresolved closeout loop with a visible 14-night scorecard, not reminders plus garnish. This is better than the prior version because it creates a concrete reason to return in week 2 and gives the builder a clear pass/fail metric: do users complete the bedtime closeout for 10-14 nights, or not?
## Improved Description
LensLock should become a brutally simple 14-night compliance tool for monthly reusable contact lens wearers who sometimes go to bed with lenses still in. The product promise is no longer "track your lens health" or "measure wear risk." It is: `Before you sleep, confirm your lenses are out.` On first launch, the user sets only a bedtime window and notification permission. Every night, the app creates one unresolved closeout task. The bedtime notification opens a single decision screen with three choices: `Lenses out`, `Still in`, or `Skip tonight`.

If the user taps `Lenses out`, the night is marked safe and the streak/score advances. If the user taps `Still in`, the app snoozes and sends another reminder after a short interval until the user clears it or explicitly skips. If they never clear it, the next morning the app marks that night as missed. There is no fake precision, no duration math, and no annoying reconstruction flow. The product measures only the one behavior that matters for this niche: did the user close out before sleep or not?

This version is structurally better because it stops pretending the app has a durable moat or a broad health-management future. It is a focused validation product with one job and one testable outcome. The dedicated-app case is also clearer now: generic reminders can ring, but they do not maintain a purpose-built unresolved nightly state, a one-tap closeout flow, and a 14-night compliance score in the same place. That differentiator is still demand-risk, so it is not the moat; it is the hypothesis being tested.

3 biggest assumptions that could kill the product:
- VERIFIED: A solo developer can ship an iOS-only or Android-only version with local notifications, local state, one bedtime flow, and a 14-night scorecard in 2-4 weeks. The critique explicitly supports a brutally small single-platform MVP.
- UNVERIFIED: Users who have this habit will actually complete a bedtime confirmation flow for 10-14 nights instead of ignoring it like a normal reminder. This is the main product risk. Fallback plan: if completion is weak in a tiny pilot, stop building and package the concept as a lighter Shortcut/template instead of expanding the app.
- FALSE: Accurate wear-time tracking is necessary for this product to feel valuable. The critique made clear that missed logs and reconstruction make the core value mushy, so wear-time math is removed from v1.

Why would users come back in week 2? Because the app is framed as a 14-night challenge, not an endless utility. Users return to keep the challenge alive, avoid a missed night, and finish with a clean scorecard. If testers do not care about completing that 14-night run, retention is weak and confidence in the idea should drop sharply.
## Why This Is Worth Building (Solo + AI)
This is worth building only as a tiny validation product, not as a fully formed startup bet. A solo developer can ship it fast with AI coding tools because it is one narrow flow, a small notification system, and a simple local scorecard; if users still do not return for 10-14 nights, the builder gets a clear no instead of wasting months polishing a structurally weak idea.
## Solo MVP Scope
- What's in v1: one platform only; onboarding with bedtime window and notification permission; nightly unresolved closeout reminder; one-tap bedtime check-in with `Lenses out`, `Still in`, and `Skip tonight`; repeated reminders after `Still in`; local 14-night scorecard; simple history of cleared/missed nights; basic settings for reminder timing and pause.
- What's out of v1: NFC under any name; wear-time timers; morning start logs; missed-log reconstruction; weekly summary; replacement tracking; biweekly support; extended-wear support; medical scoring; analytics dashboards; streak gamification beyond the 14-night run; widgets as a required feature; Apple Health; social/community features; payments; cross-platform sync.
- Estimated build time with AI coding tools: 8-12 working days total, including 1 day for notification and state-flow copy/content prep, 4-5 days for UI build and local state logic, 2-3 days for notification/integration setup and permission handling, 1 day for score/history data modeling, and 1-2 days for QA on reminder timing, edge cases, and onboarding drop-off.
## Open Questions
- UNVERIFIED: Is a dedicated closeout app meaningfully better than a default reminders app for this segment, or does this collapse into a nicer wrapper around the same behavior?
- Should v1 launch as iOS-only or Android-only based on where notification behavior is easier to ship cleanly for a solo builder?
- Does the 14-night challenge framing feel motivating enough to drive week-2 return, or does it read like lightweight habit gamification?
- Should `Skip tonight` exist in v1 for honesty and cleaner data, or does it weaken the closeout habit by giving users an easy escape hatch?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Welcome / 14-Night Challenge Intro — explains the narrow promise; key elements: headline about not sleeping in lenses, short explanation of the 14-night challenge, who it is for, primary CTA.
- Screen 2: Onboarding / Bedtime Setup — captures the only setup needed; key elements: bedtime window picker, notification permission prompt, short note that the app tracks nightly closeout only.
- Screen 3: Home / Tonight Status — main daily screen; key elements: current challenge day, tonight status (`pending`, `cleared`, `missed`, `skipped`), countdown to bedtime, primary button to open closeout, compact 14-night score row.
- Screen 4: Bedtime Closeout Modal / Screen — action screen opened from reminder; key elements: prompt asking whether lenses are out, three actions (`Lenses out`, `Still in`, `Skip tonight`), short explanation of what happens next.
- Screen 5: Reminder Escalation / Still In State — shown after `Still in`; key elements: next reminder time, `Clear now` action, `Snooze` confirmation, visible unresolved state.
- Screen 6: Challenge History / Scorecard — shows progress over the 14-night run; key elements: nightly calendar/list with cleared, missed, or skipped status, current completion rate, restart challenge action after day 14.
- Screen 7: Settings — basic controls only; key elements: bedtime window, reminder interval, pause challenge, reset data, help/trust copy.
Key interactions:
- User moves from Welcome / 14-Night Challenge Intro to Onboarding / Bedtime Setup, then lands on Home / Tonight Status.
- At bedtime, a notification opens Bedtime Closeout Modal / Screen, where the user clears the night, snoozes through `Still in`, or skips.
- Choosing `Still in` moves the user into Reminder Escalation / Still In State and schedules another reminder until the night is cleared or missed.
- Home / Tonight Status links to Challenge History / Scorecard so the user can see whether the 14-night run is holding together.
- Settings is reachable from Home / Tonight Status for bedtime changes, reminder interval changes, and data reset.
## Version History
- v1: Original idea
- v2.1: Critique - still too close to a reminder app, weak week-2 retention, fragile logging, and too much scope pretending to be value
- v3: Improved - reduced to a 14-night bedtime closeout challenge with no wear-time math and a clear validation goal
