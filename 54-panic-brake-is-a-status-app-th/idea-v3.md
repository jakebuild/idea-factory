# Idea #54: (Improved)
## Version: v3
**Date:** 2026-04-02 22:53
**Status:** improved
## Original Idea
Panic Brake is a status app that copies the Poised model but compresses it into a sharper mobile reveal for high-stakes moments. Two minutes before a meeting, interview, sales call, or date, you read one short prompt and the app measures pace, filler words, volume consistency, and camera-based pulse, then gives you a 60-second reset drill and a second retest so the loop becomes check, reset, retest, walk in greener than before. The reveal is instant and screen-recordable: your pulse drops, your filler-word risk flips from red to green, and the app shows that you sound calmer before the conversation even begins.
## What Changed and Why
- The product is no longer pitched as a "calm check" or a prettier voice memo. v3 is a web-first interview answer drill that grades one thing users actually care about: did this answer land as a clear, complete interview response. That fixes the critique that pace and filler words alone are too weak and too easy to game.
- MVP scope is cut harder. The dashboard is removed, the question bank drops to 15-20 high-quality prompts, the reset library drops to 5 drills, and there is no answer archive narrative beyond per-question history. This directly honors the critique's cuts and keeps the build inside a realistic solo 2-4 week window, including content prep and transcription/privacy UX.
- The core loop now adds a lightweight structure check and explicit retry queue. After each take, the app flags missing pieces such as "no direct answer," "weak example," or "missing outcome," then pairs that with one concrete redo drill. That gives users a reason to return in week 2: finish the remaining question pack and clear the questions still marked "needs redo," not just watch generic metrics move.
## Improved Description
Rebuild Idea #54 as a web app for practicing tighter interview answers, not for measuring stress. The user picks one of 15-20 curated interview questions across four packs: opener, behavioral stories, role fit, and wrap-up. They record a 60-90 second answer from their laptop, get a transcript, then receive a compact scorecard with only four outputs: pace, filler words, answer length, and a simple structure check.

The structure check is the actual product value. For each question type, the app evaluates whether the answer hit the expected bones of a good response. Example outputs: "Direct answer missing in first 15 seconds," "Situation and action present, outcome missing," or "Too abstract, no concrete example." This is intentionally lightweight and rule-based, not a fake AI oracle. It is better than raw speech metrics because it pushes the user toward stronger answers, not just slower answers.

After the first take, the app assigns one of 5 specific redo drills tied to the failure mode. Example drills:
- "Answer-first": say the conclusion in one sentence before the example
- "STAR compression": give situation, action, result in 3 beats
- "Outcome snap": end with a measurable result or lesson
- "Cut the runway": restart and remove the first 10 seconds of throat-clearing
- "Specifics only": replace generic claims with one concrete detail

The retake comparison shows whether the answer actually improved. The app highlights transcript deltas, shows whether the missing structure flags were cleared, and saves only a per-question attempt history with latest status: "solid" or "needs redo." There is no broad dashboard, no answer library positioning, and no anxiety framing.

Why would users come back in week 2? Because the product gives them an unfinished practice plan, not a one-off reading. They still have uncompleted question packs, and some practiced questions remain marked "needs redo" until they produce one solid version. That creates a concrete repeat loop during an active interview cycle without depending on social features, marketplaces, or fake progress charts.

