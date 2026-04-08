# Idea #70: (Improved)
## Version: v3
**Date:** 2026-04-08 10:11
**Status:** improved
## Original Idea
VoiceMirror is a career anxiety app that copies the ElevenLabs model but applies it to self-rehearsal instead of content creation.
## What Changed and Why
- Narrowed the product to behavioral interviews only and replaced broad "question packs" with a fixed story-bank workflow. This addresses the critique's scope and trust problems: one wedge is easier to explain, the content can be curated well by one person, and users are solving a real interview problem instead of browsing generic AI coaching.
- Flipped the core UX from rewrite-first to critique-first. The app now diagnoses missing proof, weak structure, and overlong answers before offering an optional outline rewrite. This is better because it avoids the "bullshitting machine" failure mode and gives value even for users who want diagnosis but do not want AI to write for them.
- Replaced the fake 7-day countdown plan with repeatable follow-up drills tied to the user's own story bank. This makes week-2 retention more credible: users come back to pressure-test the same stories from new angles, not to click through generic reminders.
## Improved Description
VoiceMirror v3 is a behavioral interview drill app for job seekers who need better, more defensible stories for STAR-style questions. The user records or types an answer to a behavioral prompt like "Tell me about a time you disagreed with a teammate." The app transcribes it and returns a strict diagnosis first: which STAR parts are missing, where the answer is too long, whether it lacks evidence like numbers or concrete actions, and one sentence on why that issue matters in an interview. There is no fake-smart personality scoring, no "confidence markers," and no cloned voice.

After diagnosis, the app builds a reusable Story Card from the answer: situation, task, action, result, proof points, and likely follow-up questions. The user can edit the card themselves. Only then can they opt into AI help, and that help is constrained: the default output is a tighter outline or bullet structure, not a polished script. A full rewrite is optional and clearly labeled as a draft to edit, not something to memorize blindly. This directly addresses the trust problem because the product's main job is to help users defend what they actually did, not generate prettier claims.

The real loop is story-bank rehearsal, not one-off rewriting. A small curated content set maps 25 behavioral interview questions into 8 story themes such as conflict, failure, ownership, prioritization, leadership, ambiguity, feedback, and impact. Once a user practices one story, the app serves new prompts and follow-ups that reuse that same evidence from different angles. That is the core value versus pasting a transcript into ChatGPT: faster reps, consistent structure checks, and side-by-side improvement on the same story across multiple questions in one session.

Why would users come back in week 2? Because they are no longer practicing isolated questions; they are building a compact interview story bank they can reuse across second-round and panel interviews. In week 2, the app can say: "Your conflict story still has no measurable result" or "You still freeze on follow-up probes about tradeoffs," then assign three short drills against that exact weakness. That is still episodic, but it is a stronger retention mechanic than a countdown timer and honest about the product's prep-sprint nature.

