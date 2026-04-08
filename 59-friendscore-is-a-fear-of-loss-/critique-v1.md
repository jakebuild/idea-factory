Verdict: NEEDS MAJOR WORK
Score: 5/10
What's Actually Good:
- The emotional hook is real. "You thought you were close, but you have not actually talked in 52 days" is a sharp, instantly understandable punch.
- The core MVP is technically simple if you keep it brutally manual: friend list, interaction log, decay function, reminders, and a dashboard.
- The screen-recordable reveal is better than most productivity sludge because it creates an immediate "oh no" reaction instead of a vague promise of self-improvement.
- Dex proved some people will tolerate relationship management software, so this is not totally invented demand.
Brutal Feedback:
- This is a guilt machine disguised as a friendship app. That can get clicks, but it can also make users feel judged, neurotic, and vaguely pathetic. Most people do not want an app that turns their personal relationships into a failing KPI.
- "Friendship health score" sounds fake unless the score is obviously grounded in something real. Right now it is just a decay mechanic wearing a lab coat. If the user knows the number is arbitrary, the magic dies instantly.
- The product asks users to do manual logging for one of the most annoying categories of software: personal CRM. Professionals endure that pain because money is attached. Friendships do not have a commission check at the end.
- The idea pretends "likes are not real connection" is a powerful insight. It is not product moat; it is a tweet. You still need users to consistently log actual calls, hangouts, and conversations or the app becomes a stale graveyard in four days.
- The retention loop is basically: come back because your friendships are getting worse. That is not a healthy loop; it is a shame loop. Shame can trigger one screenshot, maybe one week of usage, then uninstall.
- Why does the user come back after day 1? Because a number is decaying in the background? That is thin. If logging is manual, most users will ignore the app until the notifications become annoying, then mute them.
- The most compelling moment depends on accurate "real conversation" detection, but the MVP as described does not actually have that. If it relies on imports from messages, call logs, or social apps, that is UNVERIFIED and immediately more fragile than the pitch admits.
- If it stays manual, the data quality is garbage because users forget to log. If it automates, you walk into contacts permissions, communication metadata, platform restrictions, and privacy creep. Pick your poison.
- The idea also carries social risk. A lot of people will have an immediate visceral reaction to quantifying friendship because it feels manipulative, transactional, or emotionally weird.
- "Copies Dex for friendship" is not enough differentiation. Dex exists in a context where people consciously manage networks. Consumer friendship management is where good intentions go to die.
- Weekly drift alerts are only useful if the app can suggest a low-friction next action that the user will actually take. Otherwise it is just a recurring accusation.
- Virality is overstated. A screen recording that says your best friend is fading is not inherently shareable; it is more likely embarrassing than brag-worthy.
Key Questions:
- What is the non-manual source of truth for interaction data, if any? If the answer is "we'll figure out integrations later," the strongest version of the pitch is built on vapor.
- Why does the user come back after day 1 once the initial emotional sting wears off?
- Who wants this enough to log interactions consistently for weeks: anxious college students, long-distance adults, new parents, expats, therapists' patients? "Everyone with friends" is not a market.
- What specific action does the app unlock besides "text them"? If the output is just guilt plus a contact card, the product is shallow.
- How do you stop the score from feeling arbitrary, unfair, or emotionally punishing when some healthy friendships naturally go quiet for stretches?
- Is the business model subscription, one-time purchase, or something else? Consumer guilt apps are easy to try and easy to churn.
Suggestions:
- Cut the fake precision. Do not pretend to measure "friendship health." Position it as "drift risk" or "stale contact reminder" so the product is honest about what it knows.
- Pick one narrow user segment where relationship drift is a real, repeated pain. Long-distance close friends is more believable than generic friendship maintenance.
- Build the MVP around one useful weekly ritual: a ranked list of 3 people at risk of drifting plus one-tap outreach prompts and a quick log afterward.
- Start fully manual and admit it, but reduce logging friction to near zero: one tap for call, hangout, voice note, or deep chat.
- Replace punitive decay theatrics with actionable prompts. "You are a bad friend now" is lazy product design. "You usually talk every 2-3 weeks; it has been 5" is better.
- Test whether users even want a score before building score theatrics. A simple reminder dashboard may outperform the dramatic branding.
- If you cannot prove recurring use in a manual prototype, do not waste time on integrations, AI summaries, or social graph features.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — a stripped-down manual tracker, score, reminder engine, and dashboard are feasible, but the compelling version people imagine usually smuggles in integrations, background data, and smart prompts that blow the scope up fast.
- Biggest solo complexity traps: contacts import and deduping; notification timing that does not feel spammy; designing a score that feels credible instead of arbitrary; privacy messaging around personal relationships; sync/auth if users expect cross-device use; any SMS/call/social integration; seeding enough habit formation before users churn.
