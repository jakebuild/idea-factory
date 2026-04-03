# Idea #57: (Improved)
## Version: v3
**Date:** 2026-04-03 07:57
**Status:** improved
## Original Idea
Reflux Receipt is a health app that copies the Cal AI model but applies it to late-night heartburn anxiety instead of calories.
## What Changed and Why
- Replaced the open-ended "coach" with a strict 7-night reflux experiment. This fixes the retention and honesty problem: a short protocol is more believable than an ongoing journaling habit, and it gives users a clear finish line instead of vague self-improvement.
- Removed the instant risk band and any predictive language from v1. This directly addresses the critique that early value was just common-sense advice wearing medical-looking confidence; v1 now focuses on structured evidence capture and only shows findings after enough completed nights.
- Shrunk the data model to the minimum useful variables and made the output a completion report, not fake personalization. This reduces manual burden, avoids thin-data overfitting, includes the real content-design work in scope, and makes the product shippable by one person in 2-4 weeks.
## Improved Description
Reflux Receipt becomes a 7-night nighttime reflux experiment for people who already suspect they get heartburn from late eating but have never captured a clean personal pattern. The product is not a predictor, not a diagnosis tool, and not an ongoing wellness coach. It is a short protocol that helps a user log the same small set of variables for one week, then produces a blunt summary of what did and did not line up with symptoms.

Each night, the user logs only the fields that matter for a first-pass reflux experiment: meal time, planned lie-down time, portion size, and up to two trigger tags from a fixed list. The trigger list is intentionally tiny: `spicy`, `tomato/citrus`, `fried/fatty`, `alcohol`, `caffeine/carbonated`, and `chocolate/mint`. The next morning, the user completes a 10-second check-in: symptoms `none / mild / moderate / severe`, sleep disruption `yes / no`, and optional note. That morning step is the only required second habit, so the app is designed around ruthless speed, reminders, and a visible 7-night countdown.

The first-session value is not "AI insight." It is a credible protocol with a finish line: the app tells the user exactly what they are doing for the next 7 nights, what data will be collected, and what kind of report they will get at the end. The value proposition is: "Stop guessing. Run one clean week and get a usable pattern summary." That is materially better than generic reflux articles because it turns vague self-knowledge into a structured record the user can actually review.

Insights are gated hard. Before 5 completed nights, the app shows no trigger conclusions at all, only progress and consistency prompts. After 5 nights, it may show limited findings with explicit confidence labels such as `weak signal` or `moderate signal`, based on simple count thresholds the user can inspect. Example: "Symptoms followed 3 of 4 nights when you lay down within 90 minutes. Signal: weak." It must also be able to say "No usable signal yet" without trying to sound smart. That keeps the product honest and reduces fake-personalization theater.

The end of the protocol is the real deliverable: a one-screen reflux summary with counts, patterns that crossed threshold, variables with no signal, and one suggested next experiment that changes only one thing, such as "Repeat for 7 nights while keeping 2+ hours upright after dinner." That suggestion is framed as a self-test plan, not advice. If the user wants week-2 value, that is the answer: they come back to run one comparison round with a single variable changed, or they use the report in a doctor conversation. If users do not come back for a second round, the product still works as a one-week utility instead of pretending to be a sticky daily app.

This is viable because the hard part is not model-building; it is content design and scope discipline. V1 needs a fixed trigger taxonomy, confidence thresholds, severity scale, safety copy, reminder copy, protocol instructions, and report templates. That content-prep work is part of the estimate, not hand-waved away.

