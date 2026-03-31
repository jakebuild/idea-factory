# Idea #34: Book Pledge - bet on your reading goal (Improved)

## Version: v3
**Date:** 2026-03-31 08:34
**Status:** improved

## Original Idea
Book Pledge - bet on your reading goal - pick a book and set a deadline - stake something real like donate to cause you hate, post embarrassing photo, message your ex - hit goal you win, miss it consequence triggers automatically - consequences beat willpower - accountability without accountability buddy

## What Changed and Why

- **Cut all real money from MVP entirely — Stripe, refunds, deposits, and charity all deferred to v2.** The critique was unambiguous: the Stripe compliance question is UNVERIFIED, manual charity transfer is a credibility grenade, and the refund state machine is where "2 weeks" becomes "I run a tiny financial complaints desk now." Without money, those risks disappear completely. The MVP now validates the commitment loop first, adds payment only after real users prove the weekly re-pledge flow works.

- **Replaced financial stakes with a permanent commitment record — your pledge history IS the consequence.** The psychological pressure of a visible streak and a logged failure record is real without being legally complicated. Users don't want to see "Week 3: MISSED" on their own reading history. The "consequence" is reputational with yourself — the same force that drives GitHub contribution streaks and Duolingo streaks — not a charity transfer nobody believes happened.

- **Shifted to email-first interaction to remove build complexity and validate demand faster.** A full web app with auth, Stripe, webhooks, and state machines is 10+ days of build. An email-based commitment flow — pledge setup form, daily one-click check-in links in email, weekly summary with re-pledge button — is 7 days and tests the exact behaviors that matter: will users actually check in daily, and will they re-pledge weekly? If they do, add a web dashboard and payment in v2. If they don't, nothing was wasted.

## Improved Description

ReadPact is a reading commitment tracker built around one repeating loop: pledge a reading goal for the week, check in after each session via a single email link, and get an honest weekly summary showing exactly what you said you'd do versus what you actually did.

No money changes hands in v1. The stake is your own commitment record — a permanent, private log of every week you hit your goal and every week you didn't. This is psychologically real: a streak of broken pledges is uncomfortable to look at, even when only you can see it. That discomfort is the motivator, not a charity payment nobody believes was processed correctly.

The weekly loop: every Monday the app sends a pledge confirmation email ("This week: 3 sessions of 30 minutes — book: The Obstacle Is the Way"). Each day you get a morning nudge with a one-click "I read today" link. Click it and the session is logged — no page number required, no friction. Sunday evening an honest summary lands in your inbox: "3/3 sessions hit — Week 4 streak. 47% through your book." Below it: a one-click re-pledge for next week.

Missed a week? The summary shows it plainly and offers a recovery path: "You missed 2 sessions. You can catch up next week by adding them to your pledge — same book, slightly higher session count." This reframes failure as debt, not shame. You don't churn — you re-engage to clear the debt.

The product is fully email-driven in v1. There is a web page for initial pledge setup and a minimal dashboard to view history, but the primary interaction surface is email. This keeps the build scope honest and puts the app where readers already are.

## Why This Is Worth Building (Solo + AI)

A solo dev can ship this in under 2 weeks: it is a form, an email sequence, a cron job, and a dashboard. No payment processing, no state machine with money attached, no Stripe webhook edge cases. The hardest technical piece is reliable email delivery (solved by Resend or Postmark) and a weekly cron that evaluates session counts and generates summary emails. AI coding tools handle all the scaffolding.

It works because the core insight — making a commitment visible and trackable creates psychological pressure to follow through — does not require money to be true. Beeminder users pay because the app already proved the commitment loop works. ReadPact proves the loop first, charges later.

## Assumption Audit

1. **"Users will click a daily email link to log reading sessions without a full app"** — UNVERIFIED. This is the top risk: email open rates vary and daily check-in friction via email may be too high. **Fallback:** Web dashboard check-in button as primary path, email as reminder only. Do not treat email as the only check-in method.

2. **"A visible commitment record (streak + failure log) creates enough psychological pressure without money"** — UNVERIFIED. This is the core differentiator bet. If users say "who cares, I just reset my streak," the product has no teeth. **Fallback:** Add a minimal social layer — share your weekly summary card to Twitter/X as an optional brag/shame post. This is opt-in and not coercive, so it survives the v1 scope cuts. Do not present the streak alone as proven until 20+ users complete at least 3 consecutive pledge cycles.

3. **"Users who miss a week will use the recovery/debt mechanic instead of churning"** — UNVERIFIED. Failure recovery is the hardest UX problem in habit apps. **Fallback:** Survey the first 20 users who miss a week. If 80%+ churn immediately, the product needs a stronger re-engagement hook before adding money.

## Solo MVP Scope

**What's in v1:**
- Landing page with pledge setup form (book title, sessions/week, minutes/session, start date)
- Email auth (magic link — no password to manage)
- Daily check-in email with one-click session log link
- Weekly summary email: sessions hit/missed, streak count, % through book (by page if entered, by weeks elapsed if not), re-pledge CTA
- Minimal web dashboard: current pledge status, streak count, full history table
- Recovery mechanic: missed week shows "reading debt" and offers adjusted next-week pledge
- Email reminders: Monday pledge confirmation, daily morning nudge, Sunday evening pre-summary warning if sessions are missing

