# Idea #53: (Improved)
## Version: v2
**Date:** 2026-04-02 21:09
**Status:** improved
## Original Idea
Glow Clash is an attractiveness app that copies the Cal AI model but applies it to skincare anxiety instead of calories. You scan the skincare products you plan to use tonight, and the app flags ingredient collisions that commonly lead to redness, peeling, breakouts, or next-morning irritation before a date, event, or photo day. The reveal is instant and screen-recordable: a blunt warning that says your routine is likely to make your face look worse by morning, plus the single product to remove so the loop becomes scan, get the verdict, swap one thing, and check your face tomorrow.
## What Changed and Why
- Scanning, product recognition, and ingredient database dependency are removed from v1. Users build a routine manually from a fixed list of active categories such as retinoid, AHA/BHA exfoliant, benzoyl peroxide, vitamin C, prescription acne treatment, scrub, and barrier-repair products. This fixes the biggest solo-dev trap in the critique: unverified scanner accuracy and product-data maintenance.
- The promise is narrowed from "your face will look worse by morning" to "higher irritation risk before an event." The app no longer predicts breakouts or pretends it knows exactly which product caused the issue. Instead it shows the conflict rule, the reason, and a caution level. This makes the advice safer and more credible.
- The product shifts from one-off warning tool to "pre-event routine planner + next-day sensitivity log." Users save their usual routine templates, run an event check the night before, and log what happened the next morning. This answers the critique's retention problem: week-2 value comes from building a personal sensitivity record, not from repeating generic infographic advice.
## Improved Description
Build Glow Clash as a cautious web app for people who already use strong actives and want a safer routine the night before a date, event, or photo day. The MVP is not a scanner, not a skincare oracle, and not a product library. It is a manual pre-event routine checker for a narrow wedge: users combining retinoids, acids, acne treatments, and other irritation-prone actives.

The user picks tomorrow's event type and how important it is to avoid visible irritation, then assembles tonight's routine from a short structured list of active categories plus optional notes like "new product," "used this before," or "skin already feels dry." The app returns:
- a simple caution level: low, medium, or high irritation risk
- the exact rule(s) triggered, for example "retinoid + leave-on exfoliant increases irritation risk"
- a safer fallback suggestion, framed as "remove one harsh step" or "switch to barrier-only night"
- a confidence note explaining this is heuristic guidance, not prediction

The retention loop is the next-day check-in. The morning after an event, the user logs whether they saw redness, stinging, peeling, or no issue. Over time the app highlights patterns like "you usually tolerate retinoid alone but not retinoid + acid before events." That makes the product more useful in week 2 because it becomes a personal sensitivity tracker instead of a one-time rules chart.

This is viable because it cuts every fragile dependency the critique flagged. No scanning. No product OCR. No ingredient normalization pipeline. No dramatic beauty promise. The differentiator is not hidden AI. It is a narrow, explicit, event-driven workflow that turns generic conflict rules into a reusable personal decision tool.

3 biggest assumptions that could kill the product:
- VERIFIED: A solo developer can ship the core experience in 2-4 weeks if v1 uses manual active-category input instead of product scanning and avoids any large ingredient database.
- UNVERIFIED: Users who already know the obvious "do not stack harsh actives" rules will still find enough value in the event-specific planner and next-day logging to return in week 2. Fallback plan: if retention is weak, reposition the product as a one-session pre-event checker and drop any claim that retention is the moat.
- FALSE: Product scanning or external skincare databases are needed for MVP differentiation. The critique is right that this is a complexity trap, so it is explicitly out of v1.

