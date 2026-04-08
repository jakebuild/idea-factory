# Idea #70: (Improved)
## Version: v2
**Date:** 2026-04-08 10:05
**Status:** improved
## Original Idea
VoiceMirror is a career anxiety app that copies the ElevenLabs model but applies it to self-rehearsal instead of content creation. You record an interview answer, pitch, difficult conversation opener, or toast, and the app generates a better version in your own voice that sounds calmer, clearer, and more confident. The reveal is immediate and screen-recordable: you hear your own voice say the exact same thought better than you could, then see the two phrasing and pacing changes that made the difference. The loop is record, hear your upgraded version, shadow it, retry, and come back before every high-stakes conversation because the product turns vague communication advice into an audible before-and-after.
## What Changed and Why
- Cut cloned voice from v1 entirely and repositioned the product as interview rehearsal software, not a general "career anxiety app." This removes the biggest technical and trust risk the critique identified, makes the value proposition clearer, and keeps the MVP useful without depending on a fragile external magic trick.
- Narrowed the wedge to job interviews and built around a concrete workflow: pick interview type, practice with a curated question pack, get a meaning-safe rewrite plus delivery feedback, then retry. This is better because it creates a measurable job to be done, simpler onboarding, and clearer acquisition than trying to serve interviews, pitches, toasts, and hard conversations at once.
- Added an authenticity control, weak-answer tracking, and a 7-day interview prep plan instead of vague "hear yourself better" polish. This fixes the trust problem, gives users a reason to come back in week 2, and turns the product into a repeatable rehearsal tool rather than a one-time demo.
## Improved Description
VoiceMirror v2 is an interview rehearsal app for job seekers who have a real interview coming up in the next 1-14 days. The user starts by choosing a scenario such as recruiter screen, hiring manager interview, behavioral interview, or salary conversation. The app gives them a small curated pack of common questions for that scenario, lets them record or type an answer, transcribes it, and returns three things: a cleaned-up version of their answer, a delivery review with concrete flags like filler words, rambling, or weak opening, and a retry prompt that tells them exactly what to improve on the next take.

The key trust mechanic is authenticity control. Before generating revisions, the user chooses one mode: preserve wording, preserve meaning, or rewrite more directly. That makes the product safer for interviews, where wording changes can distort intent. Instead of claiming "this is you but better," the product says "this is the same answer, made easier to deliver under pressure." The output is text-first and practice-first: transcript diff, highlighted pacing breaks, and a shadow-practice view that lets the user rehearse the improved answer in their own real voice.

The retention story is not daily habit; it is interview-cycle repetition. Users come back in week 2 because the app saves weak questions, generates a short daily drill list from past misses, and counts down to the interview date with a focused prep plan. A user preparing across multiple rounds can return to the same question bank, compare takes, and see whether they are improving on clarity, length, and confidence markers. That is episodic, but still strong enough if the product is framed and priced around upcoming high-stakes interviews rather than generic self-improvement.

