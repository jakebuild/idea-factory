# Idea #36: Meal scanner that predicts your afternoon energy crash instead of just calories. (Improved)

## Version: v2
**Date:** 2026-04-01 12:17
**Status:** improved

## Original Idea
Meal scanner that predicts your afternoon energy crash instead of just calories. Snap a meal photo, get a Crash Score from 1-10, see the exact slump window it is likely to cause, and get a post-meal step target that offsets it. The app learns your own patterns over time, so it can say things like 'rice bowls usually crash you harder than wraps' and make every meal scan feel personal.

## What Changed and Why

- **Dropped fake precision entirely.** The critique nailed it: "exact slump window" and precise step targets are product fiction without real personal data. v2 replaces these with honest population-level heuristics (Low / Medium / High crash risk, clearly labeled as general guidance), and reserves any personal language for after the user has 5+ check-ins. This removes the trust-eroding fake authority problem.

- **Made the check-in the core product, not a data-collection side effect.** v1 buried the feedback loop. v2 makes the 90-minute "How's your energy?" push notification the main event — the thing users do every day, not onboarding friction. Without this loop there is no product. With it, the app earns real personal signal within a week of lunches.

- **Cut step targets, exact slump windows, HealthKit/Fit integration, and deep personalization claims from MVP.** These were the biggest complexity traps the critique identified. None of them survive the cut. v2 is: photo scan → heuristic risk label → 90-min check-in → personal pattern summary after enough data. That is the whole loop.

## Improved Description

**Slump Diary** is a meal + energy journal that helps you discover which of your specific lunches wreck your afternoon — and which ones don't.

You snap a photo of your meal. The app uses a food AI to categorize it (e.g., "high-carb bowl", "protein-heavy plate", "mixed takeout") and shows a population-level crash risk label: Low, Medium, or High — with a short honest rationale ("meals heavy in refined carbs tend to cause energy dips for most people"). No fake precision. It never claims to know your metabolism.

90 minutes later, a notification asks one question: **"How's your energy right now? 1–5."** That's it. Optional free-text note. 10 seconds. You tap and close.

After 7 check-ins, the app shows your first pattern summary: which meal types correlate with your low-energy ratings vs your high-energy ones. It uses your own data, speaks in ranges and tendencies, and never pretends to be a glucose monitor. "Your last 4 high-carb lunches averaged a 2.1 energy rating. Your protein-focused meals averaged 3.8."

That's the product. The value is in the pattern reveal, not the prediction.

## Why This Is Worth Building (Solo + AI)

The core loop is simple: photo → category → notification → 5-tap check-in → pattern. A solo dev can build this in under 3 weeks using an LLM vision API for meal classification and standard mobile push notifications. The value proposition is honest and doesn't require a validated ML model or wearable data — just consistent user behavior that gets more interesting over time.

## Assumptions Check

1. **Users will complete the 90-minute check-in more than once.** UNVERIFIED. This is the product's single biggest risk. If users ignore the notification after day 2, there is no pattern and no retention. Fallback: make the notification framing feel like a 2-second habit ("Rate your afternoon energy — takes 3 taps") and test completion rate in week 1. If it drops below 40%, the product needs a rethink before investing further.

2. **Photo-based meal categorization is good enough for rough heuristics.** VERIFIED. LLM vision APIs (Claude, GPT-4o) can reliably identify major meal archetypes (high-carb, protein-heavy, mixed, fried, light salad) even from dim restaurant shots. The categorization doesn't need to be precise — just consistent enough to group meals meaningfully over time.

3. **Users will find their own patterns meaningful enough to keep logging.** UNVERIFIED. There's a real risk that after seeing "carb-heavy meals = low energy" a user shrugs and says "I knew that." Mitigation: the value framing should be "confirm or challenge your intuitions with your actual data" — not "discover a secret." The pattern summary should feel validating, not obvious.

## Solo MVP Scope

**What's in v1:**
- Meal photo capture → LLM vision call → meal category + population-level crash risk label (Low / Medium / High)
- 90-minute post-meal push notification with a 1–5 energy rating tap
- Optional free-text note on check-in
- Log history (photo thumbnail + category + energy rating)
- Pattern summary view: unlocks after 5 check-ins, shows meal categories vs average energy ratings
- Basic onboarding: set your typical lunch window, enable notifications

**What's out of v1 (cut per critique):**
- Exact slump window — cut completely
- Step target / offset recommendation — cut completely
- HealthKit / Google Fit integration — cut completely
- "Learns your patterns" before enough data — cut completely; early state is always honest ("keep logging to unlock patterns")
- Crash Score 1–10 as a precise metric — replaced by Low/Medium/High population heuristic
- Any claim of personalized forecasting before 5+ check-ins

