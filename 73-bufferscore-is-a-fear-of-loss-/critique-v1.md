Verdict: NEEDS MAJOR WORK
Score: 4/10
What's Actually Good:
- The core hook is instantly legible. "Credit score for layoff risk" is a strong one-line pitch.
- The first-run experience is simple enough for a solo dev to ship: collect a few inputs, calculate runway, show a harsh score.
- The screen-recordable reveal has decent viral potential because people love sharing flattering scores and doomposting bad ones.
Brutal Feedback:
- This is mostly a dressed-up emergency fund calculator. The "Layoff Buffer Score" sounds novel until you strip away the branding and realize it is savings divided by burn with a vague confidence penalty.
- Your core promise breaks the moment the user asks, "Why should I trust this number?" Credit Karma piggybacks on a real system with standardized underlying data. You have no equivalent. You are inventing a fake authority layer over messy personal finance and labor-market uncertainty.
- "Interview readiness" is hand-wavy nonsense unless you operationalize it. Self-reported readiness is vanity input. People who are delusional will rate themselves highly, and desperate users will underrate themselves. Either way, your model becomes mood tracking, not risk scoring.
- "Role" and "industry" are not enough to estimate rehire odds in any defensible way. Seniority, geography, compensation band, visa status, network strength, niche stack, portfolio quality, and hiring cycle matter. If you ignore those, the score is shallow. If you include them, your onboarding gets bloated and ugly.
- The phrase "market conditions" is doing criminal amounts of work here. Where does that data come from? If the differentiator depends on live labor-market inputs, that dependency is UNVERIFIED. No verified data source means no credible moat and no 8/10 fantasy score.
- The retention story is weak. Why does the user come back after day 1? Their savings do not meaningfully change every week. Their burn might not change either. Most users will check once, feel attacked, maybe share it, then leave.
- Fear-based products are easy to try and easy to resent. If the app keeps telling people they are screwed without giving them a concrete path to improvement, it becomes emotional junk food: intense first bite, no durable habit.
- The product risks being too soft to be trusted and too harsh to be liked. If the score is blunt, users may reject it as doom bait. If you soften it, the app loses its edge and becomes generic budgeting fluff.
- There is no obvious monetization that does not feel gross. Subscription for anxiety? Affiliate links to high-yield savings accounts? Resume coaches? Insurance-style upsells? All of that starts smelling opportunistic fast.
- The comparison to Credit Karma is more damaging than helpful. Credit Karma works because the score already exists in the world and lenders care about it. Your score is invented by the app itself. That makes this closer to a content gimmick than a trusted system.
- If you plan to personalize advice, you now need credible recommendations across budgeting, job search, interview prep, and labor-market intel. That is four products pretending to be one.
- If you do not personalize advice, the product is basically: "You have 2.3 months of runway, spend less and job hunt harder." Congratulations, you built a calculator with emotional branding.
Key Questions:
- Why does the user come back after day 1?
- What exact inputs make the score feel credible rather than arbitrary?
- What verified source, if any, powers the "market conditions" or rehire-odds layer?
- What action does the user take immediately after seeing a bad score besides feeling worse?
- What makes this better than a spreadsheet, a budgeting app, or a ChatGPT prompt that estimates runway for free?
- Are you building a diagnostic toy, a habit product, or a lead-gen funnel for financial/job services? Right now it is awkwardly between all three.
Suggestions:
- Cut the fake precision. Do not pretend to predict layoff odds or rehire probability unless you have validated data. Start with a brutally honest "runway + readiness" calculator and label uncertainty clearly.
- Replace the single score with 2-3 sub-scores the user can actually influence: cash runway, job-search readiness, income resilience. That reduces the "made-up magic number" problem.
- Add a concrete action loop or this dies instantly. Example: one weekly checkpoint with one recommended action, such as cut one recurring expense, finish one resume improvement, or complete one mock interview.
- Narrow the audience hard. "Knowledge workers worried about layoffs" is lazy. Pick one segment like mid-career tech employees in expensive cities. Broad anxiety products usually become vague products.
- Treat live market data as optional until verified. Ship v1 with user-input-only logic and honest framing instead of building around an external data fantasy you may not be able to source cheaply or legally.
- Test demand with a landing page and fake-door score reveal before building a full app. If people will not give you their numbers for the promise alone, the product is weaker than the copy.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — a narrow v1 calculator with a blunt reveal, simple history, and zero real market-data integration is feasible. A credible scoring product with dynamic labor-market intelligence is not.
- Biggest solo complexity traps: inventing a scoring model that looks authoritative without becoming fraudulent nonsense; sourcing and maintaining labor-market data; overbuilding onboarding to compensate for weak prediction quality; trying to add personalized advice across finance and careers; building retention features for a product that users naturally need only occasionally.
