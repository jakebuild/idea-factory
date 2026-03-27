# Idea #9: (Improved)

## Version: v2
**Date:** 2026-03-20 19:59
**Status:** improved

## Original Idea
a pomodoro timer that text your friend when you get distracted

## What Changed and Why

- **Drop distraction detection entirely** — The critique correctly identified this as a technically unfeasible dead end on mobile. iOS/Android platform restrictions make reliable background app monitoring impossible without app store rejection risk. Instead, detection is replaced with session-end reporting: when you complete or abandon a focus session, that outcome is what gets shared. "You left the app mid-session" is the one technically honest signal available, and framing it as session completion rate is both honest and more useful to the friend anyway.

- **Replace one-way spam texts with a shared async dashboard + opt-in digest** — The critique nailed the friend fatigue problem: unsolicited texts from an unknown number is legally and socially a dead end (TCPA, spam, opt-out after day 3). The new model gives the friend a lightweight companion view — a simple link they open to see your focus streaks and session history, like a shared scoreboard. Texts become a single opt-in daily digest ("Giang completed 4/6 sessions today"), not per-distraction spam. The friend consents once and gets one message per day max.

- **Add a reciprocal model so both parties have skin in the game** — The critique asked why the friend has any incentive to stay engaged. Answer: they don't, unless they're also a user. The improved model makes it a two-person commitment contract: both people run sessions and both see each other's data. Now the friend has a reason to care — it's mutual accountability, not passive surveillance. This also solves the "two users to acquire per one retained" problem by making it the explicit value prop: you get the app, then you recruit exactly one partner, and you both benefit.

## Improved Description

**Focus Pact** is a Pomodoro timer built for pairs. You invite one person — a friend, coworker, or study partner — and you both commit to a shared weekly focus goal (e.g., 20 sessions each). Every session you run, they can see it. Every session they run, you can see it.

There is no distraction detection. The app tracks what it can measure honestly: did you start a session, did you finish it, and how many did you complete today. When you abandon a session early, it logs as incomplete. Your partner sees your completion rate, not a real-time distraction alert.

Communication is minimal and opt-in. Each person gets one daily SMS digest at a time they choose ("You: 3/5 sessions. Alex: 5/5 sessions. 🔥 Alex is ahead."). No per-event spam. No unknown-number texts — the friend signs up with a phone number and confirms opt-in before receiving anything. TCPA compliance is built into the flow, not bolted on.

Monetization is a flat $4/month per user (both partners pay, or one pays for both). This covers SMS costs (~$0.0075/message × 30 days = ~$0.23/user/month) with healthy margin. No free tier with SMS — free tier gets email digest only.

The MVP is deliberately small: Pomodoro timer + session history + one partner link + daily digest. No distraction detection, no social feed, no gamification beyond the shared scoreboard. Ship it and measure whether pairs retain better than solo users — that's the hypothesis worth testing.

## Why This Is Now Worth Building

The revised model removes every technically infeasible component (background distraction detection) and every legally risky one (unsolicited SMS to non-consenting numbers), replacing them with things that are straightforward to build: a timer, a session log, a shared view, and a daily digest with confirmed opt-in. The reciprocal accountability model is the real differentiator — Forest and Focusmate don't do pairs-with-shared-data — and it converts the "two users per acquisition" problem from a weakness into the core mechanic. The monetization math works at small scale, which means you can prove the retention hypothesis before needing to grow.

## Open Questions

- Do pairs actually retain better than solo users, or does mutual accountability still decay after a few weeks? This is the central hypothesis and needs to be measured before expanding.
- What is the right daily digest cadence — end of day, or morning recap of yesterday? User research needed.
- Does the "both pay" model kill conversion when one partner is skeptical? Consider a 30-day free trial for the invited partner to reduce friction.
- How do you handle asymmetric commitment — one person runs 5 sessions/day, the other runs 1, and the low performer feels shame-spiraled out? Need a way to set different personal goals within the same pair.
- What happens when a partnership dissolves (breakup, job change, falling out)? Users need a graceful exit that doesn't feel like abandoning the other person.
- Is SMS the right channel or would WhatsApp / iMessage be better for international users and lower cost?

### Critique

**Score: 7/10 — WORTH BUILDING**

The v2 pivot is disciplined and correct: dropped distraction detection, replaced SMS spam with opt-in daily digest, added reciprocal accountability model, and did the SMS cost math honestly. The "pairs with shared data" angle is a real gap in the market that Forest and Focusmate don't fill.

Key remaining risks: the two-users-per-acquisition cold start problem is reframed but not solved (60-80% of signups may experience a useless product if their partner doesn't convert); the daily digest will become noise within 2 weeks without a shared consequence or stakes; session completion is gameable and measures app compliance, not real focus; and $4/month recurring is a hard sell in a market conditioned to free or one-time pricing. The partnership dissolution problem will drive emotional churn that suppresses re-engagement. The central hypothesis — pairs retain better than solo users — is still unvalidated and should be tested with a no-code MVP before building.

## Version History

- v1: Original idea
- v1.1: Critique - distraction detection is technically impossible on mobile, friend has no incentive, SMS costs unaddressed, TCPA risk, market saturated
- v2: Improved - dropped distraction detection, replaced one-way SMS spam with reciprocal pair model and opt-in daily digest, added clear monetization
