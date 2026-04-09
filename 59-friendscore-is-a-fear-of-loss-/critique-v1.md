Verdict: NEEDS MAJOR WORK
Score: 4/10
What's Actually Good:
- The hook is instantly understandable. "Your close friends are drifting and you can see it" is emotionally sharp.
- A barebones MVP is technically buildable if you keep it manual: friends list, interaction log, decay logic, and reminders.
- The idea has a real pain point underneath it because adult friendships do drift and people feel guilty about it.
Brutal Feedback:
- This is friendship anxiety as a product. You are not solving a problem so much as industrializing guilt and hoping people confuse emotional discomfort with value.
- "Friendship health score" is fake-science nonsense unless it is backed by data the user trusts. Right now it is just a spreadsheet pretending to understand human relationships.
- The whole pitch relies on a dramatic reveal, but after the first "oh shit" moment the user is left with homework. Manual logging is where consumer apps go to die.
- Dex works because professional relationships have obvious ROI. FriendScore asks people to do CRM for their private life with no paycheck, no pipeline, and no concrete reward beyond feeling less like a bad friend.
- The retention loop is weak. Why does the user come back after day 1? Because a decaying number is silently accusing them in the background? That is not a habit loop. That is a nag loop.
- The score will feel wrong constantly. Some friendships are low-frequency but strong. Some friends text memes daily and still are not close. Your metric will misread both and look stupid.
- The "likes are not real connection" angle sounds clever for ten seconds and then collapses. That is copy, not moat.
- If the product stays manual, the data becomes incomplete almost immediately because users forget to log. If you try to automate it, you run straight into contacts permissions, call/message metadata, platform limitations, privacy concerns, and likely brittle integrations. That is UNVERIFIED.
- This is also socially awkward as hell. A lot of normal people will bounce the second they realize the app wants to quantify friendship like a Duolingo streak.
- The product risks making users feel worse without actually helping them behave differently. Anxiety spikes can drive installs; they do not guarantee retention.
- Weekly drift alerts only work if the follow-through is frictionless and obviously useful. Otherwise you built a recurring shame notification.
- Virality is overstated. Most people do not want to screen-record evidence that they have neglected their friends.
- The market is muddled. People who are organized enough to use this probably already have lightweight systems. People who most need it are the least likely to maintain it.
Key Questions:
- What is the real source of truth for interaction data: fully manual logs or automated imports? If imports matter, which platform support is actually available?
- Why does the user come back after day 1 once the initial guilt spike fades?
- Who is the first narrow segment that will tolerate this behavior consistently for weeks?
- What concrete action does the product make easier besides "send a text"?
- How do you stop the score from feeling arbitrary, insulting, or emotionally manipulative?
- What evidence says people want friendship quantified instead of simply reminded?
Suggestions:
- Drop "friendship health score" and reframe it as drift tracking or reconnection reminders. Be honest about what the app knows.
- Pick one niche with clear pain and recurring distance, like long-distance close friends or people who moved cities recently.
- Reduce the MVP to one weekly workflow: see 3 people drifting, pick one, send a message, log it in one tap.
- Make logging brutally simple and accept that anything more than a tap or two will kill usage.
- Replace fake precision with interpretable signals like "you usually talk every 2-3 weeks; it has been 5."
- Validate retention with a manual prototype before touching any integrations, background syncing, or AI-generated friendship insights.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — the stripped-down manual version is feasible, but the version that feels magical depends on behavior design and data quality problems that AI coding does not solve.
- Biggest solo complexity traps: making the score feel credible; keeping manual logging low-friction; building reminder timing that is useful instead of annoying; handling contacts import and deduping; dealing with privacy expectations around personal relationships; avoiding churn after the first week; resisting scope creep into call logs, SMS, social integrations, and cross-device sync.
