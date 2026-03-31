# Idea #34: Book Pledge - bet on your reading goal (Improved)

## Version: v2
**Date:** 2026-03-31 08:29
**Status:** improved

## Original Idea
Book Pledge - bet on your reading goal - pick a book and set a deadline - stake something real like donate to cause you hate, post embarrassing photo, message your ex - hit goal you win, miss it consequence triggers automatically - consequences beat willpower - accountability without accountability buddy

## What Changed and Why

- **Cut all harassment-style consequences entirely (ex messaging, embarrassing posts, coercive auto-actions).** The critique was right: these are lawsuit bait and platform policy violations, not features. The new consequence is a single safe mechanism — small weekly deposit that goes to a charity if you miss your sessions. This is legally clean, emotionally real, and technically buildable. It removes the entire abuse, harassment, and legal landmine surface.

- **Replaced "finish the whole book by deadline" with weekly reading sprint pledges.** The proof-of-completion problem for an entire book is unsolvable without surveillance. Weekly sprints (e.g., "3 sessions this week, 30 min each") sidestep it entirely. Self-reported page-number check-ins are good enough when the stakes are small — if users cheat themselves out of finishing a book, that's on them. The app tracks honesty signals lightly (page number must advance), not surveillance.

- **Made stakes an optional layer on top of a habit tracker, not the core product identity.** The critique said "if you remove auto-consequence, the differentiator disappears." The real differentiator is now the weekly re-pledge loop — each Sunday you commit to next week's sessions with a small financial stake. This creates a recurring commitment ritual, not a single anxiety event. Retention is baked in: you re-pledge weekly until the book is done.

## Improved Description

