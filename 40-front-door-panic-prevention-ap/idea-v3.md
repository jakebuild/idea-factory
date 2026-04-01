# Idea #40: Front-door panic prevention app (Improved)

## Version: v3
**Date:** 2026-04-01 16:18
**Status:** improved

## Original Idea
Front-door panic prevention app. You stick cheap NFC tags on your wallet, keys, badge, meds, or laptop charger, and the app arms a leave-home check when your phone disconnects from home WiFi during chosen hours. If an essential item has not been tapped that morning, it throws a loud lock-screen alert and keeps nagging until you confirm it or deliberately skip it. Different packs cover work, gym, travel, or school, so it becomes Alarmy for forgetting your essentials.

## What Changed and Why

- **Replaced the generic pre-departure checklist with a miss-based personal watch list.** The critique landed the killing blow: "the same six taps forever" is compliance theater, not a product. It is indistinguishable from a sticky note. V3 inverts the model — the watch list is built from items you have ACTUALLY forgotten. You do not set it up; it grows from your real failures. Pre-departure reminders surface only your known weak spots (1–3 items), not a generic 6-item generic inventory. The list evolves as items graduate to "mastered." This makes the product feel personalized and alive instead of static.

- **Made the miss logger the primary entry point, not the checklist.** The critique identified the circular dependency exactly: "asks users to remember to open an app so they can remember not to forget things." V3 fixes this by meeting users at the emotional moment of failure — after they have already left and realized they forgot something. The core action is "I forgot X" — a one-tap log from the lock screen or home screen widget when the frustration is real. This organic, zero-friction log populates the watch list without any manual setup and creates actual personal data. "Days since last miss" replaces the streak because it measures real-world success, not ritual compliance.

- **Cut the history screen entirely and demoted the streak mechanic.** The critique was explicit: the 30-day calendar heatmap is "builder self-indulgence" and the streak is "flimsy without content depth." Both are removed from MVP. Accumulated value comes from the watch list itself — seeing items move from "active miss" to "mastered" over weeks is forward progression that earns meaning. No dashboard garnish.

## Improved Description

**GotIt** is a personal forgetting tracker for office commuters who consistently miss the same 1–3 items when leaving the house. Instead of a generic pre-departure checklist, it learns what you specifically forget and only reminds you about those things.

The primary action is logging a miss. When you arrive at work and realize you left your badge at home, you open the app (or tap a widget) and log "forgot badge." After two misses on the same item, it joins your active watch list. The next morning, your departure notification says: "Watch out — you've forgotten your badge twice. Got it today?" That is a targeted nudge about a real pattern, not a generic "did you grab everything?"

As you go 14 days without forgetting a watched item, it graduates to "mastered" and drops off the active list. The watch list shrinks over time as you fix your weak spots. The success state is an empty list — the app has done its job and goes quiet.

There are no NFC tags, no WiFi detection, no background monitoring, no 6-item compliance checklist, no history heatmap. Just: log your misses → build your personal weak-spot list → get targeted departure nudges for YOUR specific patterns.

Target user: office commuters with consistent weekday routines who forget the same item 2–3 times per month. Not shift workers, not parents with chaotic schedules, not general forgetfulness.

MVP is a single-platform mobile app (iOS first) with local notifications, a home screen widget for quick miss logging, and no backend.

## Why This Is Worth Building (Solo + AI)

A solo dev can ship this in under two weeks because the stack is: local persistence, one scheduled notification per day, a list UI with state transitions, and a home screen widget. No server, no auth, no real-time sync. The personalization comes from the user's own logged data — zero content prep needed. The unfair advantage over Apple Reminders and alarm labels is that those tools require you to anticipate the problem; this app captures the failure after it happens and converts it into a forward-looking nudge. That inversion is what makes it feel smart rather than manual.

## Solo MVP Scope

- **What's in v1:**
  - Miss logger: one-tap log screen with item name (free text or quick-pick from watch list)
  - iOS home screen widget: single button "I forgot…" → opens miss logger instantly
  - Watch list: items auto-added after 2+ misses; shows each item, miss count, days since last miss
  - Item mastery: item graduates to "mastered" after 14 clean days; removed from active reminders
  - Pre-departure micro-check: single scheduled notification showing only active watch-list items (not a generic checklist)
  - "Days since last miss" counter on home screen (across all items combined)
  - Onboarding: set departure time, one screen, done — no item setup required
  - Settings: departure time, notification on/off, reset an item's mastery progress

- **What's out of v1:**
  - History screen / 30-day calendar heatmap (explicitly cut by critique — builder self-indulgence)
  - Generic 6-item starter pack checklist as core loop (replaced by miss-based watch list)
  - Streak mechanic as primary retention driver (replaced by mastery graduation and days-since-last-miss)
  - NFC scanning (cut in v1 critique — hardware dependency)
  - WiFi disconnect / geofence detection (cut in v1 critique — unreliable)
  - Multiple named packs (not needed; watch list IS the pack)
  - Gamification badges, charts, or social features (critique explicitly warned against decorating a shallow loop)
  - Cloud backup / account (adds backend complexity, not needed for v1 validation)
  - Android (start iOS-only; WidgetKit makes the miss-logger widget straightforward with AI tools)

