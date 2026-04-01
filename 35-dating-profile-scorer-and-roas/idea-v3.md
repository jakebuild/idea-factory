# Idea #35: Hinge Profile Rewriter (Improved)

## Version: v3
**Date:** 2026-04-01 11:02
**Status:** improved

## Original Idea
Dating profile scorer and roaster — upload your bio and photos, AI scores your profile 1-10 and tells you exactly why you're getting ghosted, then rewrites your bio for free. One-time 3.99 unlock for photo optimization tips. Shareable roast card with your score drives organic TikTok distribution.

## What Changed and Why

- **Removed the free audit tier entirely.** v2's free audit was threading a needle with no room for error: strong enough to trust = users leave satisfied; weak = they don't pay. The critique called this out directly. v3 flips to a single upfront pay-once model ($12.99 gets everything: audit + rewrite). No needle to thread. The before/after landing page samples replace the "trust-building" role the free audit was playing. This also eliminates the messy "returning user skip the audit" state management that was quietly adding account-system complexity.

- **Hard-coded Hinge UX constraints into the product itself.** v2 claimed "Hinge-specific" as a differentiator but delivered it only through prompt engineering — invisible to the user, indistinguishable from ChatGPT at the surface. v3 puts the specificity into the UI: a dropdown of actual Hinge prompt categories (not a freeform text box), per-field character counters with Hinge's real limits, and a cliché detector that flags banned phrases inline before submission. Now "Hinge-specific" is something users can see and feel, not just a marketing claim.

- **Accepted one-shot utility framing; dropped email retention theater.** The critique was direct: day-14 and day-30 reminder emails are not a retention loop. v3 does not pretend otherwise. This is a one-shot profile optimization tool. Revenue model is new-user volume, not repeat purchases. Removing the drip setup saves 2–3 days of build time and removes a lie from the product positioning. If data later shows re-purchase intent, a discount re-run flow can be added post-launch.

- **Shrunk the deliverable to quality over quantity.** "3 bios + 6 prompt rewrites" signals a bulk bundle, which invites the "two are bland and one is cringe" disappointment the critique predicted. v3 delivers 1 best bio + 3 best prompt rewrites with a short rationale for each choice. Fewer outputs, more confidence per output. This is also faster to prompt-tune well.

- **Raised price to $12.99.** At $7.99, unit economics require too much volume for a niche one-shot tool with unproven distribution. At $12.99, ~250 paying users = ~$3,200/month — a more realistic bar for a solo product with organic Reddit/SEO acquisition. Still an impulse buy for someone spending $30/month on Hinge Gold.

- **Distribution-first build sequence.** The critique's biggest point: distribution is the hole in the floor. v3 commits to building the landing page with real before/after examples first and seeding Reddit (r/hingeapp, r/dating_advice) before the full product is done. That test tells you whether organic acquisition works before you spend 10 days on LLM tuning.

## Improved Description

**HingeFix** — paste your Hinge bio and prompt answers into a structured, Hinge-aware editor, pay $12.99, and get a full profile audit plus one rewritten bio and three rewritten prompt answers in under 60 seconds.

The UX is built around Hinge's actual format: choose your prompt from a dropdown of Hinge's real categories, type into fields with Hinge's real character limits, and get a cliché detector that flags phrases like "fluent in sarcasm" and "love to laugh" before you even submit. This is what makes it visibly different from pasting your profile into ChatGPT — the constraints are part of the product.

After payment, you get:
- A structured audit: first impression, personality signal, specificity, and conversation-starter potential — each with a one-paragraph verdict and one concrete fix
- 1 rewritten bio with a short rationale for the choices made
- 3 rewritten prompt answers, selected from your weakest entries, each with Hinge character limits respected and clichés removed

One-time payment. No account. No subscription. No photos. Copy your rewrites and go update your profile.

## Why This Is Worth Building (Solo + AI)

Text-in, text-out with a Stripe paywall is the simplest viable shape for this — no storage, no moderation, no account system. Hard-coding Hinge's actual UI constraints into the editor is a 1-day UX build that makes "Hinge-specific" tangible to users, not just a pitch claim, and it's a moat that ChatGPT doesn't have by default. At $12.99 with Reddit as the primary organic channel, this needs ~20 paying users/week to generate meaningful signal — a realistic target for a niche utility with genuine before/after results.

## Solo MVP Scope

**What's in v1:**
- Landing page with real before/after profile examples (built first, before full product)
- Hinge prompt selector (dropdown of real Hinge categories) + answer fields with real character limits
- Inline cliché detector flagging common banned phrases before submission
- $12.99 upfront Stripe payment (one-time, no subscription, no free tier)
- LLM-generated structured audit (4 dimensions) + 1 bio rewrite + 3 prompt rewrites
- Results page with copy-to-clipboard per output + short rationale per rewrite
- Email receipt only (no drip, no account, no retention flow)

**What's out of v1:**
- Free audit tier — cut (the core structural fix of v3; not coming back)
- Photos — cut (critique hard cut from v1; not coming back)
- 1-10 score / roast card / share mechanic — cut (from v1; not coming back)
- Multiple bio variants (3 bios) — cut (quality over quantity; 1 best bio only)
- Email drip / refresh reminders — cut (accepted as retention theater)
- Returning-user skip-audit flow — cut (removes account-system complexity)
- Support for Bumble, Tinder, or other apps — post-v1 only
- Account system / saved profiles — not needed

**Estimated build time with AI coding tools:** 12–14 days (honest, includes all work)
- Days 1–2: Landing page with real before/after examples (distribution test first)
- Days 3–4: Profile input UI — Hinge prompt dropdown, character counters, cliché detector
- Days 5–8: LLM prompt engineering — audit quality, bio rewrite, prompt rewrites, Hinge-aware constraints (this is the hard part; do not compress)
- Days 9–10: Stripe integration + payment gating + results display
- Days 11–12: Copy-to-clipboard, email receipt, edge cases, deploy
- Days 13–14: Reddit seeding, A/B copy test on landing page, fix what breaks