Book Sprint is a focused reading commitment app where you pick a book and pledge to complete a set number of reading sessions per week — say, 3 sessions of 30 minutes each. Before the week starts, you stake a small amount (e.g., $5–$10). Each session you check in by logging your current page number (proof you're actually progressing). Hit your sessions by Sunday night — your deposit is refunded. Miss them — the money goes to a charity from a pre-approved list you pick at signup.

No ex-messaging. No embarrassing posts. No coercion engine. Just a clean, repeatable commitment loop: pledge → read → check in → refund or donate → pledge again next week.

The weekly re-pledge is the retention mechanic. After each week ends, you see your streak, how far through the book you are, and you set up next week's commitment. The stakes are small enough to be psychologically real without being life-ruining — just enough friction to make skipping feel slightly worse than reading.

Stripe handles payment upfront: user pays weekly deposit, gets a full refund on success via Stripe's refund API. No escrow complexity, no holds. Simple charge → refund or keep flow.

MVP is fully self-reported, which is fine. The financial friction is the motivator, not the surveillance. A user who logs fake page numbers is only cheating their own reading habit — the app cannot stop that and does not try to. The only light validation: page number must be higher than previous check-in.

## Why This Is Worth Building (Solo + AI)

A solo dev can ship this in ~2 weeks: it is a form-driven CRUD app with Stripe checkout and a daily check-in screen. The weekly re-pledge loop is the one genuinely novel mechanic, and it requires no external APIs beyond Stripe. The hardest part — Stripe refund automation — is well-documented and can be handled with standard Stripe webhooks. AI coding tools make the boilerplate (auth, forms, scheduling, Stripe) fast to scaffold.

It works because small financial stakes have proven psychological weight (Beeminder, stickK). Targeting readers who already want to finish books but keep failing gives a warm audience who understands the value proposition immediately.

## Assumption Audit

1. **"Stripe refund flow is feasible for weekly pledge-and-refund without compliance issues"** — UNVERIFIED. Stripe supports refunds and charges cleanly, but a recurring "pay upfront, get refunded on success" pattern for a habit app has not been validated against Stripe's terms. Must verify before treating payment as solved. **Fallback:** MVP runs on manual honor system — user pledges publicly (no real money), real payment added in v2 after demand is proven.

2. **"Self-reported page-number check-ins are good enough proof for users"** — UNVERIFIED. Users may feel the app is toothless if they know others can cheat. **Fallback:** Lean into it explicitly — the app's copy says "we trust you, that's the point; you're betting against yourself." Frame it as a commitment contract, not surveillance.

3. **"People will pay $5–$10/week as a reading stake repeatedly"** — UNVERIFIED. This is the core demand assumption. **Fallback:** Build landing page with fake-door pledge flow first. Only build payment if 50+ people complete the pledge form.

## Solo MVP Scope

**What's in v1:**
- Book selection (manual entry: title + total pages)
- Weekly session goal setup (# of sessions, minutes per session)
- Daily check-in screen (log current page, mark session done)
- Weekly summary: sessions hit vs. missed
- Streak tracking (consecutive weeks fully completed)
- Stripe payment: weekly deposit at pledge start, auto-refund on goal met, no refund on miss (manual charity transfer for MVP)
- Email reminder day before week ends if sessions are missing
- Charity selection from a fixed list of 5 options (pre-vetted, no moderation needed)

**What's out of v1 (hard cuts from critique):**
- Auto-messaging anyone (ex, friends, anyone) — CUT
- Auto-posting to social media — CUT
- Embarrassing photo storage or sharing — CUT
- Full-book completion proof or verification — CUT
- Automated charity donation API — CUT (manual transfer in v1, automated in v2)
- Book database / ISBN lookup / cover art — CUT
- Social features, leaderboards, friends — CUT
- Multiple simultaneous book pledges — CUT
- Mobile app — CUT (web only)

**Build estimate with AI coding tools:**
- Auth + basic CRUD (book, pledge, check-in): 2 days
- Weekly pledge flow + session tracking UI: 2 days
- Stripe checkout + refund webhook: 2–3 days
- Email reminders (Resend or similar): 1 day
- Weekly summary + streak screen: 1 day
- Deploy + testing: 1 day
- **Total: ~10 days / 2 weeks**

## Retention Answer

**Why does the user come back in week 2?**
Every Sunday night the app prompts: "Week complete — set up next week's pledge?" This is a deliberate re-pledge moment. The user sees their page progress, their streak count, and how many weeks until the book is done at this pace. Re-pledging is a 30-second action. This creates a weekly ritual tied to actual progress through a book they already want to finish — intrinsic motivation reinforced by a tiny financial commitment.

## Open Questions

- Does Stripe's ToS allow a "pay upfront, get refunded on goal success" recurring flow for a personal commitment app? Must check before building payment layer.
- What is the minimum viable charity transfer mechanism that is not fraudulent? Options: manual bank transfer logged publicly, Stripe direct to charity, Benevity API. Must resolve before launch.
- Is $5–$10/week too low to feel real or too high to convert? Needs a survey or fake-door test to validate price sensitivity.
- Should the page-number check-in require a photo of the physical page? Would add friction but also credibility. Leave as open question for post-MVP user research.

## Design Handoff

List every screen the app needs:

- **Screen 1: Onboarding / Landing** — value prop headline, single CTA "Make a Reading Pledge", shows how it works in 3 steps (pledge → read → get refunded or donate), no sign-in required to see this screen
- **Screen 2: Sign Up / Log In** — email + password, or magic link email auth; minimal fields
- **Screen 3: New Pledge Setup** — book title input, total pages input, sessions per week selector (1–7), minutes per session selector (15/30/45/60), stake amount input ($5/$10/$20 options), charity picker (5 fixed options with logos), pledge start date (defaults to next Monday), "Start Pledge" CTA
- **Screen 4: Dashboard / Home** — current book title and cover placeholder, week progress bar (X of Y sessions done), current page vs. total pages, days left in week, streak count (weeks fully completed), "Check In" primary CTA, "View History" secondary link
- **Screen 5: Check-In Screen** — current page number input (must be ≥ last logged page), session timer (optional, runs in background), "Mark Session Done" CTA, motivational one-liner, shows sessions remaining this week after submit
- **Screen 6: Week Summary** — end-of-week recap: sessions completed vs. pledged, pages read this week, total pages read, streak update, outcome badge (Goal Met / Missed), refund confirmation or donation notification, "Pledge Next Week" CTA
- **Screen 7: Next Week Pledge** — same as Screen 3 but pre-filled with same book and same session defaults, one-tap confirm or adjust, Stripe checkout for new weekly deposit
- **Screen 8: Pledge History** — list of all past weeks: book name, sessions hit/missed, outcome, amount refunded or donated, running total donated to charity
- **Screen 9: Settings** — email notification preferences, charity preference, payment method management, account deletion

**Key interactions:**
- Onboarding → New Pledge Setup → Stripe payment → Dashboard (linear first-time flow)
- Dashboard → Check-In → Dashboard (daily loop, multiple times per week)
- Sunday night → Week Summary → Next Week Pledge → Stripe → Dashboard (weekly re-pledge loop, the retention core)
- Missed week → Week Summary shows donation outcome → "Try again next week" CTA → Next Week Pledge (failure recovery path keeps users in the app rather than churning)

## Version History

- v1: Original idea — full-book deadline bet with auto-consequences including ex messaging and embarrassing posts
- v1.1: Critique — KILL IT (3/10): auto-consequence execution is legally broken, harassment features are lawsuit bait, no retention loop, proof of completion unsolvable, too much surface for solo build
- v2: Improved — reframed as weekly reading sprint with small financial stake, self-reported page check-ins, Stripe refund-on-success, hard cuts on all harassment mechanics, weekly re-pledge as retention loop
