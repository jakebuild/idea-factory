# Idea #35: Dating Profile Bio Rewriter — App-Specific (Improved)

## Version: v2
**Date:** 2026-04-01 10:58
**Status:** improved

## Original Idea
Dating profile scorer and roaster — upload your bio and photos, AI scores your profile 1-10 and tells you exactly why you're getting ghosted, then rewrites your bio for free. One-time 3.99 unlock for photo optimization tips. Shareable roast card with your score drives organic TikTok distribution.

## What Changed and Why

- **Cut photos entirely.** The critique flagged photo handling as the biggest solo complexity trap: storage, NSFW moderation, privacy anxiety, and user hesitation kill the funnel before it starts. Text-only is cleaner, faster to build, and avoids every privacy landmine. Photos are explicitly out of v1.
- **Replaced fake 1-10 score with structured breakdown.** "6.4/10" is invented precision that breeds distrust and arguments. Instead, the audit evaluates four specific dimensions: first impression, personality signal, specificity, and matchability. Each dimension gets a short verdict and a fix. That feels actionable, not arbitrary.
- **Flipped the monetization model: audit is free, rewrite pack is paid.** Giving away the bio rewrite while charging for vague photo tips was backwards — the free part solved the job, so nobody bought. Now the free audit shows what's broken, and the $7.99 paid unlock delivers 3 bio variants + 3 app-specific prompt answers. You're charging for transformation, not tips. This also makes the paywall feel justified because users already trust the audit quality.
- **Cut the roast angle as the primary positioning.** The critique correctly identified the narrow tuning window: too mean = refunds and backlash, too soft = nobody shares. The new angle is "get a real critique and better bios" — honest, direct, outcome-focused. A light "this needs work" tone replaces the performative dunking.
- **Added a light retention loop: weekly refresh nudge.** The critique's hardest hit was zero retention. The fix: after purchase, users get a "refresh reminder" email at day 14 and day 30 suggesting they update their bio after new matches, a new city, or a seasonal refresh. No user-generated content, no contributions, no moderation — just a simple email drip and a re-entry flow that lets returning users skip the full audit and go straight to the rewrite pack.
- **Narrowed the audience to Hinge users specifically.** Generic dating advice is mush. Hinge has prompt-based profiles with known structural weaknesses (lazy one-word answers, generic openers, overused prompts). Targeting Hinge specifically lets the AI give prompt-aware rewrites, not vague suggestions. This is the strongest single differentiator over just pasting your bio into ChatGPT — the app knows Hinge's format and gives you answers that fit the actual character limits and prompt categories.

## Improved Description

**Bio.Fix** — paste your Hinge bio and prompt answers, get a structured audit of what's killing your match rate, then unlock 3 custom rewritten bios and 6 prompt answer rewrites for $7.99.

The free audit breaks your profile into four dimensions: first impression (does the opener hook?), personality signal (do you sound like a real person or a LinkedIn summary?), specificity (do you name actual things or float in vague adjectives?), and matchability (are you making it easy to start a conversation?). Each dimension gets a one-paragraph verdict and one concrete fix.

The $7.99 paid rewrite pack gives you:
- 3 bio variants in different tones (warm, witty, direct)
- 6 Hinge prompt rewrites chosen from your worst-performing answers
- App-specific formatting: answers calibrated to Hinge character limits and the conversational dynamic of prompt-based profiles

No photos. No account required. Paste text, pay once, get better bios. Return when your profile needs a refresh.

## Why This Is Worth Building (Solo + AI)

Text-in, text-out is the simplest possible product shape for a solo dev — no file storage, no moderation queue, no privacy infrastructure. The Hinge-specific angle is narrow enough to give the LLM a real constraint to work within, which makes outputs noticeably better than generic rewrites, and that quality delta is the only moat you can build in 2–4 weeks. The $7.99 one-time purchase with clear deliverables (3 bios, 6 prompts) is easy to justify and easy to convert.

## Solo MVP Scope

**What's in v1:**
- Landing page with clear value prop and "paste your Hinge profile" CTA
- Free audit: structured 4-dimension breakdown with verdicts and one fix per dimension
- Paywall: $7.99 unlocks rewrite pack (3 bio variants + 6 prompt rewrites)
- Payment via Stripe (one-time, no subscription)
- Email capture post-purchase for the 14-day and 30-day refresh nudge (simple drip, no fancy automation)
- Results page with copy-to-clipboard for each rewrite

**What's out of v1:**
- Photos — cut entirely (critique hard cut, no re-entry under any framing)
- 1-10 score / roast card / share mechanic — cut (fake precision, unreliable virality)
- TikTok distribution strategy — cut (prayer, not a strategy)
- Support for other dating apps (Bumble, Tinder, etc.) — post-v1 only
- Account system / saved profiles — not needed for one-time purchase flow
- Match conversation feedback / opener generation — out of scope
- Photo optimization tips — cut (the original paid feature, now replaced)

