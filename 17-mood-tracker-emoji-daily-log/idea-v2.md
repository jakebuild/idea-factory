# Idea #17: MoodDot — Daily Mood + Context Tracker (Improved)

## Version: v2
**Date:** 2026-03-27
**Status:** improved

## Original Idea
One-tap emoji mood logging with a daily calendar view.

## What Changed and Why

- **Picked a specific user job: correlate mood with daily habits.** Generic "how do you feel" logging dies in 3 days. The new angle is "understand what actually affects your mood" — you log mood + 1–3 context tags (slept well, skipped gym, stressful meeting). This gives the pattern view real data to work with.

- **Made the widget the primary entry point.** The critique is right — opening an app daily is friction. A home screen widget with 5 emoji lets you log in one tap without launching anything. The app itself becomes the analysis layer, not the logging layer.

- **Pattern view is now the hero screen.** Instead of a generic calendar heatmap, the app shows correlations: "On days you tag 'good sleep' your mood averages 4.1/5. On days you skip exercise it drops to 2.8/5." This is the insight users actually want and it's genuinely hard to get from Daylio.

- **Dropped cloud sync for v1.** Local-only removes auth, backend costs, and privacy concerns in one cut. iCloud backup is enough for most users.

## Improved Description

MoodDot is a widget-first mood tracker that helps you understand what actually affects how you feel.

**Daily logging (10 seconds):** Tap a mood emoji on the home screen widget — no app required. Optionally add 1–3 context tags from a preset list (slept well, exercised, work stress, social time, alcohol, etc.). That's it.

**Weekly insight:** Every Sunday, the app sends a notification: "Your mood this week averaged 3.2/5. Your best days correlated with exercise. Your worst days followed poor sleep." One tap opens the full breakdown.

**Pattern screen:** A scrollable view showing your top positive and negative correlations over the last 30/90 days. Tap any correlation to see the raw days behind it.

## Why This Is Worth Building (Solo + AI)

The widget + correlation angle is meaningfully different from Daylio without being complex to build. Core features (widget, local storage, tag system, basic correlation math) are all achievable with React Native + AI coding tools in 2 weeks. The weekly insight notification is a simple scheduled local notification — no backend required.

## Solo MVP Scope

- **In v1:** Home screen widget (5 emoji), context tags (preset list of 12), local storage, weekly correlation summary, pattern screen (30-day view)
- **Out of v1:** Cloud sync, custom tags, social sharing, AI-generated insights, streak tracking, export
- **Estimated build time:** 2–3 weeks with AI coding tools

## Design Handoff

- **Screen 1: Widget** — 5 emoji in a row on the home screen. Tap logs immediately. No app launch.
- **Screen 2: Home / Today** — Shows today's log (or prompt if not logged). Last 7 days as a mini strip. CTA to view patterns.
- **Screen 3: Log Detail** — After tapping widget, optionally add context tags. Confirm button. Skip option.
- **Screen 4: Pattern View** — Top 3 positive correlations, top 3 negative. Each as a simple bar comparison ("mood with exercise: 4.1 vs without: 2.8"). Tap to drill down.
- **Screen 5: Calendar** — Monthly heatmap. Tap any day to see mood + tags logged.
- **Screen 6: Settings** — Notification time, tag list management, data export.
