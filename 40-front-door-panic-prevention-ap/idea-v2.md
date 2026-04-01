# Idea #40: Front-door panic prevention app (Improved)

## Version: v2
**Date:** 2026-04-01 16:14
**Status:** improved

## Original Idea
Front-door panic prevention app. You stick cheap NFC tags on your wallet, keys, badge, meds, or laptop charger, and the app arms a leave-home check when your phone disconnects from home WiFi during chosen hours. If an essential item has not been tapped that morning, it throws a loud lock-screen alert and keeps nagging until you confirm it or deliberately skip it. Different packs cover work, gym, travel, or school, so it becomes Alarmy for forgetting your essentials.

## What Changed and Why

- **Cut NFC tags and WiFi-disconnect detection entirely.** The critique called both UNVERIFIED and fragile — ambient detection via WiFi disconnect is unreliable (mailbox trips, router blips, weak signal), and NFC tapping adds friction exactly when users are most rushed. The improved idea removes all sensor magic. The trigger is now a scheduled local notification at the user's departure time — no background permissions, no hardware required, no false positives. Reliable on day one.

- **Replaced loud nagging with intentional pre-departure ritual.** The critique identified that repeated aggressive alerts are only lovable when accuracy is near-perfect, which it never is. V2 flips the model: instead of the app screaming at you when it thinks you're leaving, you open a one-tap checklist the moment you get a soft nudge 10 minutes before your normal departure time. Think pilot pre-flight checklist, not car alarm. The user opts in; the app does not ambush them.

- **Solved retention with a streak mechanic.** The critique flagged "why come back after day 1?" as unresolved. V2 adds a daily streak counter: "12 days, zero forgotten items." It is not about the app being clever — it is about the ritual feeling satisfying and quantified. Users who build a 2-week streak will protect it, exactly the same psychology that makes Duolingo sticky. The value is the habit record, not just the checklist.

## Improved Description

**CheckOut** is a pre-departure ritual app for people with consistent daily routines who periodically forget something important. You set a departure time once. Every day at that time, a soft notification fires: "Leaving in 10 minutes — run your checklist." You open it, tap through 5–7 items (keys, wallet, badge, charger, meds), skip anything not needed today, and tap "I'm out." Done in 15 seconds.

There are no NFC tags, no ambient sensors, no WiFi monitoring, no background tricks. It is a scheduled local notification plus a fast tap-through checklist — the simplest possible implementation of "did you check your bag before leaving?"

The hook is the streak. After a week of clean departures, users see "7 days, zero forgotten items." After two weeks, they are protecting that number. The app earns daily opens not because it is smart but because the ritual becomes part of leaving the house.

The starter pack (keys, wallet, phone, badge, charger, meds) works out of the box with zero setup. Users can rename items, add or remove them, and create a second pack for gym or travel days. Departure times are configurable per day of week (weekday 8:15am, weekend 10:00am). One-tap "skip today" per item prevents false-positive frustration.

MVP is a single-platform mobile app (iOS or Android) with local notifications, no backend, no account required.

## Why This Is Worth Building (Solo + AI)

A solo dev can ship this in under two weeks because the entire technical stack is local notifications + a checklist UI + simple state persistence — no server, no auth, no real-time sync, no background services, no NFC permissions. AI coding tools can generate 80% of the React Native scaffold, notification scheduling, and streak logic from a prompt. The problem is real, the solution is simple, and the moat is habit formation — which compounds over time as users build streaks they do not want to break.

## Solo MVP Scope

- **What's in v1:**
  - Single departure time per day (configurable by day of week)
  - Pre-built starter pack: keys, wallet, phone, badge, charger, meds
  - Tap-through checklist UI with per-item skip
  - "I'm out" confirmation ending the flow
  - Streak counter (consecutive days with a completed check-out)
  - Pack editor: rename, add, remove items (one pack only)
  - Local push notification at departure time (no server required)
  - History screen: last 30 days, skipped items, completion rate
  - Onboarding: set departure time, pick starter pack items, done

- **What's out of v1:**
  - NFC scanning (explicitly cut by critique — adds friction, hardware dependency, edge cases)
  - WiFi disconnect / geofence detection (explicitly cut — unreliable, platform-restricted)
  - Background ambient monitoring (explicitly cut — false positives kill trust)
  - Loud lock-screen escalation / nagging (explicitly cut — annoying without near-perfect accuracy)
  - Multiple named packs at launch (one customizable pack only; simplifies onboarding)
  - Household / family sync (network effects dependency, out of scope)
  - Cloud backup / account (adds backend complexity, not needed for v1 validation)
  - Second departure (gym, errand) — configurable only as a separate scheduled notification, not a smart re-arm

