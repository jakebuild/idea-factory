# Idea #52: Snore Receipt (Improved)

## Version: v2
**Date:** 2026-04-02 20:05
**Status:** improved

## Original Idea
Snore Receipt is a health app that copies the SnoreLab model but sharpens the reveal and the action. Leave your phone on the bed or nightstand, and by morning the app shows your total snore minutes, your worst 10-second clip, and exactly how much of the snoring happened on your back versus your side. Then it gives you one simple 3-night experiment like side-sleeping or pillow elevation so the loop becomes sleep, get the receipt, test one change, and compare tomorrow.

## What Changed and Why

- **Posture detection is cut entirely.** The critique correctly called it UNVERIFIED and a trust-killer. Accelerometer-based position inference from a bedside phone is unreliable, and wrong health-adjacent data destroys user trust immediately. Removed from all framing — not renamed, not softened, gone.

- **Replaced sensor-based insight with manual context logging.** Instead of pretending the phone knows what you did, the app asks you to log 3–5 pre-sleep factors before bed (alcohol, congestion, sleep time, self-reported position, late meal). This is cheap to build, honest about its mechanism, and produces the actual differentiator: after 7+ nights, the app shows real correlations between your inputs and your snore score. "Your 3 worst nights all had alcohol" is something SnoreLab doesn't surface without manual spreadsheet work. That's a real insight, not a generic blog tip.

- **Repositioned from "health app" to "habit-tracking tool for people whose partners say they snore."** This cuts the accuracy burden dramatically. No medical claims, no precision framing — just a consistent score, a highlight clip, and pattern discovery over time. The specific trigger use case is: someone was told they snore and wants evidence + a way to see if changes help. Build only for that.

## Improved Description

Snore Receipt is a habit-tracking app for people who've been told they snore. Before bed, log a few context factors — had a drink, congested, went to bed late, ate late. Sleep with your phone nearby. In the morning, open your receipt: a snore score for the night, your loudest 10-second clip, and how last night compares to recent nights.

After a week, Snore Receipt shows you patterns the eye can't catch — your worst nights correlate with your flagged factors. That's the actual product: not a detection engine, not a sleep coach, just honest feedback tied to things you control. The experiment loop becomes: pick one factor, test it for three nights, see if the score moves.

The app never claims to be medical, never guesses your position, and never pretends it knows the cause of your snoring. It just shows you the receipts.

## Why This Is Worth Building (Solo + AI)

The core is a recording app + a log form + a charting layer — three components a solo dev can ship with AI coding tools in 2–3 weeks. The context-correlation reveal is the differentiator and it requires no fancy sensors, no ML pipeline, just storing inputs and displaying them alongside scores over time. The trust burden is low because the app is honest about what it is: a habit logger with audio evidence, not a diagnostic tool.

## Solo MVP Scope

- **What's in v1:**
  - Overnight audio recording with background mode (phone on bedside)
  - Snore detection using audio amplitude + basic frequency analysis to generate a nightly score
  - Loudest 10-second clip extracted and playable in-app
  - Pre-sleep context log: 5 yes/no toggles (alcohol, congested, late meal, late bedtime, sleeping alone)
  - Morning receipt screen: score, clip, comparison bar vs. last 7 nights
  - Pattern view after 7 nights: which context factors correlate with higher scores
  - 3-night experiment mode: mark one factor as "testing this" and see score change over the window

- **What's out of v1 (explicitly cut per critique):**
  - Sleep position detection / posture attribution — cut, do not revisit
  - Generic AI sleep coaching or automated advice suggestions
  - Partner noise filtering (acknowledge limitation in UI, do not attempt to solve)
  - Social sharing or partner sync
  - Payments, subscriptions, or any monetization layer
  - Any medical framing or accuracy guarantees

- **Estimated build time with AI coding tools:** 2.5–3 weeks
  - Week 1: Audio recording pipeline, background mode, snore scoring, clip extraction (~8 days including testing on real devices)
  - Week 2: Context log UI, morning receipt screen, 7-night history + pattern view
  - Week 3: Experiment mode, polish, edge-case handling (battery death, OS suspension, no snoring detected nights)

## Open Questions

