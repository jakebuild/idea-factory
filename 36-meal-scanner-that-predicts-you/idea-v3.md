# Idea #36: Meal scanner that predicts your afternoon energy crash instead of just calories. (Improved)

## Version: v3
**Date:** 2026-04-01 12:20
**Status:** improved

## Original Idea
Meal scanner that predicts your afternoon energy crash instead of just calories. Snap a meal photo, get a Crash Score from 1-10, see the exact slump window it is likely to cause, and get a post-meal step target that offsets it. The app learns your own patterns over time, so it can say things like 'rice bowls usually crash you harder than wraps' and make every meal scan feel personal.

## What Changed and Why

- **Camera cut as primary input; replaced with 8-tap manual meal tags.** The critique correctly identified the camera as UX theater. Vision API categorization is unreliable on mixed dishes, office food, bad lighting, and leftovers — and "VERIFIED" was too generous a rating in v2. Manual tags (e.g., "Carb-heavy", "Protein bowl", "Salad", "Fast food", "Soup", "Light snack", "Mixed plate", "Fried/heavy") are faster, cheaper, self-assigned, and accurate by definition — the user knows what they're eating better than a photo classifier does. Optional photo attachment stays for context, but it is never run through an API.

- **Day-1 value fixed: history-based pre-log warning replaces population-level heuristics.** The "carbs might make you sleepy" heuristic was a fortune cookie. The new day-1 framing is honest about having no data yet: "We don't know how you react to this meal type yet — log your energy in 90 min and we'll start finding out." After a user has at least one prior check-in for a meal type, the app surfaces a personal warning before they log: "Last time you had a carb-heavy lunch, you rated your energy 2/5 at 2pm. Worth noting." This gives users help *before* the crash — fixing the motivation mismatch the critique identified — and it is grounded in their own history, not made-up population stats.

- **Pattern unlock delayed to 8-10 check-ins; patterns include one concrete next-step suggestion.** Five check-ins is statistically embarrassing. v3 requires 8 completed check-ins before showing any summary, shows explicit confidence language ("based on 8 data points — keep going for a clearer picture"), and adds one actionable suggestion per summary ("Your best lunch type: Protein bowl — try having it tomorrow and compare."). This converts a passive retrospective diary into a forward-looking personal experiment.

- **App reframed explicitly as a lunch-only experiment, not a general meal tracker.** The scope confusion from v1/v2 is killed. The app is called out clearly as a lunch energy lab. Breakfast and dinner are out of scope; the app says so in onboarding. This makes the value proposition crisper and removes the implied false promise of general meal tracking.

## Improved Description

**Lunch Lab** is a personal experiment for people who suspect their lunches are wrecking their afternoons but have never actually tracked it.

You open the app at lunch. You tap one of 8 meal type tags — Carb-heavy, Protein bowl, Salad, Fast food, Soup, Light snack, Mixed plate, Fried/heavy. If you've eaten this type before and rated your energy poorly, the app shows a quiet flag: "Last time: 2/5 energy." That's it. No heuristics. No fortune cookies. Just your own history.

90 minutes later, one notification: "How's your energy right now? Tap 1–5." Three taps, done. No essay required.

After 8 completed check-ins, your first pattern summary unlocks: a simple list of your meal types ranked by average energy rating, with a confidence note, and one concrete suggestion for tomorrow. "Your worst lunch: Carb-heavy (avg 2.1/5 across 4 logs). Your best: Protein bowl (avg 3.9/5). Try a protein bowl tomorrow and see if it holds."

That's the whole product. You run a two-week experiment on yourself. The app just remembers for you.

Lunch only. No breakfast. No dinner. No wearables. No calorie counts. No fake science.

## Why This Is Worth Building (Solo + AI)

The camera is gone, which removes the biggest solo complexity trap: no vision API, no photo upload failures, no classifier drift, no API costs. What's left is a form, a push notification, a local database, and a chart — buildable by one developer in under 3 weeks with AI coding tools. The value is honest and doesn't overclaim: it's a memory aid for a behavior experiment users can run in two weeks.

## Assumptions Check

1. **Users will complete 8 check-ins over two weeks (roughly one per working day).** UNVERIFIED — this is still the core risk and cannot be engineered away. Fallback: run a manual validation experiment first — share a Google Form with 5 friends, set a daily Slack reminder, and track completion for 10 days. If fewer than 3 of 5 complete 8 check-ins without an app, the behavior loop is broken and no amount of polish will fix it.

