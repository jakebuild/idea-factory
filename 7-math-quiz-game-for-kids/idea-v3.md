# Idea #7: Math Sprint (Improved)
## Version: v3
**Date:** 2026-03-20 17:24
**Status:** improved

## Original Idea
math quiz game for kids

## What Changed and Why

- **Free tier restructured around habit formation, not sprint count:** v2's "5 sprints/week" actively breaks the daily habit loop — users miss Wednesday and Thursday, streak is gone, churn follows. v3 flips the gate: unlimited sprints are free forever, but the parent gap dashboard (the highest-value feature for adults writing the check) is Pro-only. The child never hits a paywall mid-habit. The parent hits it the moment they want to act on the data. This is the correct gate: habit forms first, paywall appears when value is demonstrated.

- **Curriculum split by channel — no more Common Core in homeschool contexts:** v2 offered "Common Core 3rd grade pacing" as a Pro feature and simultaneously targeted homeschool Facebook groups. These are in active conflict — religious homeschool communities are disproportionately anti-Common Core and will ban product posts that invoke it. v3 uses channel-specific framing: for homeschool communities, "aligned to 3rd grade multiplication fluency benchmarks" (no Common Core mention); for school-adjacent parents, "Common Core 3rd grade pacing." Same feature, different language. The Pro feature is "curriculum pacing calendar" — the user picks their curriculum track at signup.

- **COPPA compliance addressed before any child data is stored:** v2 never priced in the legal overhead of tracking named per-child performance data on under-13s. v3 solves this structurally: the free tier stores no individual child data at the account level — it runs in "anonymous session mode" (no login required, no persistent child profile, no data stored server-side). Pro tier requires a parent account, parental consent flow, and explicit data agreement. The gap dashboard only exists for authenticated parents who have completed COPPA consent. This is the architecture decision that gates Pro, not a sprinkle-on-later compliance review.

- **LTV extended via graduation path:** A child masters 12×12 in 3–6 months, then has no reason to keep paying. v2 had no answer. v3 adds an explicit progression: Multiplication → Division Facts → Fraction-Decimal Pairs → Multi-step Word Problem fluency. Each "level" is a distinct product milestone. "Math Sprint: Level 2" launches as an in-app unlock ($2.99 one-time or included in Pro). Annual LTV per subscriber goes from $20–40 (3–6 months) to $60–120 (12–18 months). This makes the unit economics viable before you need 2,000+ subscribers.

- **Performance capture is explicit and parent-reported only in Phase 1:** v2 left the data capture mechanism unspecified — a fatal flaw since the SR engine is only as good as its inputs. v3 is explicit: in Phase 1 (printable sheet + web app), the parent enters a single number after each sprint ("Emma got 14/20 correct"). This is coarse but reliable. The SR engine in Phase 1 operates on fact-set difficulty levels, not individual fact tracking. In Phase 2 (native app), the child taps answers on-screen and individual fact-level tracking begins. The SR moat is a Phase 2 claim, not a Day 1 claim. This is honest.

- **Validation threshold recalibrated:** v2 set "200 parents return the sheet" as the go signal but never modeled the funnel. At a realistic 5–10% completion rate, that requires 2,000–4,000 downloads — a real distribution challenge. v3 replaces this with a two-step gate: (1) 500 downloads of the printable sheet from organic community posts (measures awareness and pull), and (2) 50 parents post a result or reply with feedback (measures active engagement, not passive download). 50 engaged parents is more signal than 200 passive downloaders. The bar is lower in absolute numbers but higher in quality.

## Improved Description

**Math Sprint** is a 60-second-per-day multiplication fluency app for 7–9 year olds with ADHD or attention challenges whose parents have already tried and given up on Prodigy or Khan Academy.

**The core mechanic is unchanged and remains the sharpest thing about this idea:** Every morning, the app sends the parent a push notification. The child does one 60-second sprint — adaptive problems targeting their current weakest facts. Then they're done. No loading screens, no game tokens, no 20-minute sessions. For a kid who can't sustain 5 minutes of Khan Academy, 60 seconds is achievable. Every day. That daily win is the product.