- Background audio recording on iOS is notoriously restricted — does the app need to keep the screen on, use a specific background mode entitlement, or rely on a workaround? This must be tested on a real device before committing to iOS as primary platform.
- How does snore scoring behave when a partner, pet, or white noise machine is in the room? The app should surface this as a known limitation and ask users to self-report "sleeping alone" in the context log rather than attempting to solve it technically.
- Will users remember to log context before bed consistently? If not, pattern discovery never fires and the core differentiator is inert. Consider a bedtime reminder as a default-on setting.
- Is there a lightweight, royalty-free snore detection model or audio fingerprinting library that can run on-device and reduce false positives from ambient noise? Or does scoring need to be manual-threshold-based?

## Assumptions Check

1. **Overnight audio recording works reliably on mobile in background mode** — UNVERIFIED. iOS background audio has known restrictions. Must prototype and test on a real device in week 1 before committing the rest of the build. Fallback: prompt users to keep screen on in airplane mode, or build Android-first where background audio is less restricted.

2. **Users will log pre-sleep context most nights** — UNVERIFIED. This is the engine that produces insight. If compliance is low, the pattern view never generates a meaningful correlation. Fallback: make the log a 5-second swipe on the receipt screen, not a separate step; add bedtime push reminder on by default.

3. **A simple audio score is honest enough that users trust it even knowing it's approximate** — UNVERIFIED but LOW RISK. The app is explicitly positioned as a habit tracker, not a medical tool. Users who self-select into this product likely already know audio detection is imperfect. Trust the honest framing to manage expectations rather than trying to achieve false precision.

## Why Users Come Back in Week 2

Week 1: curiosity — "how bad is it really?"
Week 2 onward: the pattern view starts populating. After 7 nights, users see a chart that says "you scored 40% higher on nights with alcohol." That correlation — which SnoreLab doesn't surface and a voice memo can't produce — is the reason to keep logging. The experiment loop reinforces it: pick the factor, test it three nights, see the number move. That is a closed feedback loop with a personal variable under the user's control, not generic advice.

## Design Handoff

- **Screen 1: Onboarding / Setup** — purpose: explain what the app does, get mic permission, set bedtime reminder. Key elements: 3-step explainer (log, sleep, receipt), permission prompt, reminder time picker. No account required.

- **Screen 2: Pre-Sleep Log** — purpose: user logs context before bed. Key elements: 5 toggle rows (Alcohol tonight, Congested, Ate late, Late bedtime, Sleeping alone), a large "Start Recording" CTA button, estimated recording duration note (hours until alarm). Triggered by bedtime reminder or manual open.

- **Screen 3: Active Recording** — purpose: confirm recording is running and give user permission to put phone down. Key elements: pulsing mic indicator, elapsed time, "Recording will continue while your phone sleeps" note, Stop Recording button for early termination. Minimal — screen should be dismissible immediately.

- **Screen 4: Morning Receipt** — purpose: the daily payoff. Key elements: large snore score (0–100 scale), loudest clip player (waveform + play button), comparison bar showing last 7 nights with last night highlighted, tagged context factors from the previous night's log, "Start 3-Night Experiment" prompt if 3+ nights logged.

- **Screen 5: 7-Night Pattern View** — purpose: show correlations between context factors and scores. Key elements: bar chart of nightly scores, factor tags overlaid on bars, correlation callout ("Your 3 worst nights: all had Alcohol flagged"), no causal language — just "tends to correlate." Unlocks after 7 nights of data.

- **Screen 6: Experiment Mode** — purpose: structured 3-night test on one factor. Key elements: factor selector (pick the one to test), 3-night score tracker with before/after comparison, pass/fail summary at end ("Score dropped 30% — this factor seems to matter"). One experiment at a time only.

- **Screen 7: History / Calendar View** — purpose: browse past nights. Key elements: calendar with color-coded score dots, tap any night to see that night's receipt. Secondary screen, not primary nav.

**Key interactions:**
- Bedtime reminder → opens Pre-Sleep Log → tap Start Recording → phone goes to sleep
- Wake up → open app → Morning Receipt loads automatically (processes audio on first open)
- Receipt → tap "See Patterns" after 7 nights → Pattern View
- Pattern View → tap a factor → launches Experiment Mode for that factor
- Experiment completes after 3 nights → summary card on Receipt screen

## Version History
- v1: Original idea
- v1.1: Critique — posture detection unverified, SnoreLab clone with thin moat, action layer generic, retention unclear
- v2: Improved — cut posture detection, replaced with pre-sleep context log and correlation reveal after 7 nights; repositioned as honest habit tracker not health app; clear solo MVP scope
