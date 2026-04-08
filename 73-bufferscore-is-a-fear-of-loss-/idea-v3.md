# Idea #73: (Improved)
## Version: v3
**Date:** 2026-04-08 18:16
**Status:** improved
## Original Idea
BufferScore is a fear-of-loss app that copies the Credit Karma model but applies it to job security instead of creditworthiness.
## What Changed and Why
- Reframed the product from a weekly habit app into a brutally useful one-time layoff-readiness audit with optional 14-day recheck. This directly addresses the critique that weekly retention was being faked instead of earned.
- Cut the mushy sub-scores and removed Income Backup entirely. v3 scores only objective, defensible inputs: liquid savings, essential monthly burn, resume recency, LinkedIn recency, and whether the user has a ready-to-send resume file plus a named target-company list. This fixes the trust problem caused by mixing hard facts with vibes.
- Replaced the "one recommended task" rules engine with a ranked action queue tied to score impact and time required. This makes the output feel less arbitrary, gives the user choice, and turns the product into an audit plus execution aid instead of fake AI coaching.
## Improved Description
BufferScore v3 is a layoff-readiness audit for mid-career software engineers, not a weekly anxiety ritual. The user spends 5-7 minutes entering hard facts only: liquid savings, essential monthly spend, last time they updated their resume, last time they updated LinkedIn, whether they already have a current PDF resume ready to send, and whether they have a saved shortlist of target companies. The app then produces a harsh but transparent result: a Runway Grade, a Search Readiness Grade, and a single overall status such as Fragile, Recoverable, or Ready.

The key output is not "keep checking your score every week." The output is a ranked fix queue showing the 3-5 highest-leverage actions to improve readiness fast. Each action has a reason, expected impact, and time estimate. Example actions: cut $400 from monthly burn, export a current resume PDF tonight, refresh LinkedIn headline and top five bullets, or save 20 target companies into one list. The queue is generated from explicit rules the user can understand, not fake personalization.

The product positioning is now clear: BufferScore is a preparedness audit with an execution plan. It does not predict layoffs, manage anxiety, or promise a durable habit loop. It helps a skeptical technical user answer one practical question: "If I lost my job this quarter, what are the few concrete gaps that would hurt me most?" That framing makes it more credible, more useful, and less easily dismissed as a spreadsheet clone because the value is in the harsh rubric, clean prioritization, and fast output, not in generic persistence features.

Why would users come back in week 2? Some will not, and v1 should assume that. The realistic return trigger is finishing one or two actions from the ranked queue and rerunning the audit to see if they moved from Fragile to Recoverable. To support that, v1 includes one optional 14-day reminder email with a deep link back to the saved audit. If second-check usage is weak, the product should stay positioned as a one-time audit tool and not expand into a habit app.

3 biggest assumptions that could kill the product:

- VERIFIED: Users care about a blunt runway calculation anchored in liquid savings and essential burn. This is the most credible part of the concept and is already familiar from emergency-fund behavior.
- FALSE: Mid-career engineers want a weekly layoff-anxiety ritual. The critique is right that this is not a credible core behavior, so weekly usage is removed from the product pitch and MVP value proposition.
- UNVERIFIED: A ranked action queue based on a harsh, simple rubric will feel materially more useful than a spreadsheet or ChatGPT prompt. This is the main product risk, so it is not the moat. Fallback plan: if users treat the queue as too easy to copy, package the audit as a lead-gen tool or downloadable worksheet and stop investing in persistence features.
## Why This Is Worth Building (Solo + AI)
This is viable for a solo developer because the MVP is mostly a landing page, a short audit flow, transparent scoring logic, a small authored action library, and one optional email reminder. AI coding tools help with the form logic, scoring implementation, copy iteration, and deployment, while the real human effort stays focused on the rubric and action content that actually determine whether the product feels useful.
## Solo MVP Scope
- What's in v1: landing page; 5-7 minute audit flow; objective scoring for runway and search-readiness; a final results page with harsh status labels; ranked action queue with impact and time estimates; optional email capture to receive the result and one 14-day recheck reminder; saved audit session via magic link or simple tokenized return flow.
- What's out of v1: weekly check-ins, progress history, streaks, notifications beyond one reminder email, social sharing, recruiter or market data, AI resume review, benchmarking, partner-income logic, contract-income logic, community features, user-generated content, monetization, and any feature that depends on users returning regularly.
- Estimated build time with AI coding tools: 2 to 3 weeks total, including 4-5 days for UI and audit logic, 3-4 days for rubric and action-library content prep, 1-2 days for email and return-link setup, 1 day for analytics/instrumentation, and 2-3 days for QA, copy tuning, and issue fixes.
## Open Questions
- Do users actually rerun the audit after 14 days, or is this best kept as a single-session tool plus emailed result?
- Which search-readiness inputs are objective enough to stay in the rubric without drifting back into self-soothing theater?
- Does "save your result and get one reminder" convert better than letting the audit remain fully anonymous?
- What proof threshold will justify building persistence later: second-session rate, email capture rate, or action completion self-report?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing Page — explain the product as a blunt layoff-readiness audit for software engineers; show the promise, sample output, trust notes, and primary CTA to start the audit.
- Screen 2: Audit Step 1 - Cash Runway — collect liquid savings and essential monthly spend; explain what counts as liquid savings and essential burn; show simple inline validation.
- Screen 3: Audit Step 2 - Search Assets — collect objective readiness inputs: resume last updated date, LinkedIn last updated date, whether a sendable PDF resume exists, and whether a target-company list exists.
- Screen 4: Audit Step 3 - Contact + Save Result — optional email capture to send the result and a 14-day reminder; explain that returning is optional and the product is primarily a one-time audit.
- Screen 5: Results Summary — show Runway Grade, Search Readiness Grade, overall status, plain-language explanation, and the top three reasons the user is vulnerable.
- Screen 6: Ranked Action Queue — show the prioritized actions with impact, estimated time, and rationale; allow the user to expand each action to see exact instructions.
- Screen 7: Action Detail — present one action in full detail with why it matters, what done looks like, and a link back to the queue or rerun flow.
- Screen 8: Return / Recheck Audit — prefill prior answers when available, let the user update changed inputs quickly, and regenerate results after the reminder flow.
- Screen 9: Honesty / Methodology — explain the rubric, what is intentionally excluded, and why this is a preparedness audit rather than a prediction product.
Key interactions:
- User lands on the landing page, starts the audit, completes the three-step input flow, and reaches the results summary plus ranked action queue.
- From the results summary, the user opens any action detail, completes work offline, and optionally requests the emailed result plus recheck reminder.
- A returning user opens the reminder link, updates only the changed fields in the recheck flow, and gets a refreshed result without needing a full account system.
## Version History
- v1: Original idea
- v2.1: Critique - weekly retention was unconvincing, the score mixed hard facts with vibes, and the product was better as an audit than a habit app
- v3: Improved - repositioned as a one-time layoff-readiness audit with objective inputs, a ranked fix queue, and only an optional 14-day recheck