**What's cut from v1 (honoring all prior critique cuts):**
- Stripe, payment processing, deposits, refunds — CUT (deferred to v2, after demand validation)
- Charity selection and donation transfer — CUT (critique called manual transfer a credibility grenade)
- Auto-messaging anyone (friends, ex, anyone) — CUT
- Auto-posting to social media — CUT
- Embarrassing photo storage — CUT
- Full-book completion verification — CUT
- Book database / ISBN lookup / cover art — CUT
- Social features, leaderboards, friends — CUT
- Multiple simultaneous pledges — CUT
- Mobile app — CUT (web + email only)
- Page-number check-in as required proof — CUT (optional only, no validation)

**Estimated build time with AI coding tools:**
- Landing page + pledge setup form: 1.5 days
- Magic link auth + user model: 0.5 days
- Daily check-in link handler (click → logs session in DB): 1 day
- Email sequences: pledge confirmation, daily nudge, Sunday warning, weekly summary (Resend): 2 days
- Weekly cron job: evaluate sessions, generate summary, trigger re-pledge email: 1 day
- Web dashboard: streak display, history table, missed-week recovery prompt: 1.5 days
- Deploy + end-to-end testing: 1 day
- **Total: ~8.5 days / under 2 weeks**

## Retention Answer

**Why does the user come back in week 2?**
Two mechanisms. First: the re-pledge CTA is in the Sunday summary email — one click, 30 seconds, zero friction. The user is already reading the email when the prompt arrives. Second: the streak and history table on the dashboard create the same pull as a GitHub contribution graph — a visual record you don't want to break. The missed-week recovery path keeps users engaged after failure instead of shaming them out. Users who see "Week 3: MISSED — add 2 catch-up sessions next week to clear your debt" have a concrete reason to return, not just guilt.

## Open Questions

- What daily check-in open and click rates are needed for the commitment loop to feel real? If most users ignore the daily email after day 3, the product needs a web-first check-in with email as backup only.
- Does the streak + failure record create enough psychological pressure without money, or does demand evaporate once users realize there are no real stakes? Answer this before building Stripe integration.
- What is the minimum re-pledge rate (week 1 → week 2) that signals product-market fit? Hypothesis: 40% re-pledge is enough to warrant adding payment in v2. Track this from day 1.
- If adding an optional share-your-summary-to-X feature, does it boost or kill re-pledge rates? This is the one social mechanic left on the table — needs a quick A/B test in v1.5.

## Design Handoff

Every screen the app needs:

- **Screen 1: Landing Page** — headline: "Make a reading commitment you'll actually keep", 3-step how it works (pledge → check in daily → see your record), single CTA "Start My Pledge", social proof placeholder (e.g., "42 readers currently on a streak"), no login required to view
- **Screen 2: Pledge Setup Form** — book title (text input), sessions per week (selector: 2/3/4/5), minutes per session (selector: 20/30/45/60), optional: current page and total pages (for progress % display), pledge start date (defaults to next Monday), email input, "Send Me My First Pledge" submit button — this is also the signup flow
- **Screen 3: Magic Link / Email Confirmation** — "Check your inbox — we sent a confirmation link to [email]", no password, just a waiting state with a resend button
- **Screen 4: Dashboard / Home** — current book title, week progress indicator (X of Y sessions done this week), streak badge (N weeks in a row), % through book if pages entered, days left in current week, "Log a Session Now" primary CTA (web check-in button, not just email), "View My History" link, subtle "Missed last week? Recover here" prompt if applicable
- **Screen 5: Session Log Confirmation** — shown after clicking check-in link from email or dashboard CTA — "Session logged ✓", shows updated sessions remaining this week (e.g., "2 of 3 done — 1 left"), a short motivational line tied to progress (e.g., "47% through — keep going"), "Back to Dashboard" link
- **Screen 6: Weekly Summary Email View** — also accessible as a web page — sessions hit vs. pledged, streak update (maintained or broken), pages read estimate (if pages entered), honest outcome label (GOAL MET / GOAL MISSED), if missed: reading debt prompt with adjusted next-week suggestion, "Pledge Next Week" CTA button (one click, pre-filled)
- **Screen 7: Pledge History** — table of all past weeks: week dates, book, sessions pledged vs. completed, outcome badge (Met/Missed), current streak, lifetime sessions completed, pages read total estimate
- **Screen 8: Recovery / Catch-Up Pledge** — triggered when user missed previous week — shows "Reading Debt: 2 sessions missed", pre-fills next week with original sessions + missed count, user can accept or reduce, "Restart My Streak" CTA
- **Screen 9: Settings** — email frequency preferences (daily nudge on/off, Sunday warning on/off), notification time preference, account deletion

**Key interactions:**
- Landing → Pledge Setup → Magic Link email → Dashboard (first-time onboarding, ~2 minutes)
- Morning email nudge → click "I read today" → Session Log Confirmation page → back to inbox (daily habit loop, ~5 seconds)
- Sunday summary email → review results → click "Pledge Next Week" → Dashboard (weekly re-pledge, ~30 seconds)
- Missed week: Sunday summary → Recovery Pledge screen → adjusted next-week pledge → Dashboard (failure recovery path, keeps user in app)
- Dashboard → "Log a Session Now" → Session Log Confirmation (web-first check-in fallback if email is ignored)

## Version History

- v1: Original idea — full-book deadline bet with auto-consequences including ex messaging and embarrassing posts
- v2.1: Critique (5/10) — Stripe compliance UNVERIFIED kills the payment loop; manual charity transfer is a trust grenade; self-reported check-ins too weak with real money at stake; guilt product has bad retention; build complexity understated
- v3: Improved — cut all money from MVP entirely; commitment record (streak + failure log) replaces financial stakes; email-first interaction model drastically reduces build scope; missed-week recovery reframes failure as debt not shame; demand validation before Stripe ever gets added