- **Estimated build time with AI coding tools:** 12–14 days (honest, accounting for notification edge cases)
  - Days 1–2: SwiftUI scaffold, local storage (SwiftData or UserDefaults), navigation
  - Days 3–4: Miss logger screen + watch list data model (miss count, last miss date, mastery state)
  - Days 5–6: Home screen widget (WidgetKit) for one-tap miss logging
  - Days 7–8: Local notification scheduling, departure time config, notification payload showing watch-list items
  - Days 9–10: Watch list screen (item cards, mastery progress, graduation logic)
  - Days 11–12: Onboarding (1 screen), settings, edge cases (first launch, empty state, all mastered state)
  - Days 13–14: Polish, TestFlight submission, notification permission timing quirks

## Key Assumptions Check

1. **Users will log misses organically when the pain is fresh** — UNVERIFIED. This is the biggest retention risk. If logging a miss feels like extra work, users stop and the watch list never builds. Fallback: the home screen widget reduces this to a single tap from the lock screen. The miss logger must open in under 2 seconds with no auth gate. Test with 5 users in week 1 — do they actually log misses, or do they forget to log forgetting?

2. **A targeted nudge for a known weak spot is more compelling than ignoring a generic notification** — UNVERIFIED. The hypothesis is that "you forgot your badge twice — got it?" lands differently than "reminder: departure checklist." This is the core differentiator and cannot be verified before building. Fallback: if users still dismiss the nudge, the watch list's "days since last miss" counter still provides personal accountability even without notification compliance.

3. **Local notifications + WidgetKit can support this without a backend** — VERIFIED. Standard iOS capabilities with no special entitlements. SwiftData handles local persistence. WidgetKit supports intent-based actions for the miss-logger widget. This is the correct technical primitive.

## Open Questions

- What is the right mastery threshold — 14 days clean, or N consecutive clean departures? Days is simpler but breaks for weekend items; departures is more accurate but harder to count. Decide before building the mastery logic.
- Does the miss logger need free-text or just a quick-pick from watch-list items? Free text is flexible but harder to de-duplicate. Start with free text and normalize on display.
- What happens in the success state — when all items are mastered and the watch list is empty? This needs a graceful "you're done, the app is quiet now" state that doesn't feel like abandonment. Could be an achievement screen or just a clean empty-state home screen.
- Is there a monetization path? Possibly: one-time paid unlock for multiple departure times (weekday/weekend), or a share-your-watch-list feature for households. Neither is needed for v1 validation.
- Should missed departures (user never opened the notification) count as a miss for the mastery timer? Probably not — no signal either way. Only explicit miss logs should count.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding — Set Departure Time**
  Purpose: First-launch setup, single screen. Large time picker, headline "When do you usually leave?", subtext "We'll check in before you go." Single CTA: "Done." No item setup, no account creation. User is in the app in under 20 seconds.

- **Screen 2: Home Screen**
  Purpose: Daily anchor. Shows: "Days since last miss" counter prominently at top (e.g., "4 days clean"). Below: watch list cards — each active item shows item name + miss count + days since last miss. Tertiary: "mastered" section showing graduated items in muted style. Bottom: large "I forgot something" button. If watch list is empty, shows "Nothing to watch — log a miss when it happens."

- **Screen 3: Miss Logger**
  Purpose: Core entry point triggered from widget, "I forgot something" button, or home screen. Shows a large text field "What did you forget?" with quick-pick chips for existing watch list items (badge, charger, etc.). Single CTA: "Log it." Completes in under 5 seconds. No back-filling required — just tap and log.

- **Screen 4: Pre-Departure Micro-Check (Notification Landing)**
  Purpose: Opened by tapping the morning departure notification. Shows ONLY the active watch-list items as large tap targets (not a full generic checklist). Each item: "Got your [badge]?" with Yes / Skip buttons. "Yes" marks it clean for today. Completing all items → brief confirmation ("Good to go") and auto-dismiss. If watch list is empty, notification does not fire.

- **Screen 5: Item Detail / Mastery Progress**
  Purpose: Tapping any watch list item on the home screen. Shows: item name, full miss history (list of dates, not a heatmap), days since last miss, mastery progress bar (e.g., "9 of 14 clean days"). CTA: "Edit name" or "Remove from watch list." This is the only place historical dates appear — no calendar heatmap, no completion rate chart.

- **Screen 6: Settings**
  Purpose: Departure time config (per day of week, e.g., weekday vs. weekend), notification toggle, reset mastery for an item. About / feedback link.

- **Widget (Home Screen / Lock Screen):**
  Purpose: One-tap miss logging without opening the app. Shows current "days clean" count. Single button: "I forgot…" → deep-links directly to Screen 3 (Miss Logger).

**Key interactions:**
- User forgets badge at work → taps widget → miss logger opens → types "badge" or taps quick-pick chip → "Log it" → returns to home screen; badge appears in watch list
- Next morning: departure notification fires → shows "Watch out — badge (2 misses). Got it?" → user taps Yes → confirmation → auto-dismiss
- After 14 clean days for badge → badge card on home screen shows "Mastered" → drops off departure notification
- User taps a watch list item → opens item detail → sees mastery progress bar and miss history dates
- All items mastered → home screen shows empty state: "Nothing to watch — you're done" → departure notification goes quiet

## Version History
- v1: Original idea — NFC tags + WiFi disconnect detection + loud lock-screen nagging
- v2.1: Critique — checklist + streak is not a moat; circular dependency; history screen is builder self-indulgence; retention unsolved; audience too broad
- v3: Improved — pivoted to miss-based personal watch list; miss logger as primary entry point; targeted departure nudge for known weak spots only; cut history heatmap and streak; mastery graduation as the forward-progress mechanic