3 biggest assumptions that could kill the product:
- VERIFIED: Users care more about tightening answer structure than seeing raw voice metrics alone. The critique explicitly asked for a structure check because that is closer to real interview value than pace or filler counts by themselves.
- UNVERIFIED: A simple rules-based structure check plus transcript review will feel trustworthy and useful enough without requiring a heavier LLM judging pipeline. Because this is unverified, it is not the moat. Fallback plan: if users find the rule-based feedback too shallow, keep the same flow but reposition the app as a practice accountability tool and add one narrow LLM pass only for "missing outcome / missing specificity" on a small subset of question types.
- UNVERIFIED: Users will return across multiple days to complete question packs and clear redo items. This cannot be the moat either. Fallback plan: if week-2 retention is weak, collapse the product into a one-session "night before interview" rehearsal tool and optimize for one high-value prep session instead of a longer habit loop.
## Why This Is Worth Building (Solo + AI)
This version is viable because it turns a fuzzy coaching app into a narrow workflow with finite content and mostly deterministic analysis. A solo developer can ship it quickly with AI coding tools by building one web recording flow, one transcription integration, one rule-based structure checker, and a small curated question/drill set instead of chasing mobile polish, fake sensing, or SaaS garnish.
## Solo MVP Scope
- What's in v1: web onboarding and positioning; 15-20 curated interview questions grouped into 4 packs; 5 scripted redo drills; browser recording; transcription; pace, filler-word count, and answer-length metrics; rule-based structure checks by question type; transcript review with lightweight edit/correct option before analysis rerun; retake comparison; per-question history with latest status and attempt count; privacy/delete controls
- What's out of v1: mobile-first build, dashboard/trend charts, camera pulse, volume scoring, calm/confidence claims, meetings/dates/sales-call scenarios, AI-generated custom question banks, user-uploaded job-description tailoring, answer library product narrative, social sharing, community content, moderation, payments, and any feature that depends on users contributing content
- Estimated build time with AI coding tools: 2.5-4 weeks total, including 2-3 days to curate and structure 15-20 strong questions plus 5 redo drills, 5-7 days for the web UI and recording flow, 3-4 days for transcription and analysis integration setup, 3-4 days for rule-based structure checks and retake comparison, 2-3 days for transcript correction/privacy/deletion UX, and 2-4 days for QA on browser audio, interrupted recordings, noisy input, and latency
## Open Questions
- Which transcription provider keeps 60-90 second answers reliably under an acceptable wait time and cost for MVP?
- Which 15-20 interview questions produce the strongest repeat practice behavior: broad universal questions, or a tighter mix focused mostly on behavioral and opener prompts?
- Is the transcript-correction step enough to preserve trust when speech-to-text is wrong, or do users still feel the app is too brittle?
- Does the product need one optional text-only mode for users who want to paste an answer draft before speaking, or is that unnecessary scope for v1?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / Positioning — explains the promise "practice tighter interview answers," shows the record-review-redo loop, and makes clear this is not an anxiety or biometric product
- Screen 2: Onboarding / Privacy — explains microphone usage, transcript storage, delete controls, and lets the user start with the default question packs
- Screen 3: Question Packs — shows the 4 packs, progress by pack, and which questions are unstarted, solid, or need redo
- Screen 4: Question Detail / Prep — displays the selected question, what a strong answer should include, prior attempt count, latest status, and the CTA to start recording
- Screen 5: Record Answer — browser recording screen with timer, input-level hint, restart/stop controls, and a clear note on target answer length
- Screen 6: Processing — temporary state while transcription and analysis run, with progress feedback and concise trust-building copy about what is being checked
- Screen 7: First Take Review — shows transcript, lets the user make quick transcript corrections if needed, and rerun analysis before continuing
- Screen 8: First Take Result — displays pace, filler words, answer length, structure flags, and the recommended redo drill tied to the weakest part of the answer
- Screen 9: Redo Drill — guided short exercise for the selected drill with a checklist or countdown and a CTA into the retake
- Screen 10: Retake Comparison — compares take 1 vs take 2, highlights cleared structure flags and transcript improvements, and lets the user save the attempt
- Screen 11: Question History — per-question list of past attempts with dates, latest status, and links to reopen a prior session
- Screen 12: Session Detail — full view of one saved session with both takes, transcript, metrics, structure flags, and redo outcome
- Screen 13: Settings / Data Controls — microphone permission state, transcript/audio retention preference, delete-all and per-session delete actions, and basic account-less/local-first copy if used
Key interactions:
- User lands on Landing / Positioning, accepts privacy terms in Onboarding, and enters Question Packs
- User opens a question from Question Packs, reviews the prompt in Question Detail / Prep, and starts Record Answer
- After Recording, user moves through Processing into First Take Review, fixes transcript errors if needed, and continues to First Take Result
- From First Take Result, user starts the assigned Redo Drill, records a retake, and lands on Retake Comparison
- From Retake Comparison, user saves the session, which updates Question Packs and Question History with either "solid" or "needs redo"
- User returns later to Question Packs to complete untouched questions or reopen redo-marked questions until each core question has one solid answer
## Version History
- v1: Original idea
- v2.1: Critique - still too close to a voice memo with weak metrics, soft retention, fragile content, and bloated dashboard/polish scope
- v3: Improved - repositioned as a web-first interview answer drill with structure checks, redo-based retention, smaller curated content, and no dashboard fluff
