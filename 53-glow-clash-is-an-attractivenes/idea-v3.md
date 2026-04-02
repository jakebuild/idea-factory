# Idea #53: (Improved)
## Version: v3
**Date:** 2026-04-02 21:13
**Status:** improved
## Original Idea
Glow Clash is an attractiveness app that copies the Cal AI model but applies it to skincare anxiety instead of calories. You scan the skincare products you plan to use tonight, and the app flags ingredient collisions that commonly lead to redness, peeling, breakouts, or next-morning irritation before a date, event, or photo day. The reveal is instant and screen-recordable: a blunt warning that says your routine is likely to make your face look worse by morning, plus the single product to remove so the loop becomes scan, get the verdict, swap one thing, and check your face tomorrow.
## What Changed and Why
- The product is no longer pretending to be a sticky skincare coach. v3 is a narrow pre-event irritation audit: answer a few step-level questions about tonight's routine, get a conservative risk verdict, and see the safest simplified version. This fixes the critique's core problem that v2 was still mostly a rules table plus wishful retention.
- The input model is rebuilt around brutally obvious step cards instead of fuzzy product taxonomy. Users answer yes/no to routine steps such as "retinoid," "leave-on acid," "benzoyl peroxide," "physical scrub," "new product tonight," and "skin already irritated/dry," with examples on each card. If one product contains two harsh actives, they select both. This is better because it removes most categorization ambiguity without needing scanning or ingredient parsing.
- v1 drops fake depth and nonessential systems: no event types, no templates, no auth, no settings sprawl, no "personal sensitivity history" moat. Optional next-morning logging stays only as a validation experiment attached to the last check. This makes the build honest, faster, and more credible in a trust-sensitive category.
## Improved Description
Glow Clash should be built as a blunt "Should I do this routine tonight?" checker for people who already use strong actives and have an important morning tomorrow. It is not trying to diagnose skin, predict beauty outcomes, or maintain a full skincare diary. Its job is narrower: catch the most common irritation-prone combinations before the user makes their face harder to manage the next day.

The MVP flow is simple. The user opens the app, chooses how important it is to avoid irritation tomorrow, and taps through a short set of routine step cards with plain-language examples:
- retinoid / tretinoin / adapalene
- leave-on acid exfoliant (AHA/BHA/PHA)
- benzoyl peroxide
- physical scrub or strong cleansing treatment
- vitamin C or other low-risk active
- thick moisturizer / barrier-repair step
- new product tonight
- skin already dry, tight, stinging, or irritated

The app then returns three things only:
- a conservative risk level: low, medium, or high
- the exact rule that triggered, written in plain English
- a stripped-down fallback routine for tonight, such as "skip the acid and keep moisturizer only"

The logic is based on a deliberately small, manually curated rule set. That rule set is the product work: around 8-12 high-confidence interactions, severity labels, explanation copy, and fallback suggestions that stay on the safe side of "reduce irritation risk" instead of pretending to offer medical advice. The app should say when the signal is weak, and it should avoid dramatic claims about breakouts or attractiveness.

Optional next-morning logging remains in v1, but only as a lightweight experiment: one screen that asks whether the user had redness, stinging, peeling, or no issue after the checked routine. It is not sold as pattern intelligence. If people use it, great; if they do not, the product still works as a one-session utility.

3 biggest assumptions that could kill the product:
- VERIFIED: A solo developer can ship this in 2-4 weeks if v1 stays limited to a web app with step-card input, a small rules engine, no auth, no templates, and no product database.
- UNVERIFIED: Users will find the step-card taxonomy clear enough to map their real routine without second-guessing themselves. If this fails, fallback plan: reduce the cards further and reposition the tool around only the clearest high-risk steps, even if that makes the checker less comprehensive.
- UNVERIFIED: Enough users will return for another pre-event check or optional next-morning log to justify anything beyond a single-session utility. This is not the moat. Fallback plan: position Glow Clash as a disposable but useful pre-event checker and stop investing in retention features.

Why would users come back in week 2? Not because the app magically becomes a diary. They come back only when they face the same job again: another important morning, a new product, a stronger active, or a routine change they do not fully trust. That is episodic retention, not habit retention, so confidence here is moderate rather than high.
## Why This Is Worth Building (Solo + AI)
This is worth building because it turns a vague skincare-anxiety concept into a tight utility with one credible job and very little engineering overhead. A solo developer can ship it quickly with AI coding tools because the hard work is constrained content design and UX clarity, not ML, integrations, moderation, or marketplace complexity.
## Solo MVP Scope
- What's in v1: landing and trust-setting onboarding, one pre-event check flow, step-card routine input with examples, conservative rules engine with 8-12 manually written rules, results page with plain-language explanations and fallback routine, optional next-morning outcome log tied to the latest check, and lightweight local persistence for the latest checks only
- What's out of v1: scanning, barcode lookup, OCR, ingredient parsing, product search, event-type personalization, templates, auth/accounts, settings page, pattern history claims, push notifications, social features, UGC, moderation, subscriptions, payments, native mobile apps, and any medical-style recommendation engine
- Estimated build time with AI coding tools: 2-4 weeks total, including 4-6 days for rule writing, taxonomy examples, severity definitions, and safety copy; 5-7 days for the web UI and rules logic; 2-3 days for local persistence and integration setup; 1-2 days for the optional next-morning log flow; and 2-3 days for QA on wording, edge cases, and usability
## Open Questions
- Which 8-12 conflict rules are high-confidence enough for v1, and what source or expert review standard is strong enough to keep the copy credible?
- Are the step cards obvious in user testing, especially when one product includes multiple active types?
- Does the optional next-morning log get used at all without reminders, or should it be removed after the first test batch?
- Is "importance of avoiding irritation tomorrow" actually useful for copy tone, or does even that add fake nuance without changing decisions?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / Value Proposition — explains the app as a blunt pre-event irritation checker, shows who it is for, what it does not do, the safety disclaimer, and the CTA to run a check
- Screen 2: Onboarding / How To Use It — teaches the step-card input model with 1-2 concrete examples, explains that users should select every harsh step present even if one product covers multiple categories, and sets expectations around conservative guidance
- Screen 3: Routine Check / Step Cards — main interaction screen with importance selector, step cards for each routine type, short examples under every card, and context toggles for "new product tonight" and "skin already irritated"
- Screen 4: Results / Risk Verdict — shows risk level, triggered rules, brief reasoning, and the safest simplified routine for tonight; includes actions to edit answers or log outcome tomorrow
- Screen 5: Rule Detail / Why It Was Flagged — expands the triggered rule copy with clearer explanation, what combination caused it, and why the fallback is safer
- Screen 6: Next-Morning Log — optional 10-second follow-up showing the routine summary and asking for redness, stinging, peeling, or no issue, plus an optional note
- Screen 7: Recent Checks — lightweight list of the last few checks and logged outcomes stored on-device, mainly to reopen the most recent check and support the optional follow-up
Key interactions:
- User lands on the value proposition, reads the guardrails, and starts the check
- Onboarding teaches the card model once, then routes into the routine check
- The user selects tonight's steps and context flags, then submits to Results
- From Results, the user can inspect the detailed rule explanation or go back and change inputs
- The next day, the user can open the latest check from Recent Checks and submit the optional outcome log
## Version History
- v1: Original idea
- v2.1: Critique - still too close to an infographic, retention was unproven, taxonomy was messy, and v1 had too many trust-sensitive extras
- v3: Improved - repositioned as a one-job pre-event irritation audit with obvious step-card inputs, a smaller rule set, and no fake retention moat
