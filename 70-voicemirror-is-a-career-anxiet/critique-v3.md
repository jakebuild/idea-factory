Verdict: WORTH BUILDING
Score: 6/10
What's Actually Good:
- This is no longer a stupid gimmick. Narrowing to behavioral interviews turns the product into a real workflow instead of AI cosplay.
- Critique-first is the right correction. Diagnosis before rewrite is the only way this avoids feeling like a lying machine for nervous candidates.
- The Story Card concept is the first thing here that might actually beat a raw ChatGPT session, because it gives the user reusable structure across multiple drills instead of one disposable answer.
- The scope is finally close to solo-dev reality if you cut ruthlessly and do not pretend the content library is trivial.
Brutal Feedback:
- Your differentiator is still `UNVERIFIED`. "Faster reps, better structure checks, reusable story bank" sounds nice until the user realizes they can already record themselves, paste the transcript into ChatGPT, and ask for STAR gaps for free.
- The product is still dangerously close to a thin wrapper around transcription plus prompting. The nicer you describe it, the more obvious the commoditization problem becomes.
- You keep acting like "objective checks" are easy. They are not. Detecting missing STAR pieces, weak proof, vague actions, and overlong answers in a way users trust is exactly where AI products become embarrassing.
- The Story Card is useful, but it is also manual homework. After one or two sessions, a lot of users will think, "Why am I filling out this form instead of just practicing?"
- The retention story is improved, but still weak. Why does the user come back after day 1? Because they have another interview soon. That is not habit. That is temporary panic.
- This is still an episodic business with weak natural recurrence. Good luck making subscription math work when the honest use case is "help me not choke this month, then goodbye."
- The content work is still being minimized. Twenty-five questions across eight themes sounds compact until you remember each theme needs strong follow-up probes, scoring heuristics, examples of good versus bad evidence, and enough variation not to feel canned.
- "Local-first privacy" is not a real trust moat if raw audio still leaves the device for transcription. Users will not read the architecture diagram. They will ask one blunt question: "Are you sending my interview practice to someone else's servers?"
- The build estimate is soft-headed. Recorder UX, transcript correction, analysis latency, card editing, follow-up drills, retry comparison, and delete-all data controls are individually small, but together they are a pile of edge cases.
- The hardest part is not shipping the app. The hardest part is making the feedback feel strict, useful, and non-generic on the tenth answer. That is product quality work, not coding speed.
- There is still no moat. At best you have a focused workflow, decent UX, and maybe better trust language. Those are advantages, not defenses.
- Your own writeup quietly admits recurring retention is false and the moat is unproven. That honesty is good, but it also means this is not a business yet. It is a testable tool.
Key Questions:
- Why does the user come back after day 1 when there is no upcoming interview this week?
- What is the one sentence answer to: "Why not just use ChatGPT and a notes app?"
- Which part of the experience is truly better because of product design rather than because you wrote a nicer prompt?
- How many Story Cards will users actually complete before the workflow starts feeling like admin work?
- What is the minimum curated content set that feels complete instead of embarrassingly thin?
- What exact transcription setup are you using, and does it weaken the privacy promise enough to hurt conversion?
- If this is a one-time sprint product, can you acquire users cheaply enough for the economics not to be awful?
Suggestions:
- Cut v1 harder: 12 questions, 4 themes, typed answers plus optional recording, and one brutally clear diagnosis screen. Earn complexity later.
- Make Story Cards mostly auto-generated from the transcript, then ask the user to fix only the missing or suspicious fields. Do not make them do clerical labor for your product.
- Treat follow-up drills as the real product and rewrites as a side feature. Rewrites are easy to copy; targeted pressure-testing is harder to fake.
- Stop pretending retention is a subscription story. Design and price it like an interview sprint from day one.
- Validate the wedge with ugly manual ops before polishing UI. If users do not say "this catches weaknesses ChatGPT missed," the rest is decoration.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — one person can ship an MVP if they slash scope, but a version that feels credible instead of generic will be tight and content-heavy.
- Biggest solo complexity traps: trustworthy STAR-gap detection, transcript cleanup UX, content authoring for probes and scoring rules, latency across transcription plus analysis, privacy messaging that survives scrutiny, and turning Story Cards into help instead of homework.
Design Handoff:
- Screens needed: landing page, sprint setup, dashboard/story bank, drill screen, processing state, diagnosis screen, Story Card editor, follow-up drill view, retry comparison, session history, privacy/data controls.
- Key UI interactions: start a sprint, record or type an answer, review transcript, inspect strict feedback, confirm or edit the Story Card, launch follow-up probes, retry the same story, compare takes, delete a session or wipe all data.
- Data each screen needs: interview date, role context, theme/question metadata, transcript text, analysis results by STAR field, proof gaps, follow-up probe list, Story Card fields, attempt history, comparison metrics, local data/deletion state.