2. **Self-assigned meal tags are consistent enough across days to produce meaningful averages.** VERIFIED (roughly). Unlike camera classification, self-assigned tags are accurate by definition — the user knows their meal. The risk is inconsistency ("is this carb-heavy or mixed plate?"), mitigated by keeping tags broad (8 max) and showing examples in onboarding. Slight inconsistency degrades precision but doesn't break the product.

3. **Showing "last time you had this: 2/5 energy" will feel meaningful to users, not creepy or obvious.** UNVERIFIED. This is the pre-crash mechanic and the main differentiator from a plain diary. It could feel like useful memory augmentation, or it could feel like stating the obvious. Fallback: if early users report the warning feels noise-y, make it opt-in ("show me my history before I log"). Even without behavior change, it makes patterns feel actionable rather than purely retrospective.

## Why Users Come Back in Week 2

The pre-log history flag creates a compounding return: each new meal log is more useful than the last, because it either confirms or challenges a pattern. By day 5, logging takes 10 seconds but yields a flash of "oh right, I always crash after fast food — let me see if today is different." By day 10, the pattern summary is within reach. The flywheel is: past data → pre-log flag → new log → updated pattern — each log makes the next flag smarter. This is structurally stickier than a cold push notification with no payoff until an arbitrary chart threshold.

## Solo MVP Scope

**What's in v1:**
- Meal tag picker: 8 fixed tags, tap to select, single selection per log
- Optional photo attachment (camera roll only — no API, no classification)
- Optional free-text note on log
- Pre-log history flag: if user has a prior check-in for this meal type, show their average energy rating for it before logging
- 90-minute post-log push notification: one question, 1–5 energy tap
- Log history: reverse-chronological, tag + energy badge + date
- Pattern summary: unlocks at 8 completed check-ins; ranked meal types by avg energy; one concrete next-step suggestion; confidence note
- Onboarding: 2 screens — what the app does (lunch only), set lunch window, enable notifications

**What's out of v1 (cut and not returning under any name):**
- Camera-based meal classification via vision API — cut completely
- Population-level crash heuristics (Low/Medium/High) — cut completely
- Crash Score of any kind (1–10, Low/Medium/High, or otherwise) — cut completely
- Exact slump window — cut completely
- Step targets — cut completely
- HealthKit / Google Fit integration — cut completely
- Breakfast and dinner tracking — out of scope, app is lunch-only
- Pattern summary before 8 check-ins — hard floor, no exceptions
- Any language claiming the app "predicts" anything — framing is retrospective correlation only

**Estimated build time with AI coding tools: 3–3.5 weeks**
- App shell + navigation + local storage (SQLite or AsyncStorage): 3 days
- Meal tag picker screen + pre-log history flag logic: 2 days
- Optional photo attachment (camera roll, stored locally, no upload): 1 day
- Push notification scheduling (90 min post-log, cancel on check-in): 3 days (includes permission edge cases, background scheduling, cancel-on-complete)
- Check-in flow (notification deep-link → rating tap → save): 2 days
- Log history feed: 1 day
- Log detail view: 0.5 days
- Pattern summary view (aggregation + ranking + suggestion + confidence note): 2 days
- Patterns locked/progress state: 0.5 days
- Onboarding (2 screens): 1 day
- Edge cases + polish (first-use states, empty states, notification permission denied fallback, lunch window logic): 2 days

## Open Questions

- Manual validation first: can 5 real users complete 8 lunch check-ins over 2 weeks using a plain Google Form + Slack reminder? If not, the behavior assumption fails before any code is written.
- What is the right minimum before the pattern summary stops being embarrassing? 8 is a judgment call — 10 is more defensible. Should the app show the count prominently so users feel the data is earned?
- How do you handle the pre-log flag gracefully when a user only has 1 prior check-in for a meal type? ("Last time: 2/5 — only 1 data point, not a pattern yet.") Needs copy.
- What happens when users skip check-ins? After 3 missed notifications in a row, does the app ask if the timing is wrong and offer to adjust the check-in window?
- What does the one "next step suggestion" look like exactly — passive ("your best lunch was X") or instructional ("try X tomorrow")? Needs user testing; instructional may feel patronizing.

