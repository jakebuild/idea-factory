# Idea #38: AI Dating Photo Technical Audit (Improved)

## Version: v3
**Date:** 2026-04-01 14:32
**Status:** improved

## Original Idea
AI photo ranker for attractiveness. Upload 5-10 selfies or dating-profile photos and the app ranks them from most attractive to least, tells you exactly why the weak ones fail, and picks the single best opener for Tinder, Hinge, LinkedIn, or Instagram. The reveal is instant: your winning photo, your worst photo, and the one fix that would raise your whole profile.

## What Changed and Why

- **Cut pairwise comparisons entirely — replaced with single-pass independent scoring per photo.** The critique was right: 28 comparisons for 8 photos is expensive, slow, and produces inconsistent results. Worse, it is a lie — you would have to stop doing true pairwise at scale anyway. v3 scores each photo independently on 5 measurable technical criteria (lighting quality, face visibility, focus/sharpness, framing/crop, background clarity). Each criterion gets a 1–5 score with a plain reason. No inter-photo dependencies, no combinatorial cost explosion, no hidden cheating. The output is an honest technical audit, not a fake tournament.

- **Dropped all social outcome prediction language.** "Will perform better on Hinge" is unverified and uncheckable before build. It is heuristics wearing a lab coat and the critique called it correctly. v3 makes no claim that better technical quality = more matches. The product is a **technical photo quality audit**: it tells you if your photo is blurry, shadowed, poorly cropped, or cluttered. What you do with that is your business. This is both more honest and more defensible when users disagree.

- **Narrow the accepted input: solo clear-face photos only.** Group shots, photos where eyes are obscured (sunglasses, hats, extreme shadows), extreme filters, and NSFW content are rejected at upload with a plain-language reason. This makes moderation tractable for one developer, makes scoring results more reliable and less ambiguous, and removes the "whose face am I scoring?" moderation nightmare for group shots. The product becomes: audit tool for photos where you are the solo subject with your face visible. That is the actual use case anyway.

- **Monetize at first use — freemium gate between scores and explanations.** The critique was explicit: if this is a one-shot utility, the business model cannot be postponed. v3 shows scores for free (e.g. "Photo 2: 4/5 — Photo 5: 2/5") to create desire, then charges a small one-time fee ($4–6) to unlock the full explanations and fix suggestions. This tests willingness to pay at the exact moment the user has seen enough to want more. No subscriptions. No credits. One session, one payment, full results unlocked. Monetization decision made now, not later.

- **Added explicit disagreement mechanic to prevent trust collapse.** The critique flagged the obvious failure mode: user sees a result they disagree with and the whole product feels fake. v3 adds a thumbs-down on every photo result card. When a user disagrees, they see the raw signal breakdown — the exact 5 criteria scores and what the model flagged. This turns disagreement into transparency ("here is exactly what the AI saw") rather than a black box verdict. It does not fix bad results, but it prevents immediate trust collapse by giving the user something to inspect.

## Improved Description

A technical photo audit tool for dating profile photos. Upload 3–6 solo clear-face photos. The app scores each photo independently on 5 technical criteria: lighting quality, face visibility, focus/sharpness, framing and crop, background clarity. Each photo gets a score and a one-line reading per criterion.

The free output: a score for each photo (e.g. "Photo 2: 4/5, Photo 5: 2/5") and which photo is your technical best and worst. No explanations yet.

The paid output ($4–6 one-time): full explanations per criterion per photo, one specific fix per weak photo ("Retake this facing a window — your face is underexposed"), and a priority order for which fix will move the needle most.

No attractiveness language. No "will perform better on Hinge" claims. No pairwise. No accounts. Photos deleted immediately after analysis. Upload only solo clear-face photos — the tool rejects groups, obscured faces, and NSFW content with a plain reason.

When users disagree with a result, they can tap to see the raw criterion breakdown — the exact signals the model flagged — so the output is inspectable rather than mysterious.

The one distribution bet: Reddit communities where people already ask "which photo should I use?" (r/Tinder, r/hingeapp, r/dating_advice). People post this exact question daily. The tool solves that question. Soft launch by being useful in those communities before building any paid acquisition.

## Why This Is Worth Building (Solo + AI)