**Estimated build time with AI coding tools:** 2.5–3 weeks
- React Native or SwiftUI mobile app shell + navigation: 3 days
- Camera capture + LLM vision meal classification integration: 3 days
- Push notification scheduling (90-min post-scan): 2 days
- Check-in flow + rating storage: 2 days
- Pattern summary view (aggregation + display): 2 days
- Log history feed: 1 day
- Onboarding flow: 1 day
- Polish + edge cases (low light photo, unknown meal, first-use state): 2 days

## Open Questions

- Will users complete the 90-min check-in consistently enough to generate meaningful data within 2 weeks? (Test this manually first — run a week-long check-in experiment via a simple form before building the app.)
- What is the minimum number of logs before the pattern summary feels personally meaningful vs. statistically meaningless? (5 is a guess; 10 may be more credible.)
- How do you handle meals scanned at dinner or breakfast that have no 90-min afternoon context? (v1: only prompt for lunch-window scans, skip others.)
- Does the population-level heuristic label actually help users on day 1, or does it feel too generic to be worth opening the app for? (Risk: if the first scan result feels useless, there's no reason to return for check-in.)

## Why Users Come Back in Week 2

The 90-minute notification IS the retention mechanic. Every scan creates a scheduled re-engagement 90 minutes later. Users return not to scan again, but to close the loop on a scan they already made. After 5–7 loops, they unlock their first pattern summary — that's the payoff moment the whole early experience is building toward. The product should make that unlock feel like a small revelation, not a chart dump.

## Design Handoff

Every screen the app needs:

- **Screen 1: Onboarding (Step 1 of 2)** — What the app does in one sentence ("Track your meals and energy to find out which lunches tank your afternoon"). Single illustration. "Get started" CTA.

- **Screen 2: Onboarding (Step 2 of 2)** — Set your typical lunch window (time picker, default 12:00–13:00). Enable notifications toggle with explanation ("We'll check in 90 minutes after you scan a meal"). "Done" CTA.

- **Screen 3: Home / Scan** — Large camera button centered ("Scan your meal"). Below it: recent log list (thumbnail + meal category + energy rating badge + date). If no logs yet: empty state with prompt to scan first meal. Top right: link to Patterns view (grayed out until 5 logs).

- **Screen 4: Meal Scan / Preview** — Camera viewfinder with capture button. After capture: preview photo + "Use this photo" / "Retake" options.

- **Screen 5: Meal Result** — Shows photo thumbnail, AI-detected meal category label (e.g., "High-carb bowl"), crash risk badge (Low / Medium / High with color: green / amber / red), short 1-sentence rationale ("Meals like this tend to cause energy dips for most people"). Small disclaimer: "This is general guidance, not personal to you yet." CTA: "Done — I'll check back in 90 min." Log is saved; notification is scheduled.

- **Screen 6: Check-in Prompt** — Triggered by notification (also accessible from Home log items). Shows meal thumbnail + category from 90 min ago. Single question: "How's your energy right now?" 1–5 tap rating (emoji scale: exhausted → energized). Optional text note field. "Save" CTA. If dismissed: "Remind me in 15 min" option.

- **Screen 7: Log Detail** — Meal photo, category, crash risk label, energy rating (if check-in completed), date/time, user note. Read-only view.

- **Screen 8: Log History** — Reverse-chronological list of all scans. Each row: thumbnail, meal category, energy rating badge (or "Pending" if no check-in yet), date. Tap → Log Detail.

- **Screen 9: Patterns (Locked State)** — Accessible from Home. Shows lock illustration + "Log 5 meals with energy check-ins to unlock your patterns." Progress indicator (e.g., "2 of 5 complete").

- **Screen 10: Patterns (Unlocked State)** — After 5+ check-ins. Simple horizontal bar chart or list: each meal category the user has logged, with their average energy rating. Highlight best and worst. Copy: "Based on your last [N] check-ins." No predictive language — only retrospective summaries.

**Key interactions:**

- Scan → Result → automatic 90-min notification scheduled (no extra user action required)
- Notification tap → deep-links directly to Check-in Prompt for the matching meal log
- Home screen log item tap → Log Detail (if no check-in yet, shows "Rate your energy" CTA inline)
- Patterns unlocks progressively: locked until 5 check-ins, then live-updating as more logs come in
- Onboarding is skippable after step 1 (notifications can be enabled later via Settings)

## Version History
- v1: Original idea
- v1.1: Critique — fake precision kills trust, cold-start kills retention, step targets and exact slump windows are unverifiable, MVP scope too large for solo build
- v2: Improved — dropped all fake precision, made 90-min check-in the core loop, cut step targets / slump windows / wearable integrations, honest heuristics on day 1, pattern summary unlocks after real data