Why would users come back in week 2? Because the app starts showing their own patterns, not generic skincare rules. A user who logs three or four pre-event routines can see which combinations they personally tolerated before important days, which is more useful than rereading a static "don't mix these actives" infographic.
## Why This Is Worth Building (Solo + AI)
This is small enough for one person to ship with AI coding tools because it is a rules engine plus routine logging UI, not a computer-vision or product-data business. It also has a sharper validation question now: do users trust and reuse a cautious pre-event irritation planner enough to build a personal sensitivity history?
## Solo MVP Scope
- What's in v1: responsive web app, onboarding that explains the caution-first positioning, manual routine builder from a fixed taxonomy of active categories, pre-event irritation check with explicit rule explanations and caution levels, saveable routine templates, next-day outcome check-in, simple personal pattern history, and local auth/storage
- What's out of v1: scanning, barcode lookup, OCR, product-name search, ingredient parsing, breakout prediction, "face will look worse by morning" copy, personalized medical claims, social sharing loops, UGC, community advice, moderation, payments, subscriptions, native mobile apps, push notifications, and brand/product partnerships
- Estimated build time with AI coding tools: 2-3 weeks total, including 2-3 days to define and QA the active-category taxonomy and conflict rules, 6-8 days for UI and app logic, 2-3 days for auth/storage/integration setup, 2-3 days for next-day logging/history views, and 1-2 days for copy, safety disclaimers, and QA
## Open Questions
- UNVERIFIED: Is the fixed active taxonomy clear enough for users to map their real products without frustration, or does it need a guided "what type of product is this?" helper?
- Which conflict rules are safe and useful enough for v1 without drifting into medical overclaiming?
- How much optional context should the user provide in v1: just active categories, or also tolerance/newness/dryness flags?
- Will users complete next-day check-ins often enough for the pattern history to matter, or does the app need a lighter reminder mechanic later?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / Value Proposition — explains the app as a cautious pre-event irritation planner, shows the narrow use case, key disclaimer, and CTA to start a routine check
- Screen 2: Onboarding / How It Works — explains that users will choose active categories manually, see explicit conflict rules, and log next-day outcomes; includes trust-setting copy that this is not medical advice
- Screen 3: Home / Routine Templates — main dashboard showing saved routine templates, recent event checks, and CTA to start a new pre-event check
- Screen 4: New Event Check / Event Setup — asks what the event is, when it is, and how important it is to avoid visible irritation; includes optional context toggles like "skin feels dry today" or "trying something new"
- Screen 5: Routine Builder — lets the user assemble tonight's routine from fixed active-category chips/cards, set product order, and mark whether each step is a known product or a new one
- Screen 6: Risk Result — shows caution level, triggered rule explanations, confidence note, and the suggested safer fallback such as "skip exfoliant tonight" or "barrier-only night"
- Screen 7: Rule Detail / Why This Was Flagged — deeper explanation of each triggered rule, why it increases irritation risk, and what a safer substitution looks like
- Screen 8: Save Template Modal / View — lets the user save the checked routine as a reusable template with a custom name and event context
- Screen 9: Next-Day Check-In — morning-after flow that records redness, stinging, peeling, or no issue, with optional note field and link back to the routine used
- Screen 10: Pattern History — timeline or simple insight view showing prior event checks, outcomes, and repeated patterns such as combinations that often led to irritation
- Screen 11: Settings / Safety — account or local-data controls, reminder preference placeholder if needed later, taxonomy/help text, disclaimers, and reset/export options
Key interactions:
- User enters through Landing, completes Onboarding once, then lands on Home
- From Home, user starts a New Event Check, fills Event Setup, builds a routine, and lands on Risk Result
- From Risk Result, user can inspect Rule Detail, revise the routine, or save the routine as a template
- The next morning, the user returns through Home or a reminder link into Next-Day Check-In
- Completed check-ins feed Pattern History, which becomes the main reason to return before future events
## Version History
- v1: Original idea
- v1.1: Critique - fake certainty, scanner/data dependency, weak retention, dangerous overclaiming, and too much solo-dev scope
- v2: Improved - cut scanning, narrowed to pre-event irritation risk, made the logic explicit, and added next-day logging to create a personal sensitivity loop
