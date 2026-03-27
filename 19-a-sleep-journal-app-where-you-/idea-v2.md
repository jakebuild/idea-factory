# Idea #19: Sleep Journal App (Improved)

## Version: v2
**Date:** 2026-03-27 17:16
**Status:** improved

## Original Idea
A sleep journal app where you log bedtime, wake time, and one word for how you feel — it shows weekly sleep patterns and streaks, no wearable needed

## What Changed and Why
- **Added "why" tagging to replace the one-word gimmick with actual signal** — The critique nailed it: "groggy" every day is useless data. One optional reason tag (alcohol, stress, travel, late screen, caffeine, exercise) transforms the app from a diary into a pattern detector. Now the app can tell you something you didn't already know: "4 of your 5 worst sleep nights followed a late-screen tag." That's the "whoa" moment that keeps users past week 2.
- **Replaced streaks with a weekly consistency score** — Miss one day and a streak dies, users churn. A "5/7 this week" score forgives real life while still rewarding consistency. No more abandonment from a single missed Sunday. The streak mechanic stays as a secondary stat (longest streak) but it's not the primary hook.
- **Locked in a specific niche: shift workers and people with irregular schedules** — "No wearable needed" is a nothing burger for 9-to-5 sleepers who already know their patterns. But shift workers, new parents, and people with variable schedules have genuinely chaotic sleep they can't track passively — they go to bed at 2pm one day, 11pm the next. This audience has real pain and zero good solutions. The app's manual logging is a feature, not a consolation prize, for this group. Every design decision — flexible time entry, no fixed "last night" assumption, sleep debt tally — serves them specifically.
- **Added sleep debt tally as the killer insight** — Simple math: your target hours minus actual hours, running total for the week. This is immediately actionable. "You're 4.5 hours in debt this week" hits differently than a bar chart. It answers the "so what" question every other sleep app ignores.
- **Cut backfill ambiguity with an explicit decision** — Allow backfill up to 7 days, but backfilled entries are visually marked and excluded from the weekly consistency score. Integrity maintained, real life accommodated.

## Improved Description
A manual sleep journal built for people with irregular schedules — shift workers, new parents, remote workers who blur work/sleep time. Log bedtime, wake time, and one optional "why it was rough" tag (stress, alcohol, late screen, caffeine, travel, exercise, kid). The app surfaces two things most sleep apps can't: a running sleep debt tally for the week, and pattern connections between your tags and your worst nights.

No wearable. No subscription. No passive tracking. You log it, the app finds the pattern.