This is viable for a solo builder because the MVP only needs recording, transcription, structured rewrite prompts, basic speech metrics, saved sessions, and a small manually curated question library. No voice cloning, no social loop, no user-generated marketplace, no moderation, and no payments/payouts in v1.
## Why This Is Worth Building (Solo + AI)
This version is worth building because it replaces an unverified deep-tech promise with a narrow workflow that can be shipped using standard speech-to-text, one LLM pass, and simple audio analysis. A solo developer can use AI coding tools to assemble the recorder, transcript/rewrite pipeline, practice UI, and question-pack scaffolding quickly, while the product still delivers a clear outcome: better-prepared interview answers before a real event.
## Solo MVP Scope
- What's in v1: interview-only positioning; 3-4 curated interview packs; record or type answer; transcript view; authenticity control with three rewrite levels; delivery feedback on answer length, filler words, pacing, and weak openings; retry flow; saved sessions; 7-day interview prep checklist with daily weak-question drills; lightweight privacy controls such as local deletion and short retention defaults.
- What's out of v1: cloned voice playback; non-interview scenarios like toasts or difficult personal conversations; arbitrary "two changes" explanation gimmick; social sharing/virality features; marketplace/community content; team dashboards; coaching marketplace; payments or subscriptions inside the product; mobile apps; custom deep integrations with job boards or calendar platforms.
- Estimated build time with AI coding tools: 2.5-3.5 weeks total, including 4-5 days of content prep for curated question packs and metadata, 1-2 days of transcription/LLM integration setup, and 8-12 days for the UI, practice loop, persistence, and privacy handling.
## Open Questions
- Biggest assumption 1: Users will trust AI-generated rewrites for interview prep if authenticity is clearly controlled and transcript diffs are visible. `UNVERIFIED`. This is important, but it is not the moat. If trust is weak in testing, fallback is a stricter "coach mode" that only flags issues and suggests edits without auto-rewriting the full answer.
- Biggest assumption 2: A manually curated starter library of interview questions is enough to make the product useful without requiring user-generated content or large proprietary data. `UNVERIFIED`. This content-prep work is included in scope; if the first packs feel too shallow, fallback is to narrow further to behavioral interviews only and improve quality instead of breadth.
- Biggest assumption 3: Voice cloning is a valid MVP differentiator. `FALSE`. The critique already showed this should not be treated as the core product, so it is removed from v1 and moved entirely out of the pitch.
- Can the app produce delivery feedback that feels specific enough to be useful without pretending to do expert-grade coaching?
- What privacy/storage setup is credible enough for sensitive interview practice: browser-first local storage, short-lived server retention, or both?
- Which initial interview packs create the clearest wedge: behavioral, recruiter screen, hiring manager, or salary negotiation?
- What willingness to pay exists for an interview-cycle tool versus a recurring subscription, given the episodic use pattern?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing page — explain the product as interview rehearsal for an upcoming interview, show the core workflow, trust/authenticity positioning, and CTA to start a practice plan.
- Screen 2: Interview setup — collect interview date, interview type, role/function, and experience level; show the selected question pack and estimated practice plan.
- Screen 3: Dashboard / prep plan — show countdown to interview, today's drill list, recent sessions, weakest questions, and progress across saved answers.
- Screen 4: Question pack picker — let users browse the curated packs, preview questions, and choose a practice session.
- Screen 5: Practice recorder — present one interview question, recording controls, typed-answer option, timer, and state for processing.
- Screen 6: Answer review — show transcript, original answer stats, authenticity control selection, improved version, diff view, and concrete delivery flags.
- Screen 7: Shadow practice — show the improved answer formatted with pacing breaks, emphasis cues, and a simple read-aloud / retry interface using the user's own natural voice.
- Screen 8: Retry comparison — compare previous and latest take on the same question, including answer length, filler count, pacing notes, and whether the main coaching issue improved.
- Screen 9: Session history — list past practice sessions by date and interview type, with quick access back into saved answers and weak-question tags.
- Screen 10: Privacy and data controls — explain retention defaults, let users delete recordings/transcripts, and manage whether audio is stored or processed temporarily.
Key interactions:
- User lands on the product, starts an interview plan, and selects an interview type plus date.
- User enters the dashboard, chooses today's drill or opens a question pack.
- User records or types an answer to one question, waits for processing, and reviews transcript plus coached rewrite.
- User switches authenticity mode, reviews the diff, then moves into shadow practice.
- User retries the answer, compares versions, and saves the result back into the prep plan.
- User returns on later days to complete the next weak-question drill before the scheduled interview.
## Version History
- v1: Original idea
- v1.1: Critique - main issues were overreliance on unverified voice cloning, mushy positioning, trust risk, weak retention, and over-scoped MVP complexity
- v2: Improved - narrowed to interview rehearsal, removed cloned voice from MVP, added authenticity controls and a repeatable prep-plan loop