## Design Handoff

Every screen the app needs:

- **Screen 1: Onboarding (Step 1 of 2)** — Single illustration, headline: "Find out which lunches tank your 3pm." Subtext: "Log your lunch, rate your energy 90 min later. After 8 logs, we show you the pattern." Lunch-only disclaimer. "Get started" CTA.

- **Screen 2: Onboarding (Step 2 of 2)** — Set your typical lunch window (time range picker, default 12:00–13:00). Enable notifications toggle ("We'll check in ~90 min after you log a meal"). Explanation of what the notification will look like. "Done" CTA.

- **Screen 3: Home** — Centered "Log lunch" button (large, prominent). Below: recent log feed (meal tag label + energy rating badge or "Pending" + date). Top right: Patterns icon (locked with badge showing progress, e.g., "4/8", or unlocked). Empty state: prompt to log first meal. No statistics shown until 8 check-ins.

- **Screen 4: Log Meal** — Headline: "What are you having?" Grid of 8 meal tag buttons (icon + label): Carb-heavy, Protein bowl, Salad, Fast food, Soup, Light snack, Mixed plate, Fried/heavy. Single selection required. If user has prior check-ins for the tapped tag: show a quiet callout below the selection ("Your last [Carb-heavy] lunch: 2/5 energy on average — 3 logs"). Optional: "Add a photo" link (opens camera roll). Optional: short text note field. "Log lunch" CTA (disabled until tag selected).

- **Screen 5: Logged Confirmation** — Simple full-screen confirmation. Meal tag shown. Message: "Logged. Check back in at [time]." Countdown or just the time. "Done" CTA dismisses to Home. No heuristics. No predictions. Just confirmation and anticipation.

- **Screen 6: Check-in Prompt** — Triggered by notification deep-link (also accessible by tapping a "Pending" log item on Home). Shows the meal tag from 90 min ago. Single question: "How's your energy right now?" Five-tap rating row (1 = Wrecked → 5 = Energized, with emoji and label). Optional text note. "Save" CTA. Secondary: "Remind me in 15 min" link. If notification was dismissed: accessible from Home log list row with "Pending" badge.

- **Screen 7: Log Detail** — Meal tag, photo thumbnail (if added), energy rating (if check-in done, else "No check-in"), user note, date/time. Read-only. No edit.

- **Screen 8: Log History** — Reverse-chronological full list. Each row: meal tag icon + label, energy rating badge (color-coded 1–5 or "Pending" in gray), date. Tap → Log Detail.

- **Screen 9: Patterns — Locked State** — Accessible from Home. Progress visual: "[N] of 8 check-ins complete." Short copy: "Keep logging to unlock your lunch patterns." No preview data. No teaser chart. Progress bar only.

- **Screen 10: Patterns — Unlocked State** — Available after 8 completed check-ins. Ranked list of meal types the user has logged, sorted by average energy rating (best to worst). Each item: tag label, average rating (e.g., "3.9/5"), log count, mini bar. Confidence note: "Based on [N] check-ins — the more you log, the clearer the picture." One highlighted suggestion block: "Try this tomorrow: [best meal type]." Tapping a row shows all logs for that meal type.

**Key interactions:**

- Log Meal → Logged Confirmation → automatic 90-min notification scheduled (no extra step)
- Notification tap → deep-links directly to Check-in Prompt for the matching log
- Home "Pending" log item tap → Check-in Prompt for that log
- Pre-log history flag: tapping a meal tag on Log Meal screen immediately shows that tag's prior average below the grid (if data exists)
- Patterns screen live-updates as new check-ins are saved; locked state transitions to unlocked automatically at 8 without user action
- Onboarding is shown once on first launch; skippable after Step 1; notification permission can be enabled later via Settings

## Version History
- v1: Original idea
- v2.1: Critique — camera is UX theater, day-1 value is a fortune cookie, 5 check-ins is statistically flimsy, single push notification is not a retention moat, motivation mismatch (users want help before crash, not after)
- v3: Improved — camera removed as primary input (replaced with 8 manual meal tags), day-1 value fixed with pre-log personal history flag, pattern unlock delayed to 8 check-ins with one actionable next step, app framed explicitly as lunch-only experiment