A solo dev can ship upload → single-pass scoring → score reveal → payment gate → explanation unlock in 2 weeks. Single-pass scoring is dramatically simpler than pairwise — one API call per photo, no tournament logic, consistent cost per session. The freemium gate validates monetization in the first week of live users without building a subscription system. The narrow input constraint (solo clear-face photos only) makes moderation a real problem with tractable edges rather than an infinite edge-case swamp.

## Solo MVP Scope

**What's in v1:**
- Upload 3–6 photos with explicit constraints (solo, clear face visible, no sunglasses, no groups)
- Client-side pre-validation: file size, format, count limits
- NSFW + group shot rejection (AWS Rekognition or OpenAI moderation endpoint) before reaching vision model
- Single-pass scoring: one API call per photo, 5 criteria, 1–5 score + one-line reason each
- Free tier output: score per photo + best/worst photo labels (no explanations)
- Payment gate ($4–6, Stripe one-time charge) to unlock full explanations
- Paid output: full per-criterion explanation, one specific fix per weak photo, priority fix order
- Disagree mechanic: tap any result card to see raw criterion breakdown
- Session-only storage — photos deleted after analysis, no accounts
- Privacy copy on upload screen

**What's out of v1:**
- Pairwise comparisons — cut entirely per critique, not to return under any name
- LinkedIn or Instagram modes — different use cases, out entirely
- Opener generator — out entirely per critique
- Attractiveness scoring or attractiveness language — out entirely
- "Will perform better on Hinge" or any match outcome prediction language
- Account creation or saved history
- Match outcome feedback loop or analytics dashboard
- Social sharing of results
- Mobile app — web only
- Subscription or credits model in v1 (one-time charge only)

**Estimated build time with AI coding tools: 12–14 working days (2.5 weeks)**
- Days 1–2: Upload UI with constraint messaging, file validation, rejection flow
- Days 2–3: NSFW/group shot moderation integration and rejection handling
- Days 4–6: Vision model integration, single-pass scoring prompt, 5-criterion output parsing
- Days 7–8: Free results screen (scores + best/worst reveal), disagree mechanic (raw breakdown modal)
- Days 9–10: Stripe integration, payment gate between score reveal and explanations
- Days 10–11: Paid results screen (full explanations, fix suggestions, priority order)
- Days 12–13: Session deletion, privacy flow, end-to-end test with real photos
- Day 14: Deploy, Reddit soft launch prep

Note: prompt engineering for reliable 5-criterion scoring across diverse photo types is real work — budget 1–2 days for iteration, not an afternoon.

## Assumption Check

1. **"A vision model can reliably score 5 technical photo criteria (lighting, focus, face visibility, crop, background) on solo clear-face photos without producing outputs that feel biased or wrong."**
   — UNVERIFIED. Technical criteria are more defensible than social outcomes, but model output quality on diverse faces, skin tones, ages, and lighting conditions is not validated until you run real photos through it. **Risk:** results skew in ways users find biased or feel wrong for a meaningful subset of inputs. **Fallback:** build in the disagree mechanic from day one; frame all output as "technical reading" not verdict; add a visible disclaimer that results are a starting point, not a judgment. Do prompt testing on 20–30 diverse photo samples before launch.

2. **"Users will pay $4–6 to unlock explanations after seeing their scores."**
   — UNVERIFIED. The freemium gate is designed to test this, which is the point. The scores create desire; the question is whether that desire converts to payment. **Fallback:** if conversion is under 5%, lower the price or show one free explanation to demonstrate value before the gate. The gate itself is the experiment.

3. **"Reddit communities (r/Tinder, r/hingeapp) will tolerate soft promotion of this tool."**
   — UNVERIFIED. Reddit communities vary wildly on promo tolerance. **Fallback:** do not blast the link. Instead, manually answer "which photo should I use?" posts for the first two weeks as a real human participant, referencing the tool only when directly useful. This builds credibility before visibility.

**Why would users come back in week 2?** Mostly they will not, and that is accepted. The one real return case: user retakes photos based on feedback and wants to re-audit. That is a second purchase event, not a retention loop. The product must earn on first use. Retention is not the model.

## Open Questions

- What is the actual per-session API cost for 3–6 photos through a vision model at single-pass scoring? Must model this before setting the $4–6 price point — need margin after API cost.
- Which moderation stack is cheapest and most reliable for the narrow constraint (reject NSFW, group shots, faces with obscured eyes)? AWS Rekognition vs OpenAI moderation endpoint vs combined — test before committing.
- Does showing scores for free create enough desire to drive payment, or does it just let users screenshot the scores and leave? Needs live testing in week 1.
- What copy frames the "technical audit" framing without making the product sound boring? The claim is narrower and more honest — but it also needs to not sound like a camera settings checker.
- Is $4–6 the right price, or is $2–3 better for impulse conversion on a first-session tool? Needs a price test.

