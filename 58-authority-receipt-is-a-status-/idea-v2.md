# Idea #58: (Improved)
## Version: v2
**Date:** 2026-04-03 10:34
**Status:** improved
## Original Idea
Authority Receipt is a status app that copies the Crystal Knows model but focuses on promotion and negotiation anxiety instead of generic personality analysis.
## What Changed and Why
- Narrowed the product from "every important work message" to one recurring job: rewriting weekly manager updates. This fixes the category blur, avoids the Slack trap, and gives the product a realistic reason for users to return in week 2.
- Replaced the fake universal "authority score" with a transparent rubric: `impact clarity`, `ownership language`, `ask clarity`, and `risk framing`, plus evidence pulled directly from the draft. This makes the judgment feel teachable instead of arbitrary and reduces the false-confidence risk the critique called out.
- Added a private `evidence bank` that extracts concrete wins and asks from each update and saves them into history. This gives the app workflow value beyond a one-off rewrite and makes it more defensible than a harsh prompt wrapper, while still staying small enough for a solo builder.
## Improved Description
Instead of pretending to measure a universal status signal, v2 is a focused career-writing coach for one recurring scenario: weekly updates to your manager. The user pastes a draft weekly update or rough bullet notes, selects the goal of the message (`inform`, `show progress`, or `ask for support`), and gets a critique based on a visible rubric: `impact clarity`, `ownership language`, `ask clarity`, and `risk framing`. The app highlights the exact lines that weaken the update, explains why they weaken it in this specific scenario, and rewrites the note into a sharper version that still fits the chosen manager context.

Each session also extracts reusable evidence from the draft, such as shipped work, quantified impact, ownership signals, blockers, and asks. Those snippets are saved into a private evidence bank so the user is not just fixing one message; they are building a running record they can reuse later when writing a self-review. That is the retention loop: users come back in week 2 because they have another manager update to send, and each visit improves both today's message and their future review material.

This is better than the original because it removes the fake objectivity, chooses a repeatable wedge, and adds cumulative value. It is also more viable for a solo builder because the product can be a simple web app with one workflow, one rubric, local or lightweight account-based history, and a small curated test set for prompt evaluation.

Biggest assumptions that could kill the product:
- FALSE: A single universal "authority score" can be trusted across managers, cultures, and situations. v2 removes this from the core product.
- UNVERIFIED: ambitious ICs and managers actually feel enough weekly pain around status updates to return consistently. Fallback plan: if weekly retention is weak, reposition the same engine as a monthly self-review drafting tool instead of pretending it is a habit product.
- UNVERIFIED: the evidence bank is valuable enough to beat "saved prompt in ChatGPT" for this niche workflow. Fallback plan: if users like rewrites but ignore history, reduce v1.1 to a lighter coach plus one-click monthly export instead of pushing a weak archive as the differentiator.
## Why This Is Worth Building (Solo + AI)
One person can ship this in 2-4 weeks because the core is a narrow text workflow: paste draft, run rubric-based analysis, generate rewrite, and save evidence history. AI coding tools help with the fast UI iteration and prompt-evaluation harness, but the product still has real structure because the value comes from the specific recurring workflow and curated rubric, not just a clever prompt.
## Solo MVP Scope
- What's in v1: a web app for weekly manager updates only; draft input; goal selector; transparent rubric breakdown; rewrite; evidence extraction; saved history/evidence bank; privacy copy; a small curated rubric/test set used during build to tune output consistency.
- What's out of v1: salary negotiation, performance self-reviews as a separate mode, Slack or browser integrations, screen-recordable roast cards, team features, company tone calibration, admin/security features for enterprises, shared workspaces, payments, and any universal authority score.
- Estimated build time with AI coding tools: 12-16 working days total, including 3-4 days of rubric/content prep, 1-2 days of prompt/evaluation setup, 6-8 days of UI and history build, and 2 days of polish/testing.
## Open Questions
- How often do target users already send structured weekly updates versus ad hoc Slack messages? This must be validated before treating weekly retention as likely.
- Is browser-local history enough for MVP, or do users need account-based saved history to trust the evidence bank? Local-first is faster; account sync adds setup work.
- How many curated examples are needed before the rubric feels consistent? Content prep is not solved; expect manual work to define sample weak/strong updates and failure cases.
- Which buyer is strongest after MVP validation: individual ambitious ICs, job-seeker/career-coach audiences, or manager-training creators? Do not assume B2B.
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / Positioning — explains that the app is for weekly manager updates, not generic writing; key elements: problem statement, example before/after, rubric preview, privacy promise, CTA to start.
- Screen 2: New Update Analysis — main workspace where the user pastes a weekly update or bullet notes; key elements: text area, goal selector (`inform`, `show progress`, `ask for support`), optional manager-context selector, submit action, sample input link.
- Screen 3: Critique Results — shows rubric breakdown for the submitted draft; key elements: four rubric cards with plain-English explanations, highlighted weak lines from the original draft, warning about context dependence, and the extracted evidence snippets.
- Screen 4: Rewrite View — presents the improved manager update beside the original; key elements: side-by-side comparison, copy button, toggle for concise vs warmer rewrite, save-to-history action.
- Screen 5: Evidence Bank / History — timeline of past sessions; key elements: saved update title/date, extracted wins, asks, blockers, search/filter, empty state that explains the long-term self-review value.
- Screen 6: Session Detail — opens a saved analysis; key elements: original draft, rubric results, rewrite, extracted evidence, notes on why the rewrite changed, and delete/export actions.
Key interactions:
- User lands on the app, understands the narrow use case, and starts a new analysis.
- User pastes a weekly update, chooses a message goal, and submits for critique.
- User reviews the rubric feedback, checks the rewrite, copies the improved version, and saves the session.
- User returns later to the Evidence Bank to reuse saved wins and asks when preparing future updates or eventual self-review material.
## Version History
- v1: Original idea
- v1.1: Critique - broad, arbitrary "authority" scoring with weak differentiation and poor retention logic
- v2: Improved - narrowed to weekly manager updates with a transparent rubric and reusable evidence bank
