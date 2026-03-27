# Idea #10: Math Quiz Game for Kids (Improved)

## Version: v3
**Date:** 2026-03-20 20:17
**Status:** improved

## Original Idea
math quiz game for kids

## What Changed and Why

- **Killed the free individual parent tier entirely; pure B2B in v1** — The v2 free tier was a self-inflicted wound: it undermined the tutoring center pitch the moment a parent compared notes. Going pure B2B removes this conflict, forces validation that centers will actually pay before adding consumer complexity, and keeps the product legally cleaner under COPPA (one customer type: businesses). The consumer tier, if it ever exists, comes after 10 paying centers confirm the model.

- **Switched to per-seat pricing ($2.50/student/month, 10-seat minimum)** — Flat $30–50/month created an inverse incentive: big centers (80 students) paid $0.50/student while small centers paid $6/student. Per-seat aligns revenue with value and scales honestly. A 10-student minimum at $2.50 sets a $25/month floor; a 50-student center pays $125/month. This is more defensible to a skeptical center owner because it scales with their actual usage.

- **AI content safety is week-one infrastructure, not an afterthought** — v2 described AI word problem generation as "achievable with a weekend's integration work." That was wrong and dangerous. One inappropriate AI-generated problem reaching a 7-year-old through a tutoring center destroys every B2B relationship simultaneously. The fix: all generated problems go through a human review queue before any child sees them. A small pre-reviewed problem bank (500–1,000 problems per interest category) ships at launch. AI generation is used to expand the bank offline, reviewed by a human, then deployed — never live-generated per session. This is slower to build but is the only responsible approach.

- **Rebranded child-facing product from "Times Table Rescue" to "Times Champ"** — "Times Table Rescue" labels the child as someone who needs rescuing, every single session. An 8-year-old opening "Times Table Rescue" daily is being reminded they're behind. The parent and tutoring center can know this is a remediation tool without broadcasting it to the kid. "Times Champ" is empowering; the remediation framing is in the B2B sales materials only.

- **Added specific curriculum alignment claim to the B2B pitch** — "Aligned to Common Core Grade 3 Operations & Algebraic Thinking standard 3.OA.C.7" is a single line that meaningfully increases conversion with tutoring centers who need to justify software purchases to skeptical parents. v2 listed this as an open question; it's answered: implement Common Core 3.OA.C.7 alignment from day one and put it in the pitch.

- **Acknowledged the revenue ceiling honestly** — This is a $300K–$800K ARR business at saturation in one metro, not a venture-scale play. The doc now says that explicitly. This is a viable, profitable small business if operated leanly — a solo founder can build a good lifestyle business here. Investors are the wrong audience. The right framing is: validate in one city, expand to adjacent metros, keep costs low.

## Improved Description

**Times Champ** — a B2B-only multiplication drill app for tutoring centers and after-school programs, built specifically for kids ages 7–10 who are struggling with their times tables.

**The problem:** A kid fails their times table test. The parent calls their tutoring center. The tutoring center needs a homework tool that's more engaging than a workbook, gives the parent visibility, and doesn't require the tutor to babysit compliance. Existing apps (Prodigy, Khan Academy) are broad — they don't specifically address the crisis moment of a child who is behind on multiplication.

**How it works:**

1. **Tutoring center pays per-seat ($2.50/student/month, 10-seat minimum).** The center assigns Times Champ as homework to students who need multiplication remediation. Setup is a single link the tutor texts to parents — no IT procurement, no install required, works in any browser.

2. **Personalized word problems from a pre-reviewed bank.** During onboarding the parent picks their child's top interests (dinosaurs, Minecraft, soccer, animals). Every drill problem is drawn from a pre-human-reviewed bank of interest-tagged word problems. "8 sharks each ate 6 fish. How many fish total?" beats "8 × 6 = ?" for engagement every time. Problems are never live-generated — all content is reviewed before it reaches any child.

3. **Parent progress digest (SMS/email, weekly).** After each session the parent gets a plain-language summary: which tables are weak, which are improving, minutes practiced this week. The parent becomes the retention agent — they remind the kid because they can see the data. The tutoring center gets an aggregate dashboard showing which students are practicing and which aren't.

4. **Common Core aligned.** Explicitly aligned to 3.OA.C.7 (Grade 3 multiplication fluency standard). One line in the pitch that unlocks B2B trust with centers serving Common Core-curriculum schools.

**Scope discipline:** Multiplication tables only (×1 through ×12), ages 7–10, no open world, no avatars, no social features in v1. The entire product is: best-in-class personalized multiplication drill with parent visibility, sold exclusively through tutoring centers.

**Go-to-market:** Before writing code, 20 in-person conversations with tutoring center owners in one metro. Bring a paper prototype. The question isn't "do you like this idea?" — it's "what do you currently give students for times table homework, and what would make you switch?" First paying customer target: 3 months from first conversation.

**Revenue reality:** At 10 centers × 30 students average × $2.50 = $750/month. At 50 centers × 30 students = $3,750/month ($45K ARR). At 200 centers across a metro = $180K ARR. Saturation in a single large city might reach $300K–$500K ARR. This is a lifestyle business, not a venture play. Operate leanly, expand to adjacent metros if it works.

## Why This Is Now Worth Building

The tutoring center B2B model is validated by prior exits in this space (Remind, Bloomz) and avoids the consumer App Store war entirely. Per-seat pricing aligns revenue with value and is simpler to defend in a sales conversation. The content safety architecture (pre-reviewed problem bank, no live AI generation for child-facing content) removes the most credible kill shot against the product — one bad AI-generated problem no longer wipes out the entire customer base. This is a structurally sound small business with a defensible distribution channel, honest revenue expectations, and no dependency on viral consumer growth.

## Open Questions

- After 20 tutoring center conversations: do owners want to sign up themselves, or do they want parent-direct signup with center as referral? This changes the sales motion fundamentally.
- What does a realistic problem bank look like for 10 interest categories × 12 multiplication tables × 20 difficulty levels? That's ~2,400 problems minimum. How long does human review take per problem, and what does that cost?
- Is the tutoring center owner the decision-maker, or do they need to get parent buy-in first before purchasing? Understanding the sales chain matters before any outreach.
- What's the minimum technically viable product that a tutoring center owner would pay $25/month for before the problem bank is fully built? Could a Google Form + Sheets prototype validate willingness to pay in week 1?
- Does the per-session parent SMS create any COPPA complications, even though the student is not the direct customer? Need a 30-minute conversation with a COPPA-experienced attorney before launch.
- At what center count does this need a second person? The sales motion (in-person demos, relationship building) does not scale without a person doing it full-time.

## Version History
- v1: Original idea
- v2.1: Critique — AI content safety not solved, free tier cannibalizes B2B pitch, flat pricing creates perverse incentives, tutoring sales cycle untested, child branding stigmatizing
- v3: Improved — pure B2B only, per-seat pricing, pre-reviewed problem bank (no live AI generation for kids), renamed to Times Champ, Common Core alignment added, revenue ceiling acknowledged honestly
