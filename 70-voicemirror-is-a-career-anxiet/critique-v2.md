Verdict: WORTH BUILDING
Score: 6/10
What's Actually Good:
- This is finally a product instead of a sci-fi demo. Narrowing to interview rehearsal removes the dumbest part of v1 and gives the app a concrete job.
- The workflow is legible: pick interview type, answer a question, get specific feedback, retry. That is simple enough to explain, test, and ship.
- Cutting cloned voice from MVP was the right call. Text-first coaching plus retry loops is dramatically more credible for a solo builder.
- The authenticity control is smart because interview prep dies the second users think the app is putting fake words in their mouth.
Brutal Feedback:
- You improved the idea, but you are still overselling how useful the output will feel. "Cleaned-up rewrite plus delivery flags" sounds helpful until the user realizes ChatGPT can already do 70% of this with a transcript pasted into a prompt.
- Your moat is weak. Curated interview packs, filler-word counts, and transcript diffs are not defensible. They are starter features, not a business.
- The biggest risk is still trust, not tech. If the rewrite sounds generic, over-coached, or slightly dishonest, the whole product starts to feel like a bullshitting machine for insecure candidates.
- "Meaning-safe rewrite" is nice copy, not a guarantee. LLMs are slippery. In interviews, subtle wording changes can turn a truthful but messy answer into a cleaner answer the user cannot actually defend live.
- Your retention story is better than v1, but it is still mostly a countdown wrapper around a practice tool. Why does the user come back after day 1? Because they have an interview soon. That is usage, not love. The product is episodic by nature, which makes recurring revenue harder than your pitch admits.
- The 7-day plan is in danger of being fake structure. If it is just "practice weak questions again," users will see through it in one session. A countdown is not coaching.
- The content problem is understated. Good interview questions are easy. Good follow-up prompts, scoring logic, rewrite heuristics, and scenario metadata are the actual product, and that is manual labor. Your "small curated pack" can easily become a week of mediocre content that still feels thin.
- Delivery feedback is a trap. Filler words and answer length are easy. "Weak opening," "rambling," and "confidence markers" sound useful but are subjective enough to feel random unless you tune them carefully. Sloppy feedback will destroy trust faster than no feedback.
- Privacy is still not solved. Job seekers are not just practicing STAR answers. They are rehearsing layoffs, compensation, gaps, immigration status, performance problems, and messy career stories. "Short retention defaults" is not a privacy strategy. It is a line item.
- Pricing is awkward. This is probably not a subscription, but one-off prep products are harder to scale and easier to churn from. If the user has one interview cycle and leaves, that may still be fine, but then acquisition efficiency matters a lot.
- You are assuming users want rewrites. A meaningful chunk may only want diagnosis: "tell me what is weak and let me rewrite it myself." If that turns out true, your core UX is pointed at the wrong behavior.
- The solo build estimate is optimistic in the sneaky way solo estimates always are. The UI is easy. The hard part is making the feedback feel trustworthy enough that users do not roll their eyes after take two.
Key Questions:
- Why does the user come back after day 1 beyond "the interview has not happened yet"?
- What does this do that a transcript pasted into ChatGPT plus a voice memo app does not do well enough for free?
- Which single wedge is strongest at launch: behavioral interviews, recruiter screens, or salary conversations? Pick one. Four is already scope creep.
- How will you prove that rewrites preserve meaning instead of merely sounding more polished?
- What is the minimum amount of curated content required before the app stops feeling like an empty shell with three canned prompts?
- Is the first paid offer a one-time interview prep pass, a weekly sprint, or something else? If you do not know, your monetization story is still hand-waving.
Suggestions:
- Narrow further. Start with behavioral interviews only. That is where rambling, weak structure, and generic answers are most common, and where a rewrite-plus-retry loop is easiest to justify.
- Consider a stricter MVP where the default is critique-first, not rewrite-first: diagnose the answer, show a stronger structure, then let users opt into a full rewrite.
- Treat every subjective feedback label as guilty until proven useful. If a metric cannot be explained in one sentence and demonstrated with an obvious example, cut it.
- Make the product win on speed and repetition, not AI mystique. The value should be "ten solid practice reps in twenty minutes," not "behold the magic rewrite."
- Price for the episodic job honestly. A focused interview sprint or pack is more believable than pretending this is a forever habit product.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — the shell can be shipped, but a version that feels trustworthy instead of generic depends on tighter scope, manual content work, and aggressive cutting of fake-smart feedback.
- Biggest solo complexity traps: polishing recording/transcription UX, building credible rewrite prompts, hand-curating question packs and metadata, avoiding obviously wrong feedback labels, handling sensitive audio/text privacy cleanly, and resisting the urge to add more interview types before one works.
Design Handoff:
- Screens needed: landing page, onboarding/interview setup, question-pack picker, dashboard/prep plan, practice recorder, processing/loading state, answer review, rewrite/diff view, retry comparison, session history, privacy/data controls, empty states for no sessions and no weak questions.
- Key UI interactions: choose interview type and date, start a drill from the dashboard, record or type an answer, review transcript and flags, switch authenticity mode, inspect diff, retry the same question, compare takes, save weak questions into the plan, delete a session or recording.
- Data each screen needs: interview date, selected interview type, curated question pack metadata, current question, transcript text, rewrite mode, rewrite output, feedback flags with explanations, take history, weak-question tags, daily drill list, session timestamps, retention/privacy settings, deletion state.