**Free tier:** Unlimited sprints, no account required. The sprint runs in anonymous session mode — no child data stored, no login wall. Parents can run the sprint directly from the notification link on any device. There is no free trial countdown, no sprint cap, no reason to hit a paywall until the parent wants more.

**Pro tier ($4.99/month or $34.99/year):** Unlocks the parent gap dashboard ("Emma still misses 7×8 and 6×9 — this is her 4th week missing these two"), curriculum pacing calendar (parent chooses their track: Common Core, Singapore Math, or homeschool benchmark — no track is pushed by default), and progress export for parent-teacher conferences. COPPA consent is required to activate Pro — this is the architecture, not the legal department's problem to solve later.

**Graduation path:** When a child masters all 144 multiplication facts (tracked automatically in Pro), the app prompts: "Emma has graduated multiplication sprints. Unlock Division Facts for $2.99 — or it's included in your Pro subscription." This extends natural product lifetime from 3–6 months to 12–18 months.

**Distribution:**
- Phase 1: Organic posts in ADHD parenting communities and homeschool Facebook groups, framed as "we made this printable for our own kid." Free printable sprint sheet. Gate: 500 downloads + 50 active responses.
- Phase 2: Web app. Gate: 30% week-4 retention from Phase 1 cohort.
- Phase 3: Native app with in-screen answer tapping and per-fact SR tracking.

**Channel-specific framing:**
- Homeschool groups: "multiplication fluency benchmarks" — no Common Core language
- School-adjacent parents: "aligned to Common Core 3rd grade pacing"
- ADHD communities: "designed for kids who can't do 20 minutes — 60 seconds, every day, that's it"

**Pricing research before launch:** Test $4.99/month vs. $7.99 one-time purchase in community posts before committing. Education subscription fatigue is real; a one-time purchase with a free-forever tier may convert better in communities that have already abandoned Prodigy subscriptions.

## Why This Is Now Worth Building

The structural problems in v2 were all self-inflicted: the free tier broke the habit it was trying to build, the curriculum positioning actively alienated the best distribution channel, and COPPA compliance was a liability waiting to happen. v3 fixes all three by restructuring the free tier around unlimited access (gate the dashboard, not the sprints), splitting curriculum language by channel, and making COPPA consent the architectural gate for the Pro tier rather than a legal afterthought. With a graduation path that extends LTV from 3–6 months to 12–18 months, the unit economics work at a realistic subscriber count, and the distribution-first validation sequence (printable → web → native) remains the right call.

## Open Questions

- What is the realistic morning routine trigger? "Parent gets notification → brings child to device → child cooperates with timed sprint before school" assumes ADHD family mornings that may not cooperate. Is the right trigger before school, after school, or at bedtime? Needs direct parent input before assuming timing.
- Does "anonymous session mode" (no persistent child data in free tier) gut the SR engine enough that the free experience is too obviously inferior? Risk of making free feel broken rather than intentionally limited.
- $4.99/month vs. $7.99 one-time — needs direct price testing in communities before launch, not a guess.
- How does the pacing calendar work for homeschoolers who don't follow any standard benchmark? Is "no curriculum track" a valid option, or does that make the Pro feature useless for the biggest channel?
- After a child masters multiplication and division, do parents perceive fraction-decimal fluency as the same product (expected in subscription) or a new product (willing to pay again)? This determines whether graduation is an upsell or a churn event.
- What is the minimum viable COPPA compliance stack for a solo founder — is a 30-minute lawyer consult + standard EdTech consent flow sufficient, or does this require ongoing legal retainer?

## Version History
- v1: Original idea
- v2.1: Critique - free tier breaks habit loop, Common Core vs. homeschool conflict, COPPA unpriced, LTV too short for subscription
- v3: Improved - unlimited free sprints (gate dashboard not sprints), channel-split curriculum framing, COPPA-as-architecture in Pro tier, graduation path extends LTV to 12–18 months
