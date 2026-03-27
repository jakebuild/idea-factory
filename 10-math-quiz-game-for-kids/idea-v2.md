# Idea #10: Math Quiz Game for Kids (Improved)

## Version: v2
**Date:** 2026-03-20 20:15
**Status:** improved

## Original Idea
math quiz game for kids

## What Changed and Why

- **Brutal niche specificity: multiplication tables for kids ages 7–10 who are behind grade level** — The critique nailed it: "math quiz game for kids" is a category, not a product. The App Store has thousands of these. The niche that's actually underserved isn't the top performers or casual learners — it's the kid who just failed their times table test and whose parent is now panicking. That parent has an acute, urgent problem and will pay to solve it. Prodigy and Khan Academy serve the broad market; this serves the crisis moment.

- **One unique mechanic: AI-generated word problems using the child's own interests** — Every existing app uses generic problems ("7 × 8 = ?"). The insight from the critique is that engagement falls off a cliff without a hook. If the app asks the parent during onboarding "what does your child love?" (dinosaurs, Minecraft, soccer) and then generates "A Creeper drops 7 bombs 8 times. How many bombs total?" — you have personalization no off-the-shelf quiz app offers, and it's achievable with a simple Claude API call per problem.

- **B2B2C go-to-market through tutoring centers and after-school programs** — The critique correctly identified that winning consumer App Store in kids' edtech is a war zone. Instead, the first 100 users come from 5–10 tutoring centers who pay a flat monthly fee (~$30–50/month) for unlimited student seats. This sidesteps COPPA's individual-child data problems (the business is the customer, not the child), creates a recurring revenue model, and gives instant credibility signals parents trust ("recommended by my tutoring center").

- **COPPA-safe monetization from the start** — No behavioral ads, no in-app purchases. The model is: free for individual parents (limited problems/day), paid subscription for tutoring centers/after-school programs ($30–50/month flat), and optional one-time parent purchase ($4.99) to unlock unlimited home use. All data collection scoped to business customers, not minors directly.

- **Retention via streaks + parent visibility dashboard** — Kids abandon quiz apps in days without a loop. The fix is twofold: a simple daily streak mechanic for the child, and a weekly SMS/email digest to the parent showing progress ("Jake got 85% on 7× this week, up from 60%"). The parent becomes the retention agent — they remind the kid to practice because they can see the data. This also gives parents the credibility signal they need to trust the app.

## Improved Description

**Times Table Rescue** — an AI-powered multiplication drill app for kids ages 7–10 who are struggling with their times tables, distributed through tutoring centers and after-school programs.

The problem: a kid fails their multiplication test. The parent googles "help my kid with times tables" and finds 50 identical flashcard apps. None of them stick because they're generic and boring, and none give parents visibility into whether they're actually working.

Times Table Rescue solves this three ways:

1. **Personalized word problems via AI.** During onboarding, a parent picks their child's top interests (animals, sports, video games, etc.). Every drill problem is generated as a word problem in that context. "8 sharks each ate 6 fish. How many fish total?" beats "8 × 6 = ?" every time for engagement.

2. **Parent progress dashboard.** After each session, the parent gets a simple report: which tables are weak, which are improving, how many minutes practiced. Parents are the actual customer — give them the data they need to feel the app is working.

3. **Tutoring center channel.** The app is sold as a monthly subscription to tutoring centers and after-school math programs who assign it as homework. This is the distribution wedge: tutors already have trusted relationships with the exact parents who have this exact problem.

The scope is deliberately narrow: multiplication tables only, ages 7–10, no gamified open world, no avatars, no social features in v1. Just the best possible personalized drill experience with parent visibility, sold through trusted intermediaries.

## Why This Is Now Worth Building

The critique identified a real market (kids' math edtech makes billions) but correctly killed the original non-idea. This version is worth building because it targets a specific crisis moment (kid failing times tables) with a specific distribution channel (tutoring centers) that sidesteps the consumer App Store war entirely. The AI-personalized word problems are a genuine differentiator achievable with a weekend's integration work, and the parent dashboard solves the trust/retention problem that kills most kids' quiz apps.

## Open Questions

- How many tutoring centers exist in a typical metro area, and what's the realistic sales cycle to sign the first 10? Cold email, or does this require in-person demos?
- What's the minimum viable parent dashboard — SMS digest, email, or in-app? Which do tutoring center owners actually want to show parents?
- Does the AI word problem generation stay coherent and age-appropriate reliably enough without heavy moderation? Need to test with a real 7-year-old.
- Is the $30–50/month tutoring center price right, or should it be per-student-seat pricing to align incentives?
- What does curriculum alignment look like for Common Core grade 3–4 multiplication standards, and how hard is it to add that credibility signal?
- Are there liability or privacy concerns with tutoring centers passing student first names to the app for problem personalization?

## Version History
- v1: Original idea
- v1.1: Critique — no differentiation, category not idea, no distribution, no monetization model, ignored COPPA
- v2: Improved — narrowed to times table remediation niche, AI word problem personalization, B2B2C via tutoring centers, COPPA-safe monetization, parent dashboard for retention

### Critique

**Verdict: NEEDS MAJOR WORK — Score: 6/10**

The pivot to crisis-moment remediation and the B2B2C tutoring center channel are genuinely smart moves. The parent dashboard as retention mechanism is the most sophisticated thinking in the doc. However, four critical flaws need resolving before building: (1) AI content safety for kids is not a "weekend integration" — it's a product safety crisis waiting to happen that requires human review pipelines from day one; (2) the free individual parent tier cannibalizes the B2B pitch and needs to be killed in v1; (3) flat-fee pricing creates perverse incentives at scale — switch to per-seat immediately; (4) the tutoring center sales cycle is a 3–6 month grind requiring in-person demos, not cold email, and that assumption is completely untested. The AI word problem personalization is a feature, not a moat — it'll be table stakes within 18 months. The name "Times Table Rescue" stigmatizes the child user every session. Revenue ceiling is real: this might be a $500K ARR lifestyle business, not a venture play, and the doc never acknowledges that. Fix the pricing, kill the free tier, do 20 tutoring center conversations before writing code, and solve content safety in week 1.
