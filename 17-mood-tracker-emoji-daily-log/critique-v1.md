# Critique v1 — Idea #17: Mood Tracker

**Verdict:** NEEDS MAJOR WORK
**Score:** 6/10

## What's Actually Good
- One-tap UX is genuinely good — low friction is the right call for a habit app
- Mood calendar with pattern detection is a real insight users would find useful
- Tiny scope — a solo dev could build the core in a week

## Brutal Feedback
- This market is saturated. Daylio, Bearable, Finch, Reflectly — all do this. "Simpler" is not a differentiation strategy when free apps already nail simplicity.
- Emoji alone is shallow data. Without knowing what "😔" means to each user, patterns are noise. "I'm always low on Mondays" requires context the app doesn't capture.
- No answer to: why would someone open this every day? Habit apps live or die by their retention hook. A calendar you check monthly isn't enough.
- One-line note is nearly useless for pattern detection — too unstructured for AI, too sparse for the user to reflect on.
- No monetisation path mentioned. Mood trackers either charge subscription (competitive) or sell data (ethical minefield).

## Key Questions
- What makes this stickier than just using the Notes app with an emoji?
- Who is the specific user — anxiety management, general wellness, productivity tracking? Each needs a different product.
- What does "see patterns" actually look like — a heatmap? AI summary? Weekly digest?

## Suggestions
- Pick one specific user problem: e.g. "I want to correlate mood with sleep/exercise" — give the app a job to do beyond logging
- Add a daily push notification with a contextual prompt ("How'd the gym session go?") to drive retention
- Make the pattern view the hero — if the calendar insight is genuinely useful, lead with that, not the logging
- Consider a widget so logging happens without even opening the app

## Solo Dev Reality Check
- Can one person ship this in 2–4 weeks with AI tools? **YES** — the core (emoji log + calendar view) is straightforward. The risk is building something nobody opens after day 3.
- Biggest traps: push notification scheduling on iOS (annoying), local storage vs cloud sync decision, and the retention black hole if you don't nail the daily hook.
