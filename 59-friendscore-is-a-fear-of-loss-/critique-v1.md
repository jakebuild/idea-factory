Verdict: NEEDS MAJOR WORK
Score: 4/10
What's Actually Good:
- The hook is instantly legible: your important friendships are fading, and the app shows you which ones need attention.
- The first reveal has emotional punch. Seeing that a supposedly close friend has gone 52 days without a real conversation can provoke action.
- A brutally narrow MVP is technically feasible: a friend list, one-tap interaction logging, deterministic decay, and weekly reminders.
- Adult friendship drift is a real pain, especially for recent movers, long-distance friends, and busy people whose relationships decay through neglect rather than conflict.
Brutal Feedback:
- This is guilt-as-a-service. The product turns friendship into a red-number maintenance queue and then calls the resulting anxiety "health."
- The core metric is fake precision. A 37% friendship score looks scientific while encoding little more than an arbitrary timer chosen by the developer.
- The app confuses "relationship maintenance" with "relationship quality." It can measure elapsed time since a logged event; it cannot measure trust, affection, reciprocity, conflict, or whether either person actually enjoyed the interaction.
- Frequency is not intimacy. Some best friends talk every two months and remain close; some people message daily and barely know each other. The flagship score fails on ordinary relationships.
- Dex works because professional follow-up has legible ROI: deals, referrals, hiring, and career leverage. "CRM for people you love" risks making friendship feel transactional without offering comparable payoff.
- The instant reveal is a demo, not durable value. It works once. Then the app becomes another chore whose job is to remind users they are failing at a more important chore.
- The proposed recovery animation rewards data entry, not friendship. Users can make the scary number turn green by logging something, which makes the product trivially gameable and severs the metric from the claimed real-world outcome.
- Manual logging is lethal. The product only knows what the user remembers to enter, so forgotten calls make the app confidently wrong. The people most likely to need help maintaining friendships are least likely to maintain pristine CRM data.
- Passive logging is not a credible escape hatch. Reliable call, SMS, WhatsApp, Instagram, FaceTime, and in-person interaction data is unavailable through one clean cross-platform interface. Any automation-dependent version is UNVERIFIED and would be capped below 8/10 regardless.
- Contact import is not the hard part. Inferring a "real back-and-forth" from fragmented, privacy-sensitive platform data is the hard part, and it is far beyond a sensible 2-4 week solo MVP.
- The score can destroy trust in seconds. If it labels a lifelong friend unhealthy after a meaningful weekend together that was not logged, users will blame the app, not their memory.
- Weekly drift alerts are not automatically retention. Why does the user come back after day 1? "Because the number keeps falling" is coercion, not value; many users will disable notifications to stop feeling judged.
- The product's success metric is perversely murky. More app opens and more logs do not prove better friendships. If the product genuinely works, users should spend less time in it.
- The pitch assumes fear produces sustained behavior. Fear produces an initial click; repeated guilt often produces avoidance, notification muting, and churn.
- The social-sharing claim is wishful thinking. A screen recording showing neglected friends is embarrassing, potentially exposes names and relationship data, and is unlikely to become a healthy acquisition loop.
- There is no moat. The MVP is a contact list plus timers and reminders. Apple Reminders, a calendar recurrence, a Notion table, or a tiny Dex configuration can reproduce most of it.
- Privacy stakes are disproportionate to the product's value. A database of closest friends, interaction dates, notes, and relationship status is deeply personal data attached to a low-utility reminder app.
- Scoring invites obsessive behavior and emotional harm. Anxious users may optimize the meter, contact people out of obligation, or interpret decay as evidence that they are unloved.
- The product ignores asymmetry. A user can log outreach and inflate a score even when the other person never reciprocates, unless the model becomes much more invasive and complex.
- Monetization is ugly. Charging people to learn which friendships are "dying" feels exploitative, while a free utility with reminders has weak revenue potential.
- The likely audience splits badly: organized people can already maintain relationships without it, while disorganized people will not keep the data current enough for it to work.
Key Questions:
- Why does the user come back after day 1 once the shock of the initial score is gone?
- What concrete outcome does FriendScore deliver that a recurring reminder for five names does not?
- Is v1 explicitly manual-only? If not, which exact API, permission, and platform supplies reliable interaction data? Treat every assumed integration as UNVERIFIED until proven with a working prototype.
- How is each friend's expected cadence calibrated without forcing tedious setup or producing nonsense defaults?
- What happens when the user reaches out and the friend does not respond? Is the relationship "healthier" because the user logged an attempt?
- Who is the narrow first customer willing to track friendships for four consecutive weeks, and what evidence says they will pay?
- How will the app avoid worsening anxiety, compulsive checking, or guilt-driven outreach?
- What durable advantage exists beyond branding and a decay formula that competitors can copy in a weekend?
Suggestions:
- Kill the percentage score. Use honest, explainable language: "You usually talk every 3 weeks; it has been 7 weeks." Do not pretend intimacy can be measured to the decimal.
- Narrow the customer to one behaviorally coherent niche, such as recent movers maintaining 5-10 long-distance friendships. "People with friends" is not a market segment.
- Reframe the product as a weekly reconnection assistant: show at most three people, explain why each surfaced, offer one action, and allow one-tap logging or snoozing.
- Keep v1 manual-only and say so. Build around two-second logging instead of betting the product on inaccessible social and messaging data.
- Let users define a loose cadence per person and support "naturally low-frequency" relationships so the app does not punish normal variation.
- Run a no-code concierge test for four weeks before building: send 15-20 target users one weekly prompt and measure whether they actually reach out, keep logging, and ask to continue.
- Set a hard validation bar before coding: at least half of testers must still log interactions and act on prompts in week four, and several must volunteer to pay. Anything weaker means the retention loop is mostly notification guilt.
- Validate willingness to return and act before adding accounts, imports, AI summaries, notes, streaks, gamification, or cross-platform sync.
- Add humane controls: pause a friendship, mark life circumstances, hide scores, snooze prompts, and delete all data. If these controls feel like too much scope, that is evidence the score mechanic is the wrong product.
- Accept the likely ceiling: if the test works, this is probably a focused paid utility or a feature inside an existing personal CRM, not a venture-scale company.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — one person can ship the manual-only utility, but not the implied smart system that reliably detects meaningful interactions across calls, messaging apps, social networks, and real life. The technically easy build still faces the much harder problem of making arbitrary relationship judgments feel trustworthy and useful.
- Biggest solo complexity traps: cross-platform notification reliability; contact permission and import edge cases; privacy and secure storage; timezone-aware decay and reminder scheduling; score calibration; recurring billing; data export/deletion; preventing manual logging drop-off; handling unanswered outreach and unusual relationship cadences; resisting UNVERIFIED call, SMS, WhatsApp, Instagram, or location integrations; and spending weeks polishing a formula before proving anyone returns for week four.