- **Estimated build time with AI coding tools:** 10–12 days
  - Days 1–2: React Native scaffold, navigation, local storage
  - Days 3–4: Checklist screen with tap-through and skip logic
  - Days 5–6: Local notification scheduling (expo-notifications or react-native-push-notification)
  - Days 7–8: Streak logic, history screen, stats
  - Days 9–10: Onboarding flow, pack editor
  - Days 11–12: Polish, edge cases (first launch, skip-all, late departure), TestFlight or Play Store submission

## Key Assumptions Check

1. **Users will open the notification and run the checklist daily** — UNVERIFIED. This is the core adoption risk. The app only works if the notification habit forms in the first week. Fallback: make the checklist completable in under 15 seconds, add a visible streak on the notification itself ("Day 4 — tap to continue your streak"), and offer a 5-minute snooze without penalty.

2. **Scheduled local push notifications fire reliably on iOS and Android without special entitlements** — VERIFIED. Standard local notifications via expo-notifications work on both platforms without background modes or special permissions. No server required. This is the correct technical primitive.

3. **A streak mechanic drives meaningful retention** — UNVERIFIED. Streaks work in habit apps (Duolingo, Headspace) but those apps have daily content. CheckOut is the same checklist every day. Fallback: if streak alone is insufficient, add a "forgotten item log" where users can record what they actually forgot — this creates personal data that makes the app feel more useful over time and harder to delete.

## Open Questions

- Is iOS or Android a better initial platform for this? Android has fewer notification restrictions for local alerts; iOS has a larger audience willing to pay. Pick one and validate before going cross-platform.
- Does the 15-second checklist feel fast enough that users do not resent the notification? Needs user testing with 5–10 people in week 1.
- What is the right default departure time? 8:15am covers most commuters but might be wrong for shift workers or parents doing school drop-off.
- Should the streak reset if you open the app and tap "skip everything today" (day off), or should that count as a valid check-out? This affects how forgiving the retention mechanic feels.
- Is there a monetization path? Possible: one-time paid unlock for multiple packs, multiple departure times per day, or export/stats. Free tier (one pack, one time) is enough for validation.

## Design Handoff

- **Screen 1: Onboarding — Set Departure Time**
  Purpose: First-launch setup, step 1. Shows a large time picker. Subtext: "We'll remind you before you leave." Single CTA: "Next." No account creation, no email.

- **Screen 2: Onboarding — Customize Your Pack**
  Purpose: First-launch setup, step 2. Shows the default 6-item starter pack (keys, wallet, phone, badge, charger, meds) as toggles. User can deselect items they do not carry. CTA: "I'm ready." Takes under 30 seconds.

- **Screen 3: Home / Dashboard**
  Purpose: Main screen between departures. Shows: today's departure time, streak count prominently ("Day 7"), completion status for today ("Ready" / "Not checked yet"), and a manual "Start Check-Out" button for early departure. Secondary: link to Pack Editor and History.

- **Screen 4: Pre-Departure Checklist**
  Purpose: Core interaction. Opens from notification or manual trigger. Shows each item as a large tap target. Tap = checked (green checkmark). Long-press or swipe = "skip today" (grey, no penalty). All items checked → "I'm Out" CTA appears. Fast, single-hand operable.

- **Screen 5: Confirmation / Streak Celebration**
  Purpose: Post-completion feedback. Shows "You're good to go" with streak milestone. If it's a streak milestone (7, 14, 30 days), shows a subtle animation. Auto-dismisses after 3 seconds or user taps to dismiss.

- **Screen 6: Pack Editor**
  Purpose: Customize items in the single pack. List of current items with rename (inline edit) and delete. "Add item" at the bottom with a text field. Reorder via drag. No pack naming needed in v1.

- **Screen 7: History**
  Purpose: Shows last 30 days as a calendar grid (green = completed, grey = skipped/missed). Tap a day to see which items were skipped. Shows completion rate % for the month. Streak displayed at top.

- **Screen 8: Settings**
  Purpose: Change departure times per day of week. Toggle notification on/off. Reset streak (with confirmation). About / feedback link.

**Key interactions:**
- Notification fires 10 min before departure time → user taps → opens directly to Screen 4 (checklist), bypassing home screen
- User checks all items → Screen 5 (confirmation) → auto-returns to Screen 3 (home) after 3 seconds
- User taps "skip today" on an item → item goes grey, does not block completion
- User taps "Start Check-Out" on Screen 3 → opens Screen 4 immediately (for early or manual departures)
- Streak increments only when Screen 5 is reached (at least one item checked, not all skipped)

## Version History
- v1: Original idea — NFC tags + WiFi disconnect detection + loud lock-screen nagging
- v1.1: Critique — core loop self-defeating, ambient detection UNVERIFIED, NFC fragile, false alarms kill trust, retention unsolved
- v2: Improved — cut NFC and WiFi detection entirely; pivot to scheduled local notification + intentional tap-through checklist; added streak mechanic for retention; no backend, no sensors, shippable in 10–12 days
