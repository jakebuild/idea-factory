# Idea #54: (Improved)
## Version: v2
**Date:** 2026-04-02 22:48
**Status:** improved
## Original Idea
Panic Brake is a status app that copies the Poised model but compresses it into a sharper mobile reveal for high-stakes moments. Two minutes before a meeting, interview, sales call, or date, you read one short prompt and the app measures pace, filler words, volume consistency, and camera-based pulse, then gives you a 60-second reset drill and a second retest so the loop becomes check, reset, retest, walk in greener than before. The reveal is instant and screen-recordable: your pulse drops, your filler-word risk flips from red to green, and the app shows that you sound calmer before the conversation even begins.
## What Changed and Why
- The app is narrowed to one wedge: job interviews. Meetings, sales calls, and dates are cut because they need different prompts, privacy expectations, and coaching logic. Interviews are the cleanest use case because people already rehearse for them across multiple weeks, which gives the product a believable repeat-use window.
- Camera pulse, volume consistency, and "calmer" detection are removed entirely. v1 now measures only two explainable speech signals from audio plus transcript: speaking pace and filler words, alongside a user self-rating before and after the reset. This fixes the trust problem by limiting the app to signals a solo developer can ship and defend.
- The product changes from a last-minute truth machine into an interview rehearsal loop. Instead of promising "we know you are calmer," it promises "we help you sound more composed answering common interview questions." That creates a viable week-2 retention mechanic: users return to practice a new question, watch their own metrics improve, and build a reusable answer library during an active job search.
## Improved Description
Build Panic Brake as a mobile-first interview warmup app for people preparing for job interviews over a 2-6 week search cycle. The user picks a question from a curated bank such as "Tell me about yourself," "Describe a conflict," or "Why this role?", records a 45-90 second answer, and gets a fast readout on only what the app can actually measure: words per minute, filler-word count, transcript, and answer length. The app also asks one subjective check before and after the reset drill: "How steady do you feel right now?" on a 1-5 scale. That lets the product acknowledge nerves without pretending the phone can detect them.

After the first take, the app gives one concrete reset instruction tied to interview performance, not wellness theater. Example: "Take one breath, then answer in 3 beats: situation, action, result." The user retakes the answer immediately and sees the delta: fewer filler words, tighter answer length, slower pace, and their own self-rating shift. The payoff is still a before/after reveal, but it is grounded in transcript and timing evidence instead of fake biometrics.

The key product loop is not "check yourself two minutes before anything scary." It is "practice one interview answer a day until you sound tighter." Each session is saved to a question-specific history so users can see improvement on the exact answers they are likely to use. The app ships with a fixed starter bank of 30-40 interview questions across intro, behavioral, strengths, conflict, and motivation categories, plus 8-10 scripted reset drills. That content prep is part of the MVP workload, not hand-waved away.

3 biggest assumptions that could kill the product:
- FALSE: The product needs camera-based pulse, volume sensing, or "calm detection" to be compelling. The critique effectively invalidated that assumption; those features add trust risk and scope without creating a reliable core value, so they are removed rather than treated as the moat.
- VERIFIED: Interviews are a better MVP wedge than dates, meetings, or sales calls because users already accept prep rituals, repeated rehearsal, and structured prompts in that context. This does not prove retention by itself, but it does justify the narrower starting market.
- UNVERIFIED: Job seekers will repeat this rehearsal loop enough across a search cycle to create real week-2 retention, and pace + filler-word feedback plus transcript replay will feel useful enough on their own without deeper coaching. Because this is unverified, it is not presented as the moat. Fallback plan: if repeat usage or perceived value is weak, reposition the app as a one-session pre-interview prep utility and later add one transcript-only rubric for answer shape instead of new sensors.

