# Idea #9: (Improved)

## Version: v3
**Date:** 2026-03-20 20:01
**Status:** improved

## Original Idea
a pomodoro timer that text your friend when you get distracted

## What Changed and Why

- **Add a genuine solo mode so cold start doesn't kill the product** — The critique's hardest punch: 60-80% of signups will have a partner who never converts, leaving them with a useless product. The fix isn't a degraded fallback — it's a first-class solo mode where the user commits to a weekly session goal and gets a private streak with stakes (miss 2 days and the streak resets). Solo mode is the top-of-funnel product. The pair feature is the upgrade. Users experience real value alone first, then invite a partner to share it. This inverts the acquisition problem: instead of "invite someone or the app is useless," it's "the app works alone, but pairs make it more interesting."

- **Replace the passive daily digest with a shared pact that has real consequences** — "You: 3/5. Alex: 5/5." becomes noise in two weeks because there's no event driving urgency — no streak at risk, no penalty, just a number. The fix is a mutual streak: both partners are on one shared streak counter. If either person misses their daily minimum, the shared streak resets for both. This is genuine interdependence, not shared visibility. Now the daily check-in isn't a vanity metric — it's the thing protecting something you both built together. Notifications fire only on meaningful events: streak at risk in the next 2 hours, streak milestone hit, partner just ran their first session today.

- **Drop SMS as the default channel; make push + in-app primary, SMS optional premium** — The critique is right: SMS feels like 2018, international users drop off immediately, and an unknown number in 2026 gets treated as spam regardless of opt-in history. Push notifications are free, instant, and work globally. The revised model defaults to push + in-app partner view, with SMS as an optional add-on for users who specifically want the phone-interrupt experience. This also cuts per-user costs to near-zero at default tier, making a freemium model viable instead of requiring everyone to pay from day one.

- **Build the no-code MVP before writing any app code** — The critique explicitly called this out and it's correct. The central hypothesis — pairs retain better than solo users — is unvalidated. A shared Airtable form where two people log sessions daily, plus a Zapier-triggered email digest, tests the retention question in two weeks for effectively zero cost. If pairs don't retain better than solo users in a manual test, the core mechanic is wrong and the app doesn't get built. This is the kill condition defined in advance.

- **Design the graceful exit mechanic explicitly** — Partnership dissolution is predictable and will happen to most pairs within 60-90 days. Ignoring it means churn with emotional damage attached — people avoid re-engaging because the app reminds them of a failed commitment. The fix: when a pair goes quiet for 5+ days, the app proactively surfaces a "pause your pact" option (not a breakup, just a hibernate). Both users can resume solo mode independently. Ending a pact is a normal lifecycle event, not a failure state. The product should treat it that way.

## Improved Description

**Focus Pact** is a Pomodoro timer built around two modes: solo commitment and pair accountability.

**Solo mode (free):** You set a weekly session goal — say, 15 sessions — and commit to a daily minimum. The app tracks your streak. Miss your daily minimum and the streak resets. There's no partner required, no social component, just a clean timer with a streak worth protecting. This is the product most people start with.

**Pair mode ($3/month for the pair, split or paid by one):** You invite one person. Both of you set personal session goals within a shared pact. The pair runs on a shared streak counter: if either person misses their daily minimum, the shared streak resets for both. You each see the other's session history in a simple partner view — completion rate, current streak, sessions today. Notifications fire on events that matter: your partner just started their first session of the day, the shared streak is at risk in 2 hours, you hit a 14-day milestone together. No daily number digest; notifications only when something is actually happening.

**Channels:** Push notifications are the default. SMS is an optional add-on for users who want the phone-interrupt experience — it's not on by default, and international users never need it. All notifications require explicit opt-in.

**Gameable session completion:** The app measures what it can measure honestly — did you start and finish a 25-minute block. It cannot verify focus quality. The product doesn't pretend otherwise. The value is in the commitment structure, not surveillance. Users who game the timer are only cheating themselves, and the pair mechanic creates social pressure that passive solo apps lack.

**Graceful exit:** When a pair goes quiet for 5 consecutive days, the app surfaces a "pause your pact" option. Both partners drop to solo mode with their individual streaks intact. Resuming a pact later is a one-tap action. Ending a pact is a normal event, not a failure.

**Validation first:** Before any app code, run a no-code MVP: two people log sessions in a shared Airtable form, a Zapier zap sends a daily email digest. Run this with 5-10 pairs for 4 weeks. If pairs show meaningfully better 30-day retention than solo users (define "meaningfully" as >20% retention difference), build the app. If not, the hypothesis is wrong.

## Why This Is Now Worth Building

The cold start problem is solved by making solo mode a first-class product — users get value immediately without depending on a partner, and pair mode becomes an upgrade rather than a prerequisite. The shared streak mechanic with real consequences (both reset if either misses) creates genuine interdependence instead of passive visibility, fixing the "digest becomes noise" problem that killed v2's accountability loop. The no-code MVP requirement means the central retention hypothesis gets tested in two weeks for near-zero cost before any app development starts — if the hypothesis is wrong, you find out cheaply.

## Open Questions

- Does the shared streak reset mechanic create motivating pressure or resentment? The line between "we're in this together" and "you ruined my streak" is thin — this needs to be tested with real pairs before assuming positive framing.
- What is the right daily minimum for the streak to feel meaningful but not punishing? 1 session/day is very low; 3 sessions/day may be too demanding for casual users. This is a tuning problem that requires data.
- Does the freemium model (solo free, pair $3/month) cannibalize conversion — do people just stay on solo mode forever and never pay? If solo mode is too good, it removes the upgrade incentive.
- How do you find the first 10 pairs for the no-code MVP? This is a distribution problem, not a product problem, but it blocks validation entirely.
- When the no-code MVP is running, what exactly counts as "meaningfully better retention" — is it 30-day retention rate, total sessions per user, or streak length? Define the metric before seeing the data.
- Is $3/month/pair (i.e., $1.50/person) actually viable given even minimal infrastructure costs, or does the price need to be higher?

## Version History

- v1: Original idea
- v2.1: Critique - cold start unsolved, daily digest decays, gameable sessions, SMS wrong channel, no stakes
- v3: Improved - solo mode solves cold start, shared streak adds real consequences, push replaces SMS, no-code MVP gates app development