**Estimated build time with AI coding tools:** 10–14 days
- Days 1–2: Landing page + profile paste UI
- Days 3–5: LLM prompt engineering for audit (4-dimension breakdown, tuning for quality and tone)
- Days 6–7: LLM prompt engineering for rewrite pack (3 bio variants, 6 prompt rewrites, Hinge-aware)
- Days 8–9: Stripe integration + paywall gating + results display
- Days 10–11: Email capture + simple drip setup (Resend or similar)
- Days 12–14: Polish, edge cases, copy, deploy

## Open Questions

- **Will the audit quality feel meaningfully better than ChatGPT?** This is the core unverified assumption. Need to run 10–20 real Hinge profiles through both and compare output quality before committing to the positioning. If the gap is small, the differentiation story collapses.
- **Is $7.99 the right price?** $3.99 felt like impulse, $14.99 might face more friction. $7.99 is untested — worth A/B testing on landing page copy before launch.
- **Will the 14/30-day email nudge actually drive re-purchases?** It costs almost nothing to build, but if retention relies on it, the return rate is unverified. Don't overclaim retention until real data exists.
- **Hinge character limits and prompt list accuracy.** Hinge prompt categories and limits change. The prompt engineering needs to be maintained if Hinge updates its UI. Low risk but worth noting.
- **How do users get here without paid ads?** Organic discovery is unverified. Reddit (r/hingeapp, r/dating_advice), Twitter/X, and TikTok creator seeding are possible channels but none are guaranteed. This is the biggest distribution open question.

## Key Assumptions Check

1. **"Users will find the audit meaningfully better than free ChatGPT."** — UNVERIFIED. This is the core differentiator. It is the product's only moat. Do not present it as proven. Run real comparisons before launch. Fallback: if the gap is small, lean harder on the UX (structured format, copy-paste ready outputs) as the value, not AI quality alone.

2. **"Users will pay $7.99 for a rewrite pack after seeing a free audit."** — UNVERIFIED. The free-to-paid conversion is the entire business model. The free audit must be good enough to create trust but incomplete enough to make the rewrite feel worth buying. This tuning is not trivial. Fallback: if conversion is low, offer the full pack upfront for $4.99 with no free tier.

3. **"Hinge-specific targeting produces noticeably better outputs than generic bio advice."** — UNVERIFIED but testable in 1 day of prompt work. If Hinge-specific constraints don't produce better outputs, the narrow positioning is marketing copy, not a real product difference.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Landing Page** — Hero with value prop ("Your Hinge profile is costing you matches. Find out why — free."), single CTA button ("Audit My Profile"), brief explainer of the 4-dimension audit, sample output screenshot, $7.99 rewrite pack pitch below the fold. No nav clutter.

- **Screen 2: Profile Input** — Text paste area for bio + up to 6 Hinge prompt answers (label + answer pairs). Each prompt has a label field ("e.g. 'A shower thought I had recently'") and an answer field. Bio field at top. "Run Free Audit" CTA at bottom. Character count per field. No account required, no email gate here.

- **Screen 3: Free Audit Results** — 4-dimension breakdown displayed as a vertical list of cards. Each card: dimension name (First Impression / Personality Signal / Specificity / Matchability), a one-paragraph verdict, and one bolded concrete fix. Bottom of page: paywall CTA — "Get Your Rewrite Pack — 3 Bios + 6 Prompts — $7.99". Clear description of what's included.

- **Screen 4: Paywall / Checkout** — Stripe-powered. Shows exactly what you get (3 bio variants, 6 prompt rewrites, Hinge-formatted). Email field for receipt and refresh reminders. Pay button. No account creation friction.

- **Screen 5: Rewrite Pack Results** — Three bio variants displayed as labeled cards (Warm / Witty / Direct), each with a copy-to-clipboard button. Below: 6 rewritten prompt answers, each showing the original prompt label and the new answer with copy button. "Save these — come back in 2 weeks for a refresh" nudge with email capture if not already entered.

- **Screen 6: Email Confirmation / Thank You** — Simple confirmation that rewrites are ready, link back to results, soft reminder that they can return for a refresh in 14 days.

**Key interactions:**
- User lands on landing page → clicks "Audit My Profile" → enters bio + prompts → sees free audit results
- On audit results page → clicks "Get Rewrite Pack" → enters email + pays via Stripe → lands on rewrite results
- Copy buttons on rewrite results allow one-click copy of each bio variant or prompt answer
- Post-purchase email drip: day 14 email ("Time for a profile refresh?") and day 30 email both deep-link back to the profile input screen with a "returning user" flag that skips the audit and goes straight to the rewrite purchase flow

## Version History

- v1: Original idea
- v1.1: Critique — fake precision score, photo complexity trap, weak monetization, zero retention, roast tone too risky, no real differentiator over free LLMs
- v2: Improved — cut photos and score entirely, text-only Hinge-specific rewrite tool, audit-free/rewrite-paid model at $7.99, 4-dimension structured breakdown, email drip for retention
