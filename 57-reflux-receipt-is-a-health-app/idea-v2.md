# Idea #57: (Improved)
## Version: v2
**Date:** 2026-04-03 07:53
**Status:** improved
## Original Idea
Reflux Receipt is a health app that copies the Cal AI model but applies it to late-night heartburn anxiety instead of calories.
## What Changed and Why
- Replaced camera scanning and fake time-window prediction with manual entry, blunt risk bands, and a bedtime slider. This removes the weakest part of the original idea: pretending a solo-built scanner can infer ingredients, portions, and personal reflux causality with medical-looking precision.
- Shifted the product from "universal reflux predictor" to "personal trigger tracker for known nighttime reflux sufferers." That makes the app honest about uncertainty, lowers health-liability risk, and creates a real learning loop instead of a one-off novelty scan.
- Narrowed the intervention to one actionable recommendation per check: either wait longer before lying down or reduce/remove one high-risk trigger tag. This keeps the product useful in 2-4 weeks and avoids fake optimization theater.
## Improved Description
Reflux Receipt becomes a 14-day nighttime reflux coach for people who already know they get heartburn at night and want to spot their own patterns fast. Instead of scanning food, the user logs dinner or late-night snacks with a tiny structured library of trigger tags such as spicy, tomato, fried, chocolate, mint, alcohol, caffeine, citrus, large portion, and carbonated drink, then sets how soon they expect to lie down. The app returns an honest risk band like `Low tonight`, `Caution if you lie down within 2 hours`, or `High chance this combo triggers symptoms for you`, plus one action: wait X longer before bed, cut one trigger, or shrink the portion.

The real product is the feedback loop the next morning. Users log whether symptoms happened and how bad they were. After a few nights, the app surfaces plain-English personal patterns such as "3 of your last 4 nights with tomato + bed within 90 minutes led to symptoms" or "Spicy food alone was usually fine, but spicy + large portion was not." The app is explicitly framed as symptom-risk coaching and self-observation, not diagnosis, treatment, or clinically validated prediction.

This makes the MVP viable because the engine is simple and inspectable: a small curated trigger taxonomy, a rules table for initial risk bands, and personal pattern summaries from the user's own logs. No image recognition, no nutrition API dependency, no fake precision, no partnerships. Content prep is part of the work: the builder must create the starter trigger list, risk rules, symptom scale, copy for interventions, and pattern-summary templates before shipping.

Why would users come back in week 2? Because week 1 creates personal evidence, and week 2 helps them test changes. The retention mechanic is a nightly check-in streak plus weekly "pattern found" and "try this experiment tonight" cards that turn the app from generic advice into a self-learning trigger journal.

### Assumption Check
- People with recurring nighttime reflux will manually log food and symptoms for at least 7-14 days. `UNVERIFIED`
  Fallback plan: if logging compliance is weak, reposition v1 as a 7-night guided reflux reset with a shorter commitment and fewer fields.
- A small curated trigger taxonomy plus bedtime timing is enough to produce useful first-session value before personal history exists. `UNVERIFIED`
  Fallback plan: if users find the initial check too generic, reduce the promise further and market the app primarily as a pattern finder that gets useful after 3 logs, not as an instant coach.
- Camera-based food recognition as the core differentiator for reflux coaching. `FALSE`
  This is removed from v1 and should not appear in the pitch, MVP, or moat story.
## Why This Is Worth Building (Solo + AI)
This is now a rules-driven journaling product, not a fake medical AI predictor, so a solo developer can ship it quickly with standard app scaffolding and AI-assisted UI iteration. The value proposition is also stronger: instead of telling users what they already know in generic terms, it helps them discover their own repeatable nighttime trigger patterns.
## Solo MVP Scope
- What's in v1: onboarding and safety framing, evening food log with fixed trigger tags and bedtime slider, immediate risk band with one intervention, morning symptom check-in, history view, simple pattern summaries after enough logs, and streak or experiment reminders.
- What's out of v1: camera scan, image recognition, portion estimation from photos, ingredient extraction, nutrition database integrations, precise time-window prediction, generalized medical advice, clinician sharing, community features, payments, and any feature that depends on user-generated content or network effects.
- Estimated build time with AI coding tools: 2-3 weeks total, including UI build, starter trigger taxonomy and rule-table content prep, intervention copywriting, local data model setup, analytics events, and GitHub/App Store integration setup.
## Open Questions
- Is the target user committed enough to do both evening and morning logging, or does the flow need to collapse into a shorter 7-night experiment?
- Which 8-12 trigger tags belong in the initial library so the app feels useful without becoming a nutrition database project?
- What exact legal copy and onboarding language best keeps the product in symptom-tracking territory rather than medical guidance?
- How many logs are needed before pattern summaries feel trustworthy instead of noisy?
- If the personal-pattern loop underperforms, should the fallback wedge be a stricter "late-night reflux reset" coach instead of an ongoing tracker?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Welcome / Positioning — explain the app as a nighttime reflux tracker, not a medical predictor; key elements: value proposition, disclaimer, start button.
- Screen 2: Onboarding Trigger Setup — let users select common known triggers and bedtime habits they already suspect; key elements: trigger chips, usual bedtime range, optional reflux frequency baseline.
- Screen 3: Home / Tonight Check — main evening entry point; key elements: current streak, add tonight's meal button, recent pattern card, latest experiment prompt.
- Screen 4: Food Log Entry — structured manual logging flow; key elements: meal name text field, trigger-tag picker, portion size selector, alcohol/caffeine toggles if not tags, planned lie-down slider.
- Screen 5: Tonight Result — show honest risk band and one action; key elements: risk label, short explanation of which tags drove it, single recommended action, save log button.
- Screen 6: Morning Check-In — capture outcome; key elements: did symptoms happen, severity scale, wake-up disruption toggle, optional notes.
- Screen 7: History — show recent nights and outcomes; key elements: chronological log list, filters by trigger tag, quick view of risk vs symptom outcome.
- Screen 8: Patterns / Insights — surface personal trigger findings; key elements: top trigger combinations, confidence language based on count of logs, experiment cards for the next few nights.
- Screen 9: Settings / Safety — legal framing and controls; key elements: symptom-tracking disclaimer, reminder settings, data export/delete.
Key interactions:
- User lands on Home, logs tonight's food, sees a risk band on Tonight Result, then returns the next morning to complete Morning Check-In.
- After 3 or more completed logs, Home and Patterns begin surfacing personalized findings and a suggested experiment for the next night.
- History links into individual entries so users can compare what they ate, what risk band they received, and what symptom outcome they logged.
## Version History
- v1: Original idea
- v1.1: Critique - image-first fake precision, weak retention, and hidden content complexity made the concept untrustworthy for a solo MVP
- v2: Improved - rebuilt as a manual-entry personal trigger tracker with honest risk bands, one clear intervention, and a repeat-use learning loop
