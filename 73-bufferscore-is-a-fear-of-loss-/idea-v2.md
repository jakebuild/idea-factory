# Idea #73: (Improved)
## Version: v2
**Date:** 2026-04-08 18:11
**Status:** improved
## Original Idea
BufferScore is a fear-of-loss app that copies the Credit Karma model but applies it to job security instead of creditworthiness.
## What Changed and Why
- Replaced the fake all-knowing layoff score with three transparent sub-scores: Cash Runway, Job Search Assets, and Income Backup. This fixes the credibility problem because each score comes from user-verifiable inputs instead of invented rehire odds or vague market predictions.
- Narrowed the audience to mid-career software engineers in high-cost US cities and turned "interview readiness" into a concrete checklist. This removes bloated onboarding and makes the product useful for one segment with similar job-search behaviors instead of pretending to model every worker.
- Added a weekly action loop: one checkpoint, one recommended task, and visible progress over time. This fixes retention and the "feel bad then leave" problem by giving users a next step they can complete in 20-40 minutes.
## Improved Description
BufferScore v2 is no longer a "credit score for layoffs." It is a weekly layoff-readiness tracker for mid-career software engineers living in expensive US cities who want to know one honest thing: if income stopped this month, how ready are they to absorb the hit and restart earning?

The product gives users three separate sub-scores instead of one fake authority number:

- Cash Runway: months of survival based on liquid savings and monthly essentials.
- Job Search Assets: a checklist score based on whether resume, LinkedIn, portfolio/GitHub, references, and target-company list are actually ready.
- Income Backup: whether the user has realistic fallback options already set up, such as active recruiter conversations, contract leads, consulting availability, or a partner income buffer.

Each sub-score is explained in plain language with the exact inputs used. No market-condition API, no rehire probability, no "AI predicts your layoff risk." The app is explicit that it is a preparedness tool, not a prediction engine.

The core loop is a weekly 5-minute check-in. Users update cash, expenses, and checklist items, then the app gives exactly one recommended action for the week, chosen from a small authored library: cancel one recurring expense, finish the resume bullet pass, message two warm contacts, assemble references, or publish a portfolio sample. The win state is not "feel informed." The win state is "be 30-day job-search ready." Users come back in week 2 because the app tracks whether last week's action was completed, unlocks the next highest-leverage task, and shows progress from "exposed" to "ready."

What makes it better than a spreadsheet or generic budgeting app is that it combines money runway with career-readiness tasks in one narrow workflow. What makes it better than a ChatGPT prompt is persistence: saved baseline, weekly history, and a concrete action ladder instead of one disposable answer.

Biggest assumptions that could kill the product:

- VERIFIED: Users understand and care about a simple runway number. This is already validated by the appeal of emergency-fund calculators and the original hook.
- UNVERIFIED: Mid-career software engineers will return weekly for a guided readiness loop, not just run the calculator once. This is the main product risk, so it is not the moat. Fallback plan: position v1 as a one-time "Layoff Readiness Audit" lead capture tool and test weekly retention before building deeper habit mechanics.
- VERIFIED: A manually authored action library for one narrow segment is feasible for a solo builder in v1. The content burden is real but bounded because it only needs a few dozen task templates, not live labor-market data.
## Why This Is Worth Building (Solo + AI)
This is viable for a solo developer because the product is mostly forms, scoring rules, simple history, and a small authored recommendation system, all of which fit a 2-4 week AI-assisted build. It has a better chance of working because it drops fake prediction, narrows the audience, and gives users a weekly action loop that can create repeat usage instead of a one-time anxiety spike.
## Solo MVP Scope
- What's in v1: onboarding for one target segment; the three sub-scores; transparent scoring explanations; weekly check-in flow; one recommended action per week; progress history for prior check-ins; a small authored action library and scoring rubric for software engineers in high-cost US cities.
- What's out of v1: layoff prediction, rehire odds, live market data, role/industry benchmarking, AI resume review, personalized financial planning, social sharing, referrals, monetization, notifications, partnerships, and any feature that depends on user-generated content or marketplace supply.
- Estimated build time with AI coding tools: 2.5 to 3.5 weeks total, including 6-8 days for UI/app logic, 3-4 days for authored content prep and scoring rubric design, 1-2 days for auth/data/storage setup, and 2-3 days for QA, polish, and issue fixes.
## Open Questions
- Will the target segment complete weekly check-ins at all, or is this fundamentally a one-time audit product?
- Which exact thresholds for "good enough" runway and readiness feel motivating rather than alarmist for this audience?
- What is the lightest onboarding that still gathers enough data for useful weekly actions?
- Is the best go-to-market a landing page with an instant audit, or a private beta aimed at laid-off / layoff-anxious engineers?
- If retention is weak, should v2 become a downloadable audit plus email sequence instead of a persistent app?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / Value Proposition — explain the product for mid-career software engineers, show the three sub-scores, and make the promise clear: "Know your runway. Fix one weak spot each week."
- Screen 2: Onboarding Step 1 - Financial Baseline — collect liquid savings, monthly essential spend, dependents/household context, and current employment status; show helper text for what counts as "essential spend."
- Screen 3: Onboarding Step 2 - Job Search Assets — checklist inputs for resume freshness, LinkedIn status, portfolio/GitHub readiness, references, and target-company list.
- Screen 4: Onboarding Step 3 - Income Backup — collect fallback options such as contract leads, recruiter conversations, partner income support, and side-income readiness.
- Screen 5: Results Dashboard — show the three sub-scores, the reasoning behind each, the biggest weak point, and the single recommended action for this week.
- Screen 6: Sub-Score Detail View — drill into one sub-score with contributing inputs, threshold labels, and exactly how to improve it.
- Screen 7: Weekly Check-In — lightweight update form for changed cash, burn, and completed checklist items; ends with a new weekly recommendation.
- Screen 8: Action Detail / Task Card — explain the recommended task, why it matters, expected completion time, and a simple done / skipped state.
- Screen 9: Progress History — show prior weekly check-ins, sub-score trends, and completed actions so users can see forward motion.
- Screen 10: Settings / Assumptions — let users edit baseline assumptions, reset their profile, and review the product's honesty note that this is a preparedness tool, not a prediction engine.
Key interactions:
- User lands on the value proposition, starts onboarding, completes the three input steps, and reaches the dashboard with immediate sub-scores and one recommended action.
- From the dashboard, the user can open any sub-score detail to understand why it is weak and what inputs affect it.
- Each week, the user opens the check-in flow, updates changed numbers and checklist items, receives one new task card, and then sees the updated progress history.
- The action detail screen feeds back into the dashboard and history after the task is marked done or skipped.
## Version History
- v1: Original idea
- v1.1: Critique - strong hook, but mostly a fake-authority emergency fund calculator with weak retention and unverified market-data assumptions
- v2: Improved - narrowed to a layoff-readiness tracker with transparent sub-scores and a weekly action loop for one target segment
