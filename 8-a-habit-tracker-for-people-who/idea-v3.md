# Idea #8: A Habit Tracker for People Who Hate Habit Trackers (Improved)

## Version: v3
**Date:** 2026-03-20 17:32
**Status:** improved

## Original Idea
a habit tracker for people who hate habit trackers

## What Changed and Why

- **The Sunday check-in becomes the product, not a notification.** The critique correctly identified that the widget is table stakes and the "what felt hard?" prompt is the one genuinely novel idea. V3 centers the entire product around a weekly adaptive coaching loop: you answer two questions, the app adjusts your targets automatically based on a pattern it detects (you always miss Wednesdays → your target recalibrates to skip Wednesdays). This is not a toggle that competitors offer. Habitica and Streaks let you turn off streak counters; none of them have a behavioral adaptation engine that reshapes your commitments around your actual life. That's the real moat — not streak removal, but a system that learns what "hard" means for you specifically.

- **Fixed the business model: subscription framed as brand alignment, not extraction.** $4.99 one-time is a dead-end. V3 switches to $1.99/month with the positioning: "We earn this every month or you cancel — no guilt." This is not just a revenue fix; it's a product statement that reinforces the core brand. A subscription model that explicitly tells you to cancel if it stops working is the anti-pattern of every guilt-subscription in this space. The recurring revenue funds ongoing development and makes the business viable past year 1. At 2,000 subscribers that's ~$34k ARR after Apple's cut — not a windfall, but a compounding foundation.

- **Added a shareable artifact to convert invisible success into word-of-mouth.** The critique nailed the core tension: users who "don't think about the app much" don't leave reviews or recommend it. V3 introduces a weekly consistency card generated after the Sunday check-in — a minimal, shareable image showing your streak-free progress in plain language: *"Week 14. Ran 3 of 5 days. Adjusted my target twice. Still going."* Imperfect consistency is more relatable than polished streaks; people share it because it's honest, not aspirational. This turns the anti-shame mechanic into a social loop without contradicting the product philosophy. The card is the viral primitive.

## Improved Description

**Most habit apps are optimized for the first 30 days. Roughly is optimized for month 4.**

The streak is not the only problem. The real problem is that habit apps are rigid — they're built for the best-case version of your week, not your actual week. So when life gets busy, the app doesn't adapt. You fall behind. You feel guilty. You quit.

**Roughly** is a habit tracker built around one weekly conversation: *what actually happened, and what should we adjust?*

**The core mechanic: the Sunday Check-In**

Every Sunday, the app sends one prompt — not a notification wall, one message. You answer two questions:
- *What felt hard this week?*
- *Do you want to adjust anything?*

Based on your answers and your actual log data, Roughly adapts your targets. If you always skip Wednesdays, it removes Wednesday from your target. If you hit your goal three weeks running, it asks if you want to level up. The app learns what consistent looks like *for you*, not for a hypothetical person with a perfect schedule.

This is not a setting competitors offer. It is a behavioral adaptation engine disguised as a check-in. Over 90 days, your habits become calibrated to your real life rather than the life you planned to have when you downloaded the app.

**The rest of the product:**
- 1–5 habits, hard cap (no list-building)
- Consistency windows, not streaks: "3 of last 7 days" is green, not a counter resetting to zero
- Widget shows current status — green/yellow — no numbers, no shaming
- No daily notifications, ever

**Shareable weekly card**

After each Sunday check-in, the app generates a card you can share: *"Week 14 of running. Hit target 3 of 4 weeks. Adjusted my target once. Still going."* Plain language, no aesthetics, no streak numbers. Imperfect consistency is relatable; that's the share hook.

**Pricing:** $1.99/month. The positioning line: *"We earn this every month, or you cancel. No guilt."* This is a subscription that explicitly tells you to cancel if it stops working — the opposite of every other productivity app's retention strategy, and a statement that reinforces the brand.

**Acquisition:** Three channels. (1) Apple Search Ads on "habit tracker no guilt" / "I keep failing my habit tracker" — test with $200 before building. (2) Product Hunt launch framed as "The habit tracker that adapts when life gets in the way." (3) Organic from shared weekly cards on Twitter/Threads — the imperfect consistency narrative is counter-programming to the hustle-culture streaks aesthetic.

**Name: Roughly.** Communicates approximate consistency without explanation. Confident, not defensive. No "un-" prefix required.

## Why This Is Now Worth Building

The weekly adaptive check-in — a system that reshapes your habits around your actual behavior rather than your ideal behavior — is a genuinely differentiated mechanic that competitors don't offer, not just a toggle they omit. The $1.99/month subscription gives the business a compounding revenue foundation and doubles as a brand statement ("cancel any time, no guilt") that reinforces the product's core philosophy. The shareable consistency card solves the virality problem without compromising the anti-anxiety positioning: imperfect progress is more relatable than polished streaks, and relatable content gets shared.

## Open Questions

- Does the behavioral adaptation engine require significant ML infrastructure, or is a simple rule-based system (if you miss the same day 3 weeks running, remove that day from target) sufficient to feel "smart"?
- What is the actual App Store search volume for "habit tracker no guilt" / "habit tracker anxiety"? Run $200 in Apple Search Ads on these terms before writing code — if CPI exceeds $4 on a $1.99/month product, the acquisition channel is broken.
- Is $1.99/month the right price, or does $2.99/month still feel low-friction enough while meaningfully improving unit economics? Test with a landing page before launching.
- The weekly card needs a design that reads as "honest and simple" rather than "ugly." Is there a minimal aesthetic that works here, or does this require a real designer?
- Find 5 real people who quit a habit tracker over streak shame and would pay $1.99/month for Roughly's model. DM r/productivity. If you can't find 5 in a week, stop.
- How does the adaptation engine handle conflicting signals — e.g., user says "felt hard" but actually hit their target? Does the system override the user's self-report, or does user intent always win?

## Version History
- v1: Original idea
- v2.1: Critique — thin moat (streaks are already a toggle), dead-end one-time pricing, virality impossible when success = user invisibility
- v3: Improved — adaptive weekly check-in as core mechanic (not a toggle), $1.99/month subscription as brand statement, shareable consistency card as viral primitive, renamed to "Roughly"