Three biggest assumptions that could kill the product:
- `VERIFIED` A solo developer can ship a tiny structured tracker with reminders, thresholds, and summary reporting in 2-4 weeks if the scope stays this small. This is supported by the critique's own "maybe" verdict and the absence of heavy integrations.
- `UNVERIFIED` Enough users will complete at least 5 of 7 nights to unlock a report. If this fails, the product is not a business; fallback plan: reposition it as a one-off guided protocol distributed through low-fidelity pilots, not a retention-driven app.
- `UNVERIFIED` A 7-night summary report is valuable enough that some users will return in week 2 for a second comparison round. Because this is unverified, it is not the moat; fallback plan: treat repeat usage as upside, and pitch v1 as a finite self-assessment tool, not a habit product.
## Why This Is Worth Building (Solo + AI)
This version is worth building because it cuts the fake-intelligence layer and turns the idea into a bounded, testable utility with a clear completion moment. A solo developer can ship the UI, local data model, reminder flow, safety framing, and threshold-based summary logic quickly with AI coding tools, then test whether people will actually finish a 7-night protocol before investing further.
## Solo MVP Scope
- What's in v1: safety-first onboarding, 7-night protocol setup, nightly log with fixed tags and defaults, morning symptom check-in, reminder scheduling, progress countdown, evidence-gated findings after enough nights, final 7-night summary report, and export/share of the summary as plain text or PDF.
- What's out of v1: instant risk bands, predictive scores, camera scan, image recognition, nutrition database integrations, ongoing coaching, community or UGC, clinician dashboard, medication tracking, wearable integrations, payments, and anything that depends on network effects, moderation, or partnerships.
- Estimated build time with AI coding tools: 2.5-3.5 weeks total, including UI build, local storage and reminder integration setup, export generation, trigger taxonomy definition, confidence-threshold rules, severity scale design, onboarding and safety copy, notification copy, and report-template content prep.
## Open Questions
- Will users actually complete a 7-night protocol if the morning check-in is reduced to one tap plus optional note?
- Is the final report more valuable as a self-review artifact, a doctor-conversation artifact, or both?
- What exact threshold should define `weak signal` versus `moderate signal` so the app stays honest without feeling empty?
- Should the second-round comparison protocol be in v1 at all, or should v1 stop cleanly after the first 7-night report?
- What onboarding language most clearly says "pattern tracking only, not medical guidance" without sounding evasive?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Welcome / Safety Framing — explains the 7-night experiment, says the app is for symptom tracking not diagnosis, and sets expectations for the final report.
- Screen 2: Protocol Setup — collects usual bedtime, reminder preferences, and optional suspected triggers; shows the fixed 7-night structure and what will be logged.
- Screen 3: Home / Progress Tracker — shows current night number, completion countdown, next required action, reminder status, and whether findings are still locked.
- Screen 4: Night Log — captures meal time, planned lie-down time, portion size, and up to two trigger tags with aggressive defaults for speed.
- Screen 5: Night Logged Confirmation — confirms the entry was saved, shows tomorrow's check-in reminder time, and reinforces that no findings are shown until enough nights are complete.
- Screen 6: Morning Check-In — one-tap symptom severity, sleep disruption toggle, optional note, and completion confirmation.
- Screen 7: Findings Preview — unlocked only after the evidence threshold; shows limited signals, "no usable signal" states, and explicit confidence labels.
- Screen 8: Final 7-Night Report — summarizes completed nights, strongest observed patterns, no-signal variables, and one suggested next experiment that changes only one variable.
- Screen 9: Report Export / Share — lets the user copy, export, or save the final summary as plain text or PDF for personal review or doctor discussion.
- Screen 10: Settings / Legal / Data — reminder controls, export/delete data, safety language, and a clear explanation of how the app avoids medical claims.
Key interactions:
- User starts on Welcome, completes Protocol Setup, lands on Home, and is guided into the Night Log for each of the 7 nights.
- After a Night Log, the app routes to Night Logged Confirmation and then back to Home until the morning reminder drives the user into Morning Check-In.
- Completed mornings update Home progress; Findings Preview remains locked until the threshold is met, then becomes visible from Home.
- After night 7 or protocol completion, Home routes to Final 7-Night Report, and the user can continue to Report Export / Share.
## Version History
- v1: Original idea
- v2.1: Critique - still too close to a symptom journal, weak day-1 value, weak retention, fake-personalization risk, and understated content/safety work
- v3: Improved - rebuilt as a finite 7-night reflux experiment with no prediction theater, hard evidence thresholds, and a concrete end-of-week report
