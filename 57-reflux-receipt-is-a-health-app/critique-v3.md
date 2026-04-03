Verdict: WORTH BUILDING
Score: 7/10
What's Actually Good:
- This is the first version that stops cosplaying as medical AI and starts acting like a small, honest utility.
- The 7-night protocol is a real scope cut, not fake simplification. A solo developer can plausibly ship this if they keep it local-first and boring.
- Gating conclusions until enough nights are logged is the right call. At least now the app admits that thin data is thin data.
- The final report is a better product shape than an endless "coach." A finite deliverable is more believable than habit-app theater.
Brutal Feedback:
- You did not solve retention. You downgraded the problem from "daily habit app" to "one-week utility," which is smarter, but it still means the business is UNVERIFIED.
- Seven nights still asks for two behaviors at two different times of day. That is compliance tax, not convenience. People with reflux already procrastinate on behavior change; now you want them to log it twice.
- Your "weak signal" logic is still flirting with numerology. Five to seven nights is barely enough to say anything clean when portion size, stress, medication, meal composition, and bedtime timing all move together.
- The tiny trigger taxonomy is good for scope and bad for truth. The minute a user's obvious trigger is missing, the app looks simplistic instead of focused.
- "Useful for doctor conversations" is optimistic marketing. Most doctors are not waiting for a vibe-coded PDF unless it is extremely clean, concise, and clearly better than a patient saying "late meals seem bad."
- The product still risks feeling like premium common sense. "Eat earlier, lie down later, avoid obvious triggers" is not a breakthrough. If the report mostly confirms what users already suspect, they will not care enough to repeat the protocol.
- Safety framing is still a real product surface, not a disclaimer you sprinkle on top. If the app sounds too cautious, it feels empty. If it sounds too confident, it sounds medically shady.
- Reminders are not trivial glue work. Notification permissions, missed nights, timezone quirks, and inconsistent morning behavior can easily wreck the clean protocol experience.
- Export and PDF are classic solo-dev trap features. They sound small, but they invite polish work that does nothing to prove demand.
- The moat is still weak. This is a specialized compliance wrapper around a symptom diary, not some defensible engine.
Key Questions:
- Why does the user come back after day 1?
- What completion rate makes this a product worth continuing versus a neat one-week experiment with no business behind it?
- What exactly is in the final report that is materially better than a notes app plus a simple symptom checklist?
- If the user's real trigger is outside the fixed taxonomy, what does the app do besides shrug and say "no usable signal yet"?
- Is the primary job self-understanding, a doctor-conversation artifact, or a week-2 comparison loop? Pick one, because trying to be all three will blur the UX.
Suggestions:
- Ship v1 smaller than this writeup suggests: onboarding, protocol setup, home/progress, night log, morning check-in, and final summary. Everything else is suspect.
- Treat week-2 return as upside, not part of the core story. It is UNVERIFIED and should not carry the product thesis.
- Cut Findings Preview from v1 unless testing shows users need mid-protocol motivation. Early insight screens are an easy way to reintroduce fake-smart behavior.
- Add one escape hatch for missing triggers, such as an "other suspected trigger" note, or the taxonomy will create false negatives.
- Test completion before polish. If users do not finish 5 of 7 nights, none of the copy, export, or UI refinement matters.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — the core tracker is small enough, but only if the builder resists overbuilding reminders, export, and pseudo-insight logic.
- Biggest solo complexity traps: notification reliability, missed-night recovery logic, thin-data threshold rules, health/safety wording, App Store review risk if claims sound medical, report/export polish creep, and taxonomy expansion into a fake nutrition project.
Design Handoff:
- Screens needed: Welcome / Safety Framing, Protocol Setup, Home / Progress Tracker, Night Log, Night Logged Confirmation, Morning Check-In, Final 7-Night Report, Report Export / Share, Settings / Legal / Data.
- Key UI interactions: set reminder times during setup, log a night with aggressive defaults, complete a one-tap morning severity check, view locked versus completed protocol states, restart or duplicate the protocol for a second round, export or copy the final summary.
- Data each screen needs: Welcome needs protocol promise and safety copy; Setup needs bedtime, reminder preferences, and optional suspected triggers; Home needs current night, completion count, next required action, and reminder status; Night Log needs meal time, planned lie-down time, portion size, and up to two trigger tags; Confirmation needs saved-state and next reminder time; Morning Check-In needs symptom severity, sleep disruption, and optional note; Final Report needs completed-night counts, crossed-threshold patterns, no-signal variables, and one next-experiment suggestion; Export needs formatted summary text/PDF state; Settings needs reminder controls, export/delete actions, and legal copy.
