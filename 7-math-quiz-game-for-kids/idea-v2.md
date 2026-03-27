# Idea #7: Math Sprint — Daily Multiplication Mastery for 7–9 Year Olds with ADHD/Focus Challenges (Improved)
## Version: v2
**Date:** 2026-03-20 17:21
**Status:** improved

## Original Idea
math quiz game for kids

## What Changed and Why
- **Brutally specific niche instead of a genre:** Narrowed from "kids" to 7–9 year olds struggling specifically with multiplication fluency — the exact gap that causes downstream failure in 3rd–4th grade math. ADHD and attention-challenged kids are chronically underserved by existing apps (Prodigy requires 20-minute sessions; Khan Academy Kids is too passive). This is a real, named pain point parents search for and spend money on.
- **One sharp mechanic nobody has nailed:** 60-second daily sprints, not open-ended game sessions. Designed to fit inside a parent's morning routine (while brushing teeth, eating breakfast). Short enough for attention-challenged kids to complete without distraction. The retention loop is a parent-facing streak + gap report texted each morning — parents stay accountable, not just kids. This is the thing Prodigy, Coolmath, and Khan Academy do not do.
- **Distribution channel before code:** Target homeschool Facebook groups and "ADHD parenting" communities (large, passionate, underserved by EdTech). A free printable "60-second multiplication sprint sheet" as lead magnet validates demand before a single line of app code is written. If 200 parents download and return the sheet, build the app. If not, kill it. No app store discoverability risk at launch.
- **Monetization is concrete:** Free tier = 5 sprints/week. $4.99/month = unlimited sprints + parent gap dashboard + curriculum calendar sync (aligned to Common Core 3rd grade pacing). B2C consumer parents, not schools — avoids enterprise sales complexity entirely.
- **Moat is data + curriculum alignment:** Spaced repetition engine that tracks which specific multiplication facts each child misses repeatedly, then resurfaces them at optimal intervals. Over time the per-child model becomes the product. Cloning the quiz loop is trivial; cloning 6 months of a child's gap data is not.

## Improved Description
**Math Sprint** is a 60-second-per-day multiplication fluency app for 7–9 year olds who struggle to focus during traditional math practice — specifically kids with ADHD, sensory sensitivity, or attention challenges whose parents have already tried and abandoned Prodigy or Khan Academy.

**The core mechanic:** Every morning, the app sends a parent a push notification with a single 60-second multiplication sprint ready for their child. The sprint is adaptive — it surfaces the exact facts the child missed yesterday, not random problems. After the sprint, the parent gets a one-line gap report: "Emma still misses 7×8 and 6×9. Try again tomorrow." No dashboards to navigate, no games to load, no 20-minute sessions to schedule.

**Why 60 seconds works for this audience:** Kids with attention challenges can sustain focus for 60 seconds. They cannot sustain 20 minutes. Every existing math app is designed for neurotypical session lengths. Math Sprint is designed around the attention window of its actual user.

**Distribution path:** Launch in homeschool Facebook groups and ADHD parenting communities (r/ADHD_parents, "Homeschool Mom" Facebook groups with 100k+ members) with a free printable sprint sheet. Validate that parents return the sheet and ask for more before building the app. If 200 parents engage with a PDF, build a web app. If the web app retains at 30%, build native.

**Monetization:** Free tier covers 5 sprints/week. Pro at $4.99/month unlocks unlimited sprints, the parent gap dashboard, and curriculum calendar alignment to Common Core 3rd grade pacing (so parents know exactly which facts their child needs before the next school unit).

**Moat:** Spaced repetition model personalized per child. The longer a family uses it, the more accurate the gap identification. Competitors can clone the quiz; they cannot clone the child's individualized fact map.

## Why This Is Now Worth Building
The ADHD/attention-challenged kids' math market is a real, underserved niche — existing apps are built for 20-minute neurotypical sessions, and ADHD parenting communities are actively vocal about this failure. The 60-second sprint mechanic is genuinely differentiated (no major app is designed around a sub-2-minute daily habit), and the distribution-first validation path (printable sheet → web app → native) means you can confirm demand before writing any production code. At $4.99/month with a clear B2C motion and word-of-mouth distribution through tight-knit homeschool communities, this is a viable small business even at 500 paying subscribers.

## Open Questions
- Do parents in ADHD communities actually want a parent-driven daily trigger, or do they want the child to self-initiate? (Survey before building)
- Is Common Core 3rd grade pacing the right curriculum hook, or do homeschoolers specifically reject Common Core? (Could be a liability in that community)
- What is the actual retention rate of a 60-second mechanic — does the novelty wear off after 2 weeks even for ADHD kids?
- Is $4.99/month the right price, or does "education subscription fatigue" mean a one-time purchase ($9.99) converts better?
- Can a solo developer build a spaced repetition engine that is meaningfully better than a simple shuffle, or is this false complexity?
- Are ADHD parenting Facebook groups receptive to product promotion, or do admins block it? (Test with organic posts, not ads)

## Version History
- v1: Original idea
- v1.1: Critique - idea is a genre not a product; zero differentiation, no niche, no retention mechanic, no distribution path
- v2: Improved - narrowed to ADHD/attention-challenged 7–9 year olds, 60-second daily sprint mechanic, homeschool community distribution-first validation, $4.99/month B2C

### Critique
**Score: 6/10 — WORTH BUILDING** (with significant conditions)

The v2 sharpening is real: the ADHD niche is underserved, the 60-second mechanic is genuinely differentiated, and the distribution-first validation path (printable sheet → web app → native) is the right sequence. Parent-facing gap report is a smart UX insight. B2C motion is correct.

Critical blockers before building: (1) The free tier (5/week) breaks the daily habit loop before users hit the paywall — fix this first. (2) Common Core alignment and homeschool communities are in direct conflict; homeschoolers are disproportionately anti-Common Core. (3) COPPA compliance is unpriced — tracking per-child performance data on under-13s requires legal review and compliance overhead a solo dev probably hasn't budgeted. (4) This is a naturally time-limited product (3–6 months per child) masquerading as a subscription — model the churn before committing. (5) "Return the sheet" validation converts at maybe 5–10%, so 200 completions requires ~2,000 downloads. (6) The spaced repetition moat is probably a weighted shuffle in disguise — the real moat (gap data) only matters if you first solve retention.

Fix the free tier, split your curriculum messaging by channel, solve performance capture before building SR, and get COPPA guidance before writing any code that touches child data.