Core loop: open app in the morning → tap wake time → tap bed time (defaults to last entry's time) → optionally tap one reason tag → see updated sleep debt and weekly score. Thirty seconds. Done.

The weekly view shows a bar chart of sleep duration with color-coded debt (green = surplus, red = deficit), the week's tags as chips, and one auto-generated insight: "Your worst 2 nights this week both had a stress tag." That's the retention hook — not streaks, not gamification, but genuine self-knowledge.

Target user: shift worker, new parent, or anyone whose sleep schedule doesn't fit the "went to bed at 11pm, woke at 7am" mold. They have the most to gain from visibility, and the least served by wearable-first apps.

## Why This Is Worth Building (Solo + AI)

The core is still just a form + local storage + a chart library — nothing technically hard. But now it has a specific audience with real pain (shift workers, new parents), a differentiated insight (sleep debt + tag correlations), and a "whoa" moment that earns word-of-mouth. A solo dev can ship the logging + debt tally + weekly insight engine in 2 weeks with AI coding tools; the tag correlation logic is simple frequency math, not ML.

## Solo MVP Scope
- **What's in v1:** Log entry (bedtime, wake time, one reason tag optional), sleep debt tally (week + running), weekly bar chart, one auto-insight per week ("your most common tag on bad nights"), weekly consistency score (X/7), flexible time entry (not locked to "last night"), backfill up to 7 days marked as backfill
- **What's out of v1:** Push notifications (timezone/permission complexity — add in v1.1), onboarding flow (start with settings screen instead), social/sharing features, export, streak gamification, multiple sleep sessions per day (naps — v2 problem), mood vocabulary builder, cloud sync (local storage first)
- **Estimated build time with AI coding tools:** 2 weeks. Week 1: data model + log form + local storage + debt math. Week 2: weekly chart + insight engine + polish. Ship as PWA first to skip App Store review.

## Open Questions
- What's the monetization path? One-time paid app ($2.99) is the cleanest fit for this audience — no subscription fatigue, low barrier. Validate with a waitlist before building.
- Does the tag correlation insight require a minimum data threshold? Need to decide: show insight after 7 entries, or 14? Too early = false signal, too late = user has already churned.
- For shift workers logging a sleep session that spans two calendar dates (e.g., slept 8am–4pm), how does the date label work? Decision needed before building the data model.
- Is PWA sufficient for the target audience, or do shift workers skew toward native apps? Influences the build choice (Expo vs. Next.js PWA).

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Log Entry** — Primary action screen. Bedtime time picker + wake time time picker (scroll-wheel, defaults to previous entry's times). Optional reason tag row: chips for Stress / Alcohol / Late screen / Caffeine / Travel / Exercise / Kid / Other. Shows calculated sleep duration live as times are adjusted. Save button. "Log for a different day" link for backfill. This is the home screen — no separate home/log split.
- **Screen 2: Weekly Dashboard** — Bar chart (7 days, current week). Each bar colored by debt status (green = at/above goal, yellow = 30min under, red = 1hr+ under). Below chart: weekly consistency score (e.g. "5/7 nights logged"), total sleep debt for the week (e.g. "–3.5 hrs this week"), one auto-insight card ("Your 2 worst nights both had a Late screen tag"). Tap any bar to open Entry Detail. Week navigation arrows (prev/next week).
- **Screen 3: Entry Detail** — Single night view. Date, bedtime, wake time, total hours, delta vs. goal (e.g. "+45 min" or "–1.2 hrs"), reason tag (if set), backfill badge (if applicable). Edit button to modify entry. Delete option.
- **Screen 4: History / Calendar** — Monthly calendar grid. Each logged day has a color dot (green/yellow/red based on debt). Days with tags show a small indicator. Tap day → Entry Detail. Unlogged days are empty. Swipe or arrow to navigate months.
- **Screen 5: Stats** — All-time numbers: total nights logged, average sleep duration, sleep goal hit rate (%), most common reason tag, best week (highest consistency), worst week (most debt). Visual: small sparkline of average sleep per week over last 8 weeks.
- **Screen 6: Settings** — Sleep goal (hours, default 8). Notification toggle + time picker (off by default). Data export (CSV). Clear all data (destructive, confirm dialog). About/version.

Key interactions:
- **Main flow:** Open app → Screen 1 (Log Entry) is the default view → adjust times → optionally tap a reason tag → tap Save → brief success animation showing updated debt → auto-navigate to Weekly Dashboard
- **Backfill flow:** From Log Entry, tap "Log for a different day" → date picker → same form, entry saved with backfill marker, excluded from consistency score
- **Insight drill-down:** Tap the insight card on Weekly Dashboard → simple list view of all entries that match the pattern (e.g., all entries with Late screen tag, sorted by sleep duration)
- **Time picker UX:** Scroll-wheel style pickers. Bedtime defaults to last logged bedtime. Wake time defaults to last logged wake time. Duration updates live as user scrolls.
- **Empty state:** First open shows Log Entry with a minimal explainer ("Log your sleep. See what hurts it.") — no onboarding carousel, no sign-up wall.

## Version History
- v1: Original idea
- v1.1: Critique — too generic, one-word gimmick not sticky, no insight, streak mechanic drives churn
- v2: Improved — niche locked to shift workers/irregular sleepers, tag-based insight replaces word gimmick, sleep debt tally added, streaks replaced with weekly consistency score