Privacy in v1 is handled with a local-first posture: transcripts, Story Cards, and session history are stored on-device by default, raw audio is sent only for transcription and not retained after processing, and users can delete a session or wipe all data in one action. The first paid offer, if tested, should be a one-time behavioral interview sprint rather than a recurring subscription.
## Why This Is Worth Building (Solo + AI)
This is worth building because it no longer relies on a flashy AI gimmick that users can get 70% of elsewhere; it packages a tighter drill loop around trust, repetition, and defensible story structure. A solo developer can ship it in 2-4 weeks with AI coding tools because the MVP is mostly recorder/transcript UI, a constrained analysis prompt, local persistence, and a manually curated question-to-story map.
## Solo MVP Scope
- What's in v1: behavioral interviews only; 25 curated questions mapped to 8 story themes; record or type answer; transcript review; critique-first analysis with only objective checks the app can explain; Story Card creation/editing; optional outline rewrite; follow-up probe drills; retry comparison; local-first session history; explicit delete-all privacy controls.
- What's out of v1: cloned voice; recruiter screens; salary negotiation; general career conversations; daily countdown plans; subjective labels like confidence or executive presence; automatic "meaning-safe" guarantees; community content; user-generated question packs; marketplace features; subscriptions, payments, or payouts inside the product; mobile apps.
- Estimated build time with AI coding tools: 3-4 weeks total, including 4-6 days of manual content prep for the 25 questions, 8 story themes, follow-up probes, and scoring rules; 1-2 days for transcription/LLM integration and privacy-safe storage setup; and 10-14 days for the UI, drill loop, Story Card editor, retry comparison, and testing.
## Open Questions
- Biggest assumption 1: Users will find critique-first behavioral drilling meaningfully better than "paste transcript into ChatGPT." `UNVERIFIED`. This is the strongest product risk, so it is not the moat. Fallback plan: position the app as a fast interview sprint tool with integrated reps and comparisons, not proprietary intelligence, and test willingness to pay as a one-time prep product.
- Biggest assumption 2: A manually curated starter set of 25 behavioral questions, 8 story themes, and follow-up probes is enough to make the app feel complete. `UNVERIFIED`. Fallback plan: cut to 12 excellent questions across 4 themes and improve depth rather than shipping a thin library.
- Biggest assumption 3: This can support a recurring subscription business from habit retention. `FALSE`. The honest model is episodic interview-cycle usage, so viability depends on acquisition efficiency and a believable one-time sprint offer, not recurring love.
- The best default AI assist still needs validation: outline rewrite, targeted edit suggestions, or full rewrite hidden behind a secondary action.
- Local-first privacy reduces risk, but the exact transcription setup still needs to be chosen and verified before build starts.
- No durable moat is proven yet. The launch case is a focused workflow and better trust, not defensibility.
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing page — explain the product as behavioral interview rehearsal, show the critique-first workflow, privacy stance, and CTA to start a sprint.
- Screen 2: Sprint setup — collect interview date, target role, and current confidence level; explain that v1 focuses on behavioral interviews only.
- Screen 3: Story bank dashboard — show the user's story themes, missing proof points, next recommended drill, and recent practice activity.
- Screen 4: Question drill screen — present one behavioral question, optional context, recording controls, typed-answer option, timer, and submit action.
- Screen 5: Processing state — show transcription/analyzing progress and set expectations for what feedback will appear.
- Screen 6: Answer diagnosis — show transcript, objective checks with short explanations, missing STAR sections, answer length, evidence gaps, and CTA to build/update a Story Card.
- Screen 7: Story Card editor — let the user edit situation, task, actions, result, proof points, and notes; show mapped follow-up probes.
- Screen 8: Optional rewrite view — show a tighter outline or draft answer derived from the Story Card, with a warning that the user should edit for truth and voice.
- Screen 9: Follow-up drill screen — ask tougher variants and probe questions against the same Story Card, then let the user retry immediately.
- Screen 10: Retry comparison — compare two takes on the same story with transcript snippets, duration, missing-section changes, and evidence improvements.
- Screen 11: Session history — list past drills, linked Story Cards, timestamps, and resume actions.
- Screen 12: Privacy and data controls — explain local storage, temporary audio processing, delete-session, and delete-all controls.
Key interactions:
- User lands on the app, starts a behavioral interview sprint, and enters interview date plus role context.
- User records or types an answer to a behavioral question and waits through transcription/analysis.
- User reviews diagnosis, updates the Story Card, and optionally generates a tighter outline.
- User starts a follow-up drill on the same story, retries, and compares take one versus take two.
- User returns later to the dashboard, sees which stories still have evidence gaps, and runs the next assigned drill.
## Version History
- v1: Original idea
- v2.1: Critique - too much rewrite magic, weak moat, thin retention, understated content work, and too many trust/privacy risks
- v3: Improved - narrowed to behavioral interviews, made critique-first the default, and centered retention on a reusable story-bank drill loop
