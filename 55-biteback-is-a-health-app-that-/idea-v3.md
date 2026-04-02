# Idea #55: (Improved)
## Version: v3
**Date:** 2026-04-03 02:44
**Status:** improved
## Original Idea
BiteBack is a health app that copies the Cal AI model but applies it to dental anxiety instead of calories.
## What Changed and Why
- Reframed the product from an open-ended nightly logger into a 14-night "late-night damage control" reset for one specific habit. This fixes the biggest structural issue in the critique: users now have a concrete job-to-be-done and a real reason to return in week 2 beyond re-learning that soda and candy are bad.
- Removed the broad preset library, risk labels, streaks, weekly pattern charts, and bedtime reminders from v1. Instead, v1 covers only 5-7 culprit categories with one rescue card and one fallback plan per category, which keeps content prep realistic and avoids dashboard features the critique already called decorative.
- Changed the product from a pseudo-clinical app into a mobile-first utility/PWA with brutally concrete, low-drama guidance. That makes the trust problem smaller, reduces build scope, and gives the idea a more believable acquisition path through searchable "what do I do now?" moments instead of pretending it is a habit empire.
## Improved Description
BiteBack becomes a mobile-first 14-night reset for people who have one repeat late-night habit they actually want to break: soda after brushing, candy in bed, dessert right before sleep, sports drink at night, or post-dinner grazing. On first use, the user picks their main culprit category, chooses a simple cutoff plan, and selects two fallback moves they are willing to do instead. The product does not score "tooth damage," pretend to know exact brush timing, or ask the user to log every snack forever.

Each night, the app gives the user only three choices: `I'm about to have it`, `I already had it`, or `I skipped it tonight`. If they are about to slip, BiteBack shows a single rescue card for that category: the least-bad move right now, the one thing to avoid, and the next clean reset step for tonight. If they already slipped, it gives a low-drama recovery card instead of a guilt label. If they skipped it, the night is logged in one tap and the user moves on. After a few nights, the app does not show charts; it suggests exactly one plan adjustment based on failures, such as moving the cutoff earlier, changing the fallback choice, or delaying brushing until the user is truly done eating.

This is better because the product is no longer pretending to be a forever tracker or a medical rules engine. It is a short behavior-change utility for one recurring problem. It is also more believable for a solo builder: 5-7 tightly written culprit playbooks, local-only state, one simple rules layer, and no dependence on notifications, social loops, partnerships, or a giant content library.

Why would users come back in week 2? Because they are still inside a finite 14-night reset, the app already holds their chosen culprit and fallback plan, and week 2 is when the first plan adjustment appears if the original plan is failing. If users do not care enough to complete a 14-night reset, retention is weak and the product should be treated as a one-off utility, not a habit app.
## Why This Is Worth Building (Solo + AI)
This is small enough for one developer to ship in 2-4 weeks because the MVP is mostly structured copy, a few rule-based flows, and local persistence in a mobile-first web app. AI coding tools help move fast on the UI and state management, while the narrower scope makes the real work, content prep and behavior design, manageable instead of hidden.
## Solo MVP Scope
- What's in v1: mobile-first web app/PWA; onboarding to choose one main late-night habit from 5-7 categories; one cutoff plan; two fallback choices; nightly check-in with three actions (`about to`, `already did`, `skipped`); category-specific rescue/recovery cards; 14-night progress tracker; one rule-based plan adjustment after repeated slips; local-only history for the current reset; plain-language trust/disclaimer screen.
- What's out of v1: camera scanning; food recognition; exact safe-brush times; broad food search; expanding preset coverage beyond the initial 5-7 categories; streaks; weekly summaries; pattern dashboards; notifications/reminders; user-generated entries; moderation; social sharing; payments; dentist partnerships; Apple Health integrations; personalized dental risk modeling.
- Estimated build time with AI coding tools: 3-4 weeks total, including 6-8 days for content prep and rule writing across the 5-7 culprit playbooks, 6-8 days for UI and local state flows, 1-2 days for PWA/installability and analytics setup, and 3-4 days for copy polish, QA, and device testing.
## Open Questions
- Assumption 1: people with one recurring late-night snack/drink habit will commit to a focused 14-night reset instead of using the tool once. Status: UNVERIFIED. This cannot be treated as the moat. Fallback plan: position v1 as a one-off "late-night damage control" utility and measure completion rate before building anything deeper.
- Assumption 2: 5-7 culprit categories are enough because the product asks users to target one recurring habit, not describe every possible snack. Status: UNVERIFIED. Fallback plan: test the category list with real users before build; if coverage is poor, narrow the audience further instead of growing into a giant food library.
- Assumption 3: camera scanning, exact timings, and pseudo-precision are required for the concept to feel useful. Status: FALSE. The critique already showed those features made the product less credible and less shippable, not more valuable.
- Is the sharpest target user "people trying to stop eating after brushing" or "people trying to cut one repeat late-night sugar habit"? v1 should choose one.
- Which rescue/recovery cards feel concrete and trustworthy without drifting into medical advice?
- Is searchable web distribution around late-night "what do I do now?" queries strong enough to acquire the first users, or does this need a different wedge?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / Value Prop — explains the 14-night reset in one sentence; key elements: headline, who it is for, three-step explanation, start CTA, trust disclaimer link.
- Screen 2: Choose Your Habit — lets the user pick one main culprit category; key elements: 5-7 category cards, short examples for each, "this is the one I want to cut" CTA.
- Screen 3: Build My Plan — sets the initial reset plan; key elements: chosen habit summary, cutoff selector, two fallback options, short note explaining the plan is editable after slips.
- Screen 4: Nightly Check-In — core daily screen; key elements: three large actions (`I'm about to have it`, `I already had it`, `I skipped it tonight`), current day of 14, current plan summary.
- Screen 5: Rescue / Recovery Card — shows the concrete next step for tonight; key elements: one recommended action, one avoid-this warning, one reset instruction for later tonight, save CTA.
- Screen 6: Night Logged — confirms the entry was saved; key elements: tonight's outcome, current progress through the 14 nights, next check-in CTA, optional view history link.
- Screen 7: Plan Adjustment — appears only after repeated slips; key elements: what keeps happening, one recommended plan change, accept/change-later actions.
- Screen 8: Reset Progress / History — shows the current 14-night run; key elements: calendar/list of outcomes, current plan, accepted adjustments, restart reset CTA.
- Screen 9: Method / Trust — explains the rule-based nature of the advice; key elements: what the app can and cannot tell you, non-medical framing, contact/support info.
Key interactions:
- User moves from Landing / Value Prop to Choose Your Habit, then to Build My Plan, and lands on Nightly Check-In.
- On a nightly session, the user taps one of the three check-in actions, sees the Rescue / Recovery Card if needed, then saves the night on Night Logged.
- After repeated slips, Night Logged can route the user into Plan Adjustment before returning them to Nightly Check-In the next day.
- Reset Progress / History is reachable from Night Logged and Nightly Check-In to review the current 14-night run without turning it into a dashboard product.
- Method / Trust is reachable from Landing, Build My Plan, and Rescue / Recovery Card whenever the user needs credibility context.
## Version History
- v1: Original idea
- v2.1: Critique - retention was weak, preset coverage felt flimsy, and the product still looked like a guilty pseudo-health tracker
- v3: Improved - narrowed to a 14-night late-night habit reset with fixed playbooks, no fake scoring, and a clearer return reason