Why would users come back in week 2? Because an active job search creates multiple interview rounds and repeated practice sessions, and the app stores progress by question. In week 2, the user is not reopening a generic anxiety app; they are reopening their saved answers for "Tell me about yourself" and "Biggest challenge," trying to beat last week's pace/filler baseline before the next call.
## Why This Is Worth Building (Solo + AI)
This is now a small, defensible product: curated interview prompts, mobile recording, speech-to-text, basic transcript analysis, and a side-by-side retake flow. A solo developer can ship that with AI coding tools because the hard problem is reduced to one narrow workflow, and the content prep is finite enough to do by hand in the same 2-4 week window.
## Solo MVP Scope
- What's in v1: interview-only onboarding; curated bank of 30-40 interview prompts; record answer flow; transcription; analysis for words per minute, filler words, and answer length; one self-rating before/after reset; one reset drill per session from a fixed drill library; immediate retake with side-by-side delta; session history by question; simple progress dashboard for repeat practice
- What's out of v1: camera pulse, camera permissions, volume consistency scoring, claims about detecting calm, meetings, dates, sales calls, real-time coaching during live calls, AI-generated persona judgments, shareable social reveal features, marketplace/community features, UGC prompt libraries, payments, and any health framing
- Estimated build time with AI coding tools: 2.5-3.5 weeks total, including 2-3 days for curating and structuring 30-40 interview prompts plus 8-10 reset drills, 5-7 days for mobile UI and recording flow, 3-4 days for transcription and analysis integration setup, 3-4 days for retake comparison and history views, and 2-3 days for polish, copy, and QA on permission and latency edge cases
## Open Questions
- Should v1 use a fixed starter prompt bank only, or also allow the user to paste their own interview questions from a job description? The latter is attractive, but it adds prompt-quality and formatting edge cases.
- Is transcription latency on the target stack fast enough to keep the post-recording wait under roughly 10 seconds for a 60-second answer?
- What is the minimum useful question bank for launch: 20 prompts, 30 prompts, or 40 prompts? This directly affects content-prep time and perceived value.
- Do users prefer a pure mobile app for this ritual, or would a simple web app lower build time enough to be worth trading away microphone UX quality?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Onboarding / Positioning — explains that the app is for interview rehearsal, not anxiety diagnosis; highlights the simple loop of answer, reset, retake; sets privacy expectations for microphone use
- Screen 2: Question Bank — shows curated interview categories and question cards, lets the user pick a question to practice, and shows which questions already have saved attempts
- Screen 3: Question Detail / Prep — displays the selected question, a short tip on what a strong answer should cover, previous best metrics for this question if available, and the CTA to start recording
- Screen 4: Record Answer — main recording screen with timer, transcript-placeholder state, large stop button, and minimal UI so the user can focus on speaking
- Screen 5: Processing — short intermediate state for transcription and analysis, showing progress and reassuring copy about what is being analyzed
- Screen 6: First Take Result — shows transcript, answer length, speaking pace, filler-word count, pre-reset self-rating, and one recommended reset drill with clear CTA to retry
- Screen 7: Reset Drill — simple guided screen for the selected reset instruction, such as breathe once and answer in 3 beats, with a countdown or short checklist before the retake starts
- Screen 8: Retake Comparison — side-by-side comparison of first take versus second take for pace, fillers, answer length, and self-rating, plus the option to save this attempt to the question history
- Screen 9: Practice History / Dashboard — shows all practiced questions, last attempt date, best metrics by question, and a simple trend summary across recent sessions
- Screen 10: Session Detail — lets the user revisit a past session with transcript, metrics, and whether the retake improved the answer
- Screen 11: Settings / Privacy — microphone permission status, data retention explanation, option to delete recordings/transcripts/history, and basic preferences
Key interactions:
- User completes Onboarding once, lands in Question Bank, chooses a question, and enters Question Detail
- From Question Detail, user starts Record Answer, moves through Processing, and lands on First Take Result
- From First Take Result, user opens Reset Drill, completes the drill, records again, and lands on Retake Comparison
- From Retake Comparison, user saves the session and returns to Practice History or the Question Bank
- From Practice History, user reopens a question to compare against previous attempts before the next interview round
## Version History
- v1: Original idea
- v1.1: Critique - fake certainty, unverified pulse sensing, too many measurement problems at once, mixed use cases, weak retention, and trust risk from overclaiming calm
- v2: Improved - narrowed to interview rehearsal, cut all fake biometrics and extra sensing, and turned the product into a repeatable question-by-question practice loop
