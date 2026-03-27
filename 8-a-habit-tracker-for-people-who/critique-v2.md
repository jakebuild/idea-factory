Verdict: NEEDS MAJOR WORK
Score: 6/10

What's Actually Good:
- The streak elimination mechanic is concrete and explainable — this is a real product decision, not just vibes. "Consistency window" is a specific, implementable differentiator.
- The acquisition theory is sharp: intercept users at the moment of streak-break shame, not while browsing. That's a real insight about user psychology and timing.
- One-time $4.99 pricing is a genuine positioning statement. In a sea of $3.99/month guilt subscriptions, this signals alignment with the user. Smart.
- Widget-first is the correct interaction model for a low-friction habit product. This is the right design instinct.
- The hard cap of 1-5 habits shows genuine product restraint. Most apps fail because they become productivity todo lists.
- The Sunday soft-reset framing — asking "what felt hard?" instead of showing failure — is the first genuinely novel UX idea in this space that I've seen articulated this clearly.

Why It Might Fail:
- **The differentiator already exists as a setting.** Streaks are optional in Habitica, Streaks (ironically), Finch, Done, and Bearable. The consistency window framing is better-marketed than any competitor's implementation, but "we built a whole app around what others made a toggle" is a thin moat. Power users will find the setting in their current app before switching.
- **Streaks are annoying AND they work.** Duolingo has the most-studied streak system in mobile apps and it demonstrably drives retention. The people who hate streaks and quit are a subset; the people who hate streaks but stay because of them are a larger, invisible group. This app is betting that streak-haters are a big enough market. That bet is unvalidated.
- **The retention mechanism is one message a week.** A Sunday summary is not an engagement driver — it's a newsletter. The app explicitly designs for users who "don't think about it much." That's a beautiful philosophy and a terrible business. Low engagement means low word-of-mouth, no viral loop, no upsell, no renewal reason, and users who churn when life gets busy and never miss the app.
- **$4.99 one-time is a dead-end business model.** No recurring revenue means the business is permanently on a treadmill of new customer acquisition. There's no float, no compounding revenue, no ability to fund ongoing development or marketing. At $4.99, you need ~20,000 sales to clear six figures gross — before Apple's 30% cut, before any dev costs. This is a side project revenue model, not a business model.
- **The target user is defined by what they hate, not what they want.** "People who hate habit trackers" is not a psychographic you can build loyalty around. They don't self-identify as that. They don't have subreddits. They're not a community. They're just people who quit Habitica. Once they've used Unstreak for 90 days and built their habits, the app's job is done — and they have no reason to stay, evangelize, or pay again.
- **The acquisition channel is borrowed trust.** App Store SEO for "I broke my streak" is a wishful reading of how search works. People don't search App Store when they're rage-quitting — they delete the app and go to Twitter to complain. The search terms proposed are web/Reddit terms, not App Store terms. This whole acquisition theory needs validation before a single line of code is written.
- **iOS widget limitations will bite you.** Widgets are not interactive in iOS — you can display state but you can't tap to log a habit from the widget without deep linking into the app. The primary interaction model (glancing at the widget) works; the implied "log from widget" does not. The widget can show green/yellow but can't replace the app.
- **The name "Unstreak" requires explanation.** Any name with "un-" as a prefix is trying to define itself by what it's not. You're entering a market where you have 3 seconds of App Store copy to make a case. "Unstreak" will make people think it's a skincare app before they read the tagline.
- **Success is defined as user invisibility.** "They don't think about the app much" is the success metric. This is genuinely user-centric and also genuinely catastrophic for virality. Happy, invisible users do not leave App Store reviews, do not tweet about the app, and do not recommend it to friends. The product is optimized for an outcome that makes it impossible to grow.

Brutal Questions To Answer First:
- What is the actual App Store search volume for "no streaks habit tracker" — not estimated, actually measured? Apple Search Ads lets you test keyword volume before building anything.
- Name 5 people who deleted a habit tracker because of streak shame and would now pay $4.99 for a new one. Not hypothetical users — real humans who will give you their credit card. If you can't find 5, you don't have a market.
- Streaks are already optional in at least 4 major habit apps. Why would someone switch apps instead of turning off the streak counter in the one they're already using?
- What happens to the business after year 1? If you sell 5,000 copies at $4.99 (~$17,000 after Apple's cut), what does year 2 look like with zero recurring revenue and ongoing iOS maintenance costs?
- If users "don't think about the app much" after 3 months, what keeps the 3-month retention high enough to matter? What's the actual expected retention curve?
- The Sunday summary is the core engagement mechanic. What is the open rate? What percentage of users will have notifications off by week 4? Have you modeled what the product looks like when no one reads the summary?
- Is this a habit-building product or a habit-maintenance product? These are different things with different user journeys, different success metrics, and different marketing hooks. This idea conflates them.

Suggestions If You Proceed:
- Validate acquisition before building. Run $200 in Apple Search Ads on "habit tracker no streaks" and "I broke my streak" before writing a line of code. If the CPI is under $2 on a $4.99 app, you have a channel. If it's $8, you don't have a business.
- Rethink the pricing model. $4.99 one-time is a statement but a bad business. Consider $1.99 one-time to lower the purchase barrier (more installs, more reviews), or a "pay once, use forever" + optional tip jar, or — if you're serious about the product — a $1.49/month subscription with "cancel any time, no guilt" as a positioning line that reinforces the brand.
- The "what felt hard?" Sunday prompt is the most novel thing here. Build the entire product around that interaction, not the widget. The widget is nice but table stakes. A weekly behavioral check-in that adapts targets is genuinely differentiated.
- Test the name. Five-second test on UsabilityHub with 20 people. If more than 30% guess wrong about what the app does, you have the wrong name.
- Do one customer interview this week. Find someone on r/productivity who posted "I keep failing my habit tracker" and DM them. Ask what they tried, what broke it, what they wished existed. One real conversation is worth more than this entire document.
- If you build this, launch on Product Hunt with the specific framing: "The habit tracker for people who've given up on habit trackers." That framing will generate upvotes from the exact demographic you're targeting, and PH traffic is free.
- Consider whether "Roughly" or "On Track" is a better name. Both communicate the core mechanic (approximate consistency) without requiring explanation. "Unstreak" is defensive; "Roughly" is confident.
