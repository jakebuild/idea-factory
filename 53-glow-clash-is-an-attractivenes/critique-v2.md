Verdict: NEEDS MAJOR WORK
Score: 6/10
What's Actually Good:
- You did the one smart thing v1 refused to do: you murdered the scanner gimmick before it murdered the build. That removes the dumbest technical trap.
- The wedge is finally specific. "Night-before-event irritation checker for people using strong actives" is at least a real use case instead of vague beauty-tech sludge.
- The output format is better. Explicit rules, caution levels, and fallback suggestions are more credible than fake clairvoyance.
- This is small enough to ship as a web app without needing a team, ML pipeline, or vendor circus.
Brutal Feedback:
- The product is still mostly a skinned infographic with a memory feature. The core insight is "do not stack harsh actives before an important day." That is not product magic. That is beginner skincare advice with a progress bar.
- Your moat is weak to nonexistent. Anyone with a decent prompt and a weekend can clone this rules engine. If the main value is the rules table plus a log, there is no defensibility and barely any novelty.
- The retention story is still doing wishful thinking cosplay. "Personal sensitivity history" sounds nice, but it depends on users caring enough to log the next morning after a stressful event. Most people will forget, get busy, or not want to think about their skin again. Why does the user come back after day 1?
- UNVERIFIED: the entire week-2 value depends on repeated next-day logging being high enough to produce useful patterns. Until that behavior is proven, this cannot justify a premium score.
- The taxonomy problem did not disappear. You replaced product-data hell with categorization friction. Real users do not think in neat buckets like "leave-on exfoliant" versus "prescription acne treatment" when one product can be both, or when packaging language is muddy. If users cannot confidently map their routine into your categories, the app feels dumb immediately.
- The rules are not "ready." They require meaningful manual work: selecting safe conflict rules, writing explanations, deciding severity thresholds, handling ambiguous combinations, and QA-ing edge cases so the app does not sound reckless or useless. That is not a two-hour setup task; it is a content design job disguised as logic.
- You are still operating in a trust-sensitive category where being slightly wrong is enough to wreck credibility. Even with softer wording, a bad warning can make the app look alarmist, and a missed warning can make it look incompetent.
- The personalization is weaker than you think. A handful of self-reported logs is not robust pattern detection; it is tiny, noisy anecdote storage. Dressing that up as "your personal sensitivity record" risks sounding smarter than the data deserves.
- "Event type" may be fake depth. A date, wedding, meeting, or photo day does not change skin chemistry. It only changes user anxiety. If the event field does not materially change recommendations, it is fluff in a lab coat.
- Saveable templates are useful, but they may cannibalize usage instead of increasing it. If users find one safe pre-event routine, they stop needing the checker and just reuse the template.
- The build estimate is optimistic because it treats rule writing, safety copy, taxonomy clarity, and UX onboarding as small tasks. They are not. This app lives or dies on those details because the code is the easy part.
- Local auth/storage in v1 is a smell. For an MVP this small, auth can be overkill and local-only storage can make continuity worse. You have not fully decided whether this is a disposable utility or a long-term tracker, and that muddle will leak into the product.
- The positioning is still shaky. People with real skincare knowledge may find it insultingly obvious, and people without that knowledge may not trust themselves to categorize products correctly. That is a nasty middle zone.
Key Questions:
- Why does the user come back after day 1?
- What exact taxonomy will users choose from, and how will you handle products that fit multiple categories?
- Which rules make v1, and who decided they are useful enough without drifting into medical nonsense?
- How many completed next-day logs does a user need before the "pattern history" becomes more than decorative noise?
- If a user already has one safe pre-event routine, what ongoing job does the app still do for them?
- Is this a reusable planner, a one-off checker, or a lightweight skin diary? Right now it is trying to be all three.
Suggestions:
- Shrink the promise further. Make this a blunt pre-event irritation checker first, and treat pattern history as an experiment, not the moat.
- Reduce the taxonomy to brutally obvious labels with examples from real routines, or the manual input step will kill trust before the rules even run.
- Cut event-type complexity unless it changes output. "Importance of avoiding irritation" is enough; fake personalization is worse than none.
- Treat the rules and explanations as the real product. Spend more time validating clarity and trust than adding features.
- Test the MVP with plain manual logging and no auth before building template systems, settings pages, or extra flows that pretend retention is solved.
- If retention stays weak, reposition it as a one-session utility and stop lying to yourself that the diary is the business.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — the software is buildable, but the real work is rule design, taxonomy clarity, safety copy, and validation that users will log enough data for the app to matter twice.
- Biggest solo complexity traps: turning messy real-world products into simple categories without confusing users, writing conflict rules that are helpful but not reckless, overestimating next-day logging compliance, mistaking anecdotal self-reports for meaningful personalization, adding too many screens before proving the core checker is useful, and spending time on auth/templates/history before validating that anyone needs more than a one-time warning.
