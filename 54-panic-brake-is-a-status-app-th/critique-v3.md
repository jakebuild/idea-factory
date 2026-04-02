Verdict: WORTH BUILDING
Score: 7/10
What's Actually Good:
- This is the first version that sounds like a product instead of a biometric magic trick. Cutting pulse, camera nonsense, and general "high-stakes moments" scope stopped the idea from collapsing under its own bullshit.
- Interview practice is a real, narrow pain point with built-in urgency. People already rehearse answers, hate sounding vague, and will tolerate a focused workflow if it clearly sharpens responses.
- The loop is finally specific enough to matter: answer, detect missing structure, get one targeted redo drill, retake, compare. That is materially better than "here are your filler words, good luck."
- The scope discipline is much better. Web-first, 15-20 curated prompts, 5 drills, per-question history, no dashboard vanity layer. That is at least plausible for one person to ship.
Brutal Feedback:
- The whole product still lives or dies on one UNVERIFIED bet: will users trust a lightweight rules-based structure checker enough to believe the feedback is worth acting on? If the checker feels dumb even twice, the app turns into a transcript viewer with a fake coach costume.
- You keep calling the structure check "lightweight" like that is a virtue by itself. It is only a virtue if it feels reliably right. "Direct answer missing" and "outcome missing" sound useful until real answers land in the gray zone and your rules start misfiring.
- This is still a narrow situational tool, not a habit product. Why does the user come back after day 1? "Because some questions still say needs redo" is better than before, but it is still a paper-thin loop borrowed from looming interviews, not from intrinsic product pull.
- The finite question bank is both your MVP advantage and your ceiling. Fifteen to twenty prompts is manageable, but it also means the product risks being "one evening of prep" instead of software people keep around.
- Your differentiation is weak. A competent interview coach app, a GPT wrapper with voice mode, or a job-prep tool with transcript analysis can copy this shape fast. Curated prompts plus a rules engine is not a moat. It is packaging.
- "Rule-based, not fake AI oracle" is smart positioning, but users do not care about your philosophy. They care whether the feedback helps them get hired. If the app cannot connect its advice to noticeably better answers, the honesty will not save it.
- Transcript correction is necessary, but it is also friction. The moment users have to edit bad transcripts before they can trust analysis, your "quick practice loop" starts feeling like unpaid QA work.
- The content burden is understated. Writing 15-20 actually strong prompts is easy. Writing 15-20 prompts with precise expected answer bones, clean rule logic, and drills that map cleanly to common failure modes is not. That is product design work, not clerical setup.
- The scorecard still contains vanity metrics. Pace, filler words, and length are fine support signals, but they are also the easiest things to overemphasize and the least likely to tell a candidate whether the answer actually landed.
- The comparison screen can create fake progress. If a user simply shortens the answer and says the conclusion earlier, the app may celebrate improvement while the answer is still generic and forgettable.
- You are still relying on borrowed urgency from interview season. Outside an active job search, this product is dead weight. That does not kill the idea, but it does shrink the market and makes retention harder than your writeup wants to admit.
- Privacy and deletion controls are not "nice trust UX." They are table stakes once you store recorded interview answers. For a solo dev, this is exactly the kind of dull reliability work that burns the calendar while generating zero marketing value.
- The estimated timeline is optimistic in the way solo AI-built products always are. Browser audio edge cases, cross-browser recording weirdness, transcript latency, rerun logic after edits, and sensible structure heuristics will eat more time than the pretty flows.
Key Questions:
- Why does the user come back after day 1?
- What specific evidence says users will trust the UNVERIFIED rules-based structure check instead of dismissing it as shallow pattern matching?
- Which question types can actually be graded reliably with deterministic rules, and which ones become ambiguous enough to break trust?
- What happens when a genuinely good answer gets flagged for "missing specificity" because the transcript was messy or the rules are too blunt?
- Is the real product the feedback engine, or is it just a structured accountability flow for practicing out loud?
- If retention stays weak, do you have a credible one-session, night-before-interview positioning that still justifies building standalone software?
Suggestions:
- Treat this as a rehearsal tool, not a coaching oracle. Promise tighter practice and cleaner answer structure, not "better interview performance" in general.
- Start with the smallest set of question types that have obvious structural expectations, such as opener, one behavioral STAR question, and one role-fit prompt. Do not pretend all interview questions are equally gradable.
- Make the drill quality the product. If the redo drills are generic, the whole loop collapses into "record twice for no reason."
- Instrument trust aggressively in MVP. After each result, ask whether the feedback felt accurate and useful. If users do not trust the structure flags, stop polishing and rethink the core.
- Keep transcript editing optional and minimal. If the loop depends on users cleaning up transcripts every session, you built a homework app.
- Be prepared to collapse this into a premium single-session prep experience if repeat usage does not materialize quickly.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — the trimmed web-first scope is finally sane, but only if the builder is ruthless about limiting question types, skipping account complexity, and resisting any temptation to "improve" the analysis engine mid-build.
- Biggest solo complexity traps: browser recording reliability across devices; choosing and integrating a transcription provider that is fast enough and cheap enough; writing structure rules that feel useful instead of brittle; mapping weak answers to drills that actually help; transcript correction and rerun UX; privacy/deletion flows for stored audio and text; spending too long polishing metrics that are not the real value.
Design Handoff:
- Screens needed: landing/positioning, onboarding/privacy consent, question packs, question detail/prep, record answer, processing, transcript review, first-take result, redo drill, retake recording, retake comparison, per-question history, session detail, settings/data controls, delete-confirmation state
- Key UI interactions: start from a pack and choose a question; review what a good answer must contain before recording; record and restart within a fixed time target; wait through processing; optionally correct transcript and rerun analysis; view structure flags and assigned drill; complete the drill and retake; compare take 1 versus take 2; save the session as solid or needs redo; revisit redo-marked questions; delete a session or all stored data
- Data each screen needs: positioning copy and CTA state; privacy consent and microphone permission state; pack list with per-pack progress and question statuses; question prompt, question type, expected answer bones, prior attempt count, latest status; active recording state, timer, audio level, and error state; transcription/analysis job state and failure state; transcript text plus edit state and rerun status; metrics for pace, filler words, answer length, structure flags, and assigned drill; drill instructions and completion state; side-by-side take metrics, transcript deltas, and cleared versus unresolved flags; per-question attempt history with timestamps and latest verdict; full session record for both takes; retention preferences and delete actions.