## Design Handoff

- **Screen 1: Landing / Value Prop** — Purpose: explain what the tool does and convince the user to upload. Key elements: headline ("Is your dating profile photo technically good?"), 3-bullet explainer (what is scored, what you get free vs paid, privacy promise: "deleted after analysis"), CTA "Audit my photos." Explicit constraint callout: "Works best with solo clear-face photos." No account required badge. Privacy badge. Sample score card shown as static example to set expectation.

- **Screen 2: Upload Screen** — Purpose: collect 3–6 solo clear-face photos. Key elements: drag-and-drop or file picker, photo count indicator (3 min, 6 max), thumbnail preview grid with remove button per photo, inline constraint checklist ("Solo photo? Face clearly visible? No sunglasses?"), privacy notice ("Analyzed and immediately deleted — never stored"), Analyze CTA (disabled until 3 photos). Error states: file too large, wrong format, under minimum count.

- **Screen 3: Rejection Screen (inline or modal)** — Purpose: handle rejected photos cleanly. Key elements: which photo was rejected, plain reason ("This photo appears to show more than one person — please upload solo photos only"), option to remove and replace, or continue without that photo if count still meets minimum. No harsh language.

- **Screen 4: Processing / Loading State** — Purpose: hold user during analysis (5–10s per photo). Key elements: animated progress, photo count ("Analyzing photo 2 of 5…"), privacy reminder ("Deleting photos after analysis").

- **Screen 5: Free Results — Score Reveal** — Purpose: show scores and create desire for explanations. Key elements: score card per photo (photo thumbnail + score e.g. "4/5"), best photo badge ("Your technical best"), worst photo badge ("Needs work"), score breakdown hint without explanation ("Lighting: ✓ Focus: ✓ Face visibility: ✗ Crop: ✓ Background: ✗" — checkmarks only, no text reasons), CTA "Unlock full explanations — $5." No pairwise. No ranking beyond best/worst.

- **Screen 6: Payment Gate** — Purpose: one-time charge to unlock explanations. Key elements: price ($5), what is included ("Full explanation for every photo, specific fix per weak photo, priority fix order"), Stripe payment form (card), privacy note ("No account created"), "Cancel and keep scores" option. Clean, minimal — no upsell noise.

- **Screen 7: Paid Results — Full Explanations** — Purpose: deliver the full audit. Key elements: per-photo cards in score order (best to worst), each card shows: score, one-line reason per criterion (5 lines), one specific fix for photos scoring under 3/5 ("Face is underexposed — retake facing a window"), priority fix callout ("Start here: your top fix is…"). Disagree button on each card. Re-audit CTA at bottom ("Retook your photos? Audit again — $5").

- **Screen 8: Disagree / Raw Breakdown Modal** — Purpose: prevent trust collapse when user disagrees with a result. Key elements: triggered by thumbs-down on any result card, shows raw criterion scores with the exact signal text the model output ("Lighting: 2/5 — Face shows significant shadow on left side, ambient source appears to be behind subject"), no softening or reinterpretation, close button, optional free-text "Tell us what you think" (no account, stored anonymously for product learning).

**Key interactions:**
- Upload → rejection handling (if any) → Processing → Score Reveal (free) → Payment Gate → Full Explanations (paid) — primary linear flow
- Score Reveal: any photo card → Disagree modal showing raw breakdown (free, no payment required to see raw signals)
- Full Explanations: Re-audit CTA → back to Upload screen (new session, new payment)
- NSFW/group rejection happens between Upload submit and Processing — user returns to Upload screen to fix

## Version History
- v1: Original idea — AI attractiveness ranker with opener generator for Tinder/Hinge/LinkedIn/Instagram
- v2.1: Critique — pairwise too expensive, outcome prediction unverified, no retention, monetization postponed, moderation underspecified (Score: 5/10)
- v3: Improved — cut pairwise for single-pass scoring; dropped all outcome prediction language; narrowed to solo clear-face photos; freemium gate with one-time payment at first use; disagree mechanic for trust; Reddit soft-launch as distribution bet