## Key Assumptions Check

1. **"Users will pay $12.99 upfront without a free sample."** — UNVERIFIED. The before/after landing page examples are doing the trust-building work that the free audit did in v2. If conversion is low, fallback: add a single free "top problem with your profile" teaser (1 sentence, gated behind email, not the full audit) to build trust before the paywall. Do not restore the full free audit.

2. **"Hard-coded Hinge UX constraints produce outputs users perceive as clearly better than ChatGPT."** — UNVERIFIED but testable in 1 day. Run 10–15 real Hinge profiles through both and compare outputs blindly with 5 target users before launch. Fallback: if the quality gap is small, lean on UX format (structured cards, copy buttons, rationale per rewrite) as the value, not AI output quality alone.

3. **"Reddit organic seeding (r/hingeapp, r/dating_advice) drives enough traffic to validate the model."** — UNVERIFIED. This is the distribution assumption that killed v1 and v2. Fallback: if Reddit traffic is too small or hostile, test a single $50 Meta ad targeting "Hinge" + "dating app" interests against the landing page before investing more. Do not assume distribution is solved.

## Why Would Users Come Back in Week 2?

They won't, and that's fine. This is a one-shot utility. A user updates their Hinge profile once, gets matches, and the product did its job. Revenue comes from new users, not returning ones. At $12.99/conversion with organic Reddit/SEO acquisition, ~250 paying users/month = ~$3,200 MRR. That is the realistic ceiling for v1, not a recurring subscription business. If retention data later shows users returning after 60–90 days (new city, new relationship ended, seasonal refresh), a discounted re-run at $7.99 can be added then.

## Open Questions

- **Will upfront $12.99 convert without a free tier?** The landing page before/afters must be strong enough to close trust cold. This needs real profiles, not made-up examples.
- **How accurate is the Hinge prompt category list?** Hinge updates prompts periodically. The dropdown needs a maintenance plan — low effort but needs to be explicitly owned.
- **Will Reddit r/hingeapp tolerate promotional posts?** Some subreddits ban them. Strategy: post genuine before/after content as useful community posts, not ads. May require building community credibility first.
- **Does the cliché detector add enough perceived value to justify the build time?** It's a 1-day build, but if users don't notice it, cut it from the pitch and keep it as a silent quality filter.
- **Is $12.99 the right price or should it be $14.99?** Worth A/B testing on the landing page before launch. Higher price may signal more quality for a tool in a "pay for help" category.

## Design Handoff

Every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Landing Page** — Hero headline ("Your Hinge profile is costing you matches"), subheading ("Get a full audit and rewritten bio + prompts in 60 seconds — $12.99"), primary CTA ("Fix My Profile"). Below fold: 2–3 real before/after profile examples with visible improvements highlighted. Brief explanation of what you get (audit + 1 bio + 3 prompts). Single price, no tiers. No nav clutter. No free tier mention.

- **Screen 2: Profile Input** — Structured Hinge-aware editor. Top: bio text field (character counter set to Hinge's bio limit). Below: up to 6 prompt-answer pairs. Each pair has: a dropdown selector of real Hinge prompt categories (e.g. "A shower thought I had recently", "I want someone who", etc.), an answer text field with Hinge's character limit shown live, and an inline cliché detector that highlights flagged phrases in yellow as the user types (e.g. "fluent in sarcasm", "love to laugh", "here for a good time"). Bottom: "Continue to Checkout — $12.99" CTA. No email field here.

- **Screen 3: Checkout** — Stripe-powered. Shows what you're getting (full audit + 1 best bio + 3 best prompt rewrites). Email field for receipt. Pay button. No account creation. No subscription upsell.

- **Screen 4: Results — Audit** — Top section: 4-dimension audit displayed as vertical cards. Each card: dimension name (First Impression / Personality Signal / Specificity / Conversation Starter Potential), one-paragraph verdict, one bolded concrete fix. Below audit: "Your Rewrites →" button that scrolls/navigates to rewrite section.

- **Screen 5: Results — Rewrites** — One rewritten bio displayed as a card with a short rationale (2 sentences: what changed and why). Below: 3 rewritten prompt answers, each showing the original prompt label, the new answer, a short rationale, and a copy-to-clipboard button. "Copy All" button at bottom. "Email me these results" optional field (one-tap, no pressure).

- **Screen 6: Email Receipt / Confirmation** — Simple transactional email (not shown as a screen, but included in build): "Your HingeFix results are ready" with a link back to the results page (tokenized URL, no account required). No drip follow-up.

**Key interactions:**
- Landing page → "Fix My Profile" CTA → Profile Input screen
- Profile input: cliché detector highlights phrases inline as user types; dropdown shows Hinge prompt categories; character counters update live
- "Continue to Checkout" → Stripe checkout → on payment success, redirect to Results — Audit
- Scroll or tab navigation between Audit and Rewrites on results page
- Copy-to-clipboard on each rewrite output (bio + each prompt answer)
- Optional "email me these" field on results page saves results to a tokenized URL, sends to email as a simple one-time receipt link

## Version History

- v1: Original idea
- v2.1: Critique — core moat unverified, free audit kills conversion, email drip is not retention, $7.99 unit economics are ugly, no real distribution channel, prompt wrapper too easy to clone
- v3: Improved — removed free audit tier, upfront $12.99 pay-once, hard-coded Hinge UX constraints (prompt dropdown, character limits, cliché detector), accepted one-shot utility framing, distribution-first build sequence, shrunk deliverable to 1 bio + 3 prompts
