Verdict: NEEDS MAJOR WORK
Score: 4/10
What's Actually Good:
- The problem is real. Layoff anxiety is not fake, and people do want a fast answer to "how bad would this be if my income vanished?"
- The reveal is clean. A blunt score plus runway estimate is easy to understand, easy to screenshot, and easy to market.
- The thin version is technically shippable: a focused questionnaire, deterministic calculation, result page, and saved action plan are reasonable solo-dev scope.
Brutal Feedback:
- This is a spreadsheet in a Halloween costume. Savings minus monthly burn is not a product moat. It is eighth-grade math with scarier copy.
- The "Credit Karma for layoffs" comparison is flattering nonsense. Credit Karma works because it plugs into standardized, constantly updated, hard-to-replicate credit data. Your app asks users to hand-enter a few numbers and then pretends a homemade score has the same authority. It does not.
- "Rehire odds" is where this starts smelling fake. Based on what, exactly? The labor-market feeds, role-level benchmarks, and hiring-climate inputs are UNVERIFIED external data dependencies. Until their availability, price, coverage, update frequency, and legal usage are proven, the supposed differentiator is vapor. If the score does not use them, then "rehire odds" is decorative fiction.
- The retention loop is weak to the point of comedy. Why does the user come back after day 1? Their emergency fund does not change every Tuesday. Their fixed expenses barely move. Their interview readiness is not a daily habit. The claimed weekly drift only works if the app continuously ingests trustworthy market data—which is UNVERIFIED. Most users will run it once, feel judged, maybe share the number, and never open it again.
- The emotional promise is shaky. People do not actually want a score that makes them feel helpless unless it also gives them a believable path to improve it. Right now this risks being doom-porn for employed people with anxiety.
- You are quietly bundling multiple hard problems into one fake-simple score: personal finance, labor-market timing, career readiness, and psychology. Compressing that mess into one number does not make it smarter. It makes it more arbitrary.
- The benchmark percentile angle is also suspect. Percentile against whom? Other users? Public data? Your own invented buckets? If you do not have a real dataset, the percentile framing is theater.
- Cold start destroys the benchmark story. You have no users, no normalized cohort data, and no defensible seed dataset. Cleaning public employment data, mapping job titles, defining cohorts, and documenting assumptions is significant manual work, not "ready" content, and belongs in the estimate.
- The app is dangerously close to insulting the user with obvious advice. "Spend less, save more, update your resume" is not a product experience. It is a nagging uncle with a progress bar.
- Handling precise savings, expenses, salary, and job-risk information creates a trust and security burden wildly disproportionate to the value of the calculator. One sloppy privacy flow or analytics leak and the product looks predatory.
- Monetization is backwards. The people most motivated by layoff fear are exactly the people least eager to add another subscription, while a one-time fee matches the usage pattern but caps revenue.
- The concept is easy to clone. A solo dev can build it fast, which is nice, but that also means fifty other solo devs—or one decent spreadsheet template—can reproduce the useful part by next weekend.
Key Questions:
- What real data makes "rehire odds" or "market conditions" credible instead of made-up product cosplay?
- Why does the user come back after day 1?
- What is the one action this app gives that a spreadsheet, a calculator, or ChatGPT would not give almost as well?
- Who is this actually for: anxious general white-collar workers, tech workers, people already in layoffs, or people preparing for one? Right now it is blurry.
- Will users disclose sensitive financial details to an unknown app, and what is the minimum data you can avoid storing?
- Is this a recurring product at all, or an acquisition-friendly one-time audit pretending to be a subscription app?
Suggestions:
- Cut the fake-credit-score ambition and reposition this as a one-time layoff readiness audit with a brutally prioritized action list.
- Remove any claim that implies predictive labor-market intelligence unless you have a verified source and a reason users should trust it.
- Narrow the audience hard. "US software engineers with 3-10 years experience" is buildable. "Everyone afraid of layoffs" is mush.
- Make the outcome concrete: one page of weak points, one survival runway number, three actions to improve it this month.
- Keep v1 local-first or anonymous: no bank connection, no payroll integration, no precise employer field, and no server-side storage of raw finances.
- Use transparent labeled components instead of a magic 0-100 score: cash runway, expense flexibility, interview readiness, and evidence quality. Show exactly how each answer affects the result.
- Treat market data as a later experiment. For MVP, either omit it or label a manually maintained coarse signal as informational rather than pretending it predicts an individual's rehire probability.
- Test demand with a landing page or manual audit before building an app. This is exactly the kind of idea that sounds good in a sentence and dies on contact with real users.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — one person can ship an honest readiness calculator and action report. The proposed product, with credible rehire odds, benchmark percentiles, weekly market drift, alerts, privacy hardening, and a defensible score, is not a trustworthy 2-4 week MVP.
- Biggest solo complexity traps: sourcing and normalizing labor-market data, manually preparing seed benchmarks, inventing bogus percentiles, validating a scoring model across roles and industries, protecting sensitive financial inputs, avoiding financial or career-prediction claims, building alerts before proving retention, and polishing a magic score that still has no authority.
