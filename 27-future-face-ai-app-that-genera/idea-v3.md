# Idea #27: Future Face AI (Improved)
## Version: v3
**Date:** 2026-03-28 22:38
**Status:** improved

## Original Idea
Future Face AI app that generates scientifically accurate age progression of YOUR exact face. Take one selfie → AI estimates your current age, then creates 4 panels showing you at current age, 15-20 years older, 30-35 years older, and 55-60 years older. Identical bone structure, skull shape, all facial features stay the same — only natural aging applied. No beautification, no idealization — raw biological aging only. Photorealistic with detailed skin texture, pores, wrinkles, age spots, hair changes. The emotional hook: see what you and your family members might look like decades from now. Share the collage with friends. Parents use it to see their kids grown up. The creepy/real factor drives sharing.

## What Changed and Why
- **Adults-only in v1 — cut the "kids grown up" angle entirely.** The critique identified real legal exposure: EU AI Act, COPPA, biometric data laws in multiple jurisdictions, and Stripe's own content policies all create risk around generating aged images of minors. Even with good intentions, one content moderation flag kills the payment processor and the product. Adults-only eliminates this surface area completely. The "kids grown up" angle can be added in v2 after consulting a lawyer and validating that the core product converts.
- **Model validation is Step 0, not an open question.** The critique was blunt: if the aging model produces muddy, identity-destroying results, there is no product. v3 treats a proof-of-concept run (20 real photos through the actual Replicate/fal.ai pipeline) as a go/no-go gate before writing a single line of product code. If the output isn't good enough to charge $1.99 for, the idea is paused — not pivoted.
- **Native GIF/video export replaces "hope users screen-record."** The drag-reveal slider is a good UI interaction but a weak viral mechanic — Instagram and TikTok won't surface a screenshot of a slider. v3 adds a server-side step that renders a 3-second looping GIF/video of the reveal animation (original morphing to aged). This is what gets posted natively to every platform. It's the actual viral mechanic, not a wishful side effect.
- **Async delivery with email fallback is a first-class design decision.** If p95 Replicate latency is >20 seconds, waiting on a loading screen breaks the UX. v3 designs for this from day one: under 20s = synchronous result screen; over 20s = "we'll email you when it's ready" flow with a job queue. Email delivery adds ~1 day of build time but makes the product usable regardless of model latency.
- **Paywall moved to download/share, not result view.** Showing the full result (including aged image) before the paywall, then paywalling the download+video export, converts better than hiding the result behind watermarks. Users who see the actual output and like it will pay. Users who never see the output leave. Watermark-on-preview is a friction pattern that hurts conversion.

## Improved Description
**Future Face** is a web app where any adult uploads a single photo of themselves and gets a photorealistic image of what they'll look like 20, 40, or 60 years from now. No app install, no account, no subscription.

The core flow is intentionally minimal: upload a front-facing photo → pick a target age bracket (+20yr, +40yr, +60yr) → wait while the model runs → see the full result in a drag-reveal before/after view → pay $1.99 one-time to download the full-res image and a shareable GIF/video of the reveal animation.

The shareable format is the viral engine. Instead of a static watermarked image, the paid output includes a 3-second looping GIF/video of the reveal — original face smoothly transitioning to the aged version. This is natively shareable to Instagram Reels, TikTok, iMessage, and WhatsApp. Screen-recording is optional; the product produces the video directly.

Privacy is a hard architectural constraint and a marketing point: photos are deleted from the server immediately after the result is delivered. No face database, no training data, no storage. Zero-retention is stated in the UI and is architecturally enforced.

Scope restriction: v1 processes adults only (18+). ToS and upload UI explicitly state this. The "kids grown up" feature is a v2 consideration after legal review.

Tech stack: Next.js (web-first), Replicate or fal.ai for the aging model (validated pre-build), Stripe for one-time payments, Vercel + serverless functions for the job queue, ffmpeg/canvas for GIF/video export on the server side.

## Why This Is Worth Building (Solo + AI)
Scoping to adults-only and validating the model before writing product code eliminates the two biggest failure modes in one move — legal liability and shipping something nobody pays for. The core flow (upload → API call → result → Stripe → GIF export) is achievable in 10-14 days with Next.js and AI coding tools. Building the GIF export and email fallback as designed components (not afterthoughts) keeps the build honest and adds ~2-3 days, not weeks.

## Solo MVP Scope
- **What's in v1:**
  - Photo upload (file picker, mobile browser compatible, 18+ ToS gate)
  - Single aging progression: upload → pick target age bracket (+20yr, +40yr, +60yr) → result
  - Drag-to-reveal before/after interaction on result screen (free, no paywall)
  - $1.99 Stripe one-time payment to unlock full-res download + shareable GIF/video export
  - Server-side GIF/video render of reveal animation (3-second loop, 720p)
  - Async email delivery fallback if p95 latency >20s (job queue + simple email via Resend)
  - "Your photo is deleted immediately after delivery" privacy messaging, architecturally enforced
  - "18+ and you must own rights to this photo" consent checkbox at upload

- **What's out of v1:**
  - Kids/minors use case (v2, after legal review)
  - 4-panel collage output (single result is simpler to process and share)
  - Account/auth system (anonymous sessions only)
  - Native mobile app (web-only until validated)
  - Age estimation from selfie (user picks target age bracket manually)
  - Bulk processing / family comparison mode
  - Multiple simultaneous age progressions (pick one bracket, not all three at once)

- **Estimated build time with AI coding tools:** 12-14 days (including model validation sprint, GIF export, and async delivery)

## Open Questions
- **Does the aging model produce output good enough to charge $1.99 for?** This is the only question that matters before building anything. Run 20 real adult photos through the actual pipeline (Replicate IP-Adapter + face aging LoRA) and honestly evaluate. If the output is bad, pause the idea.
- **What is p95 latency on Replicate vs. fal.ai for this specific model?** Benchmark both before designing the loading/async flow. This determines whether sync or async is the primary UX path.
- **Does the $1.99 price convert, or is $0.99 better for impulse purchase?** No way to know until live. Launch at $1.99, A/B test down to $0.99 if conversion is poor.
- **What ffmpeg/canvas approach renders the reveal GIF/video server-side in acceptable time?** Needs a spike before designing the result screen — if server-side rendering adds >10 seconds, that changes the async architecture.

## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Landing / Hero** — Headline ("See yourself decades from now"), single upload CTA, 2-3 static sample before/after reveal previews with aged results visible, privacy line ("photo deleted immediately after delivery"), pricing callout ("view free · $1.99 to download + share"), "18+ adults only" note in footer
- **Screen 2: Upload / Photo Select** — Drag-and-drop zone (desktop) + tap-to-upload button (mobile), face guidance copy ("clear, front-facing, good lighting"), supported formats note, consent checkbox ("I am 18+ and have rights to this photo"), "no account needed" reassurance, "your photo is never stored" trust signal
- **Screen 3: Age Target Select** — 3-option toggle: "+20 years / +40 years / +60 years", thumbnail of uploaded photo confirming face detected, "Generate" primary CTA, "change photo" secondary CTA, brief copy explaining what the model does ("AI applies realistic aging — same face, more years")
- **Screen 4: Processing / Loading** — Full-screen animated loading state, progress indicator or spinner, rotating copy ("adding years...", "aging your face...", "almost done..."), estimated wait ("~15 seconds"), cancel/start over link; OR if async path: "We'll email you when it's ready" form (email input + submit), confirmation message, estimated delivery time
- **Screen 5: Result / Reveal** — Before/after drag-reveal slider (original left, aged right), divider is draggable, default 50/50 position, age label overlay on aged side, full result visible (no watermark), "Download + Share" primary CTA (triggers paywall for free users), "Try another age" secondary CTA, small "try another photo" tertiary link
- **Screen 6: Paywall / Unlock** — "$1.99 one-time — download full-res + get shareable video" headline, preview of what they get (full-res image + looping GIF/video of reveal), Stripe payment element, "pay once, yours forever" reassurance, security badge, no subscription copy
- **Screen 7: Download / Share** — Full-res image preview post-payment, download image button (saves to disk/camera roll), looping GIF/video preview of reveal animation, download video button, native share sheet trigger (Instagram, TikTok, iMessage), "Try another photo" CTA
- **Screen 8: Error / Retry** — Face-not-detected state with guidance ("try a front-facing photo in good lighting"), API failure state with retry CTA, upload size/format error state, "no face detected in minors photo" state (if ToS violation detected)

**Key interactions:**
- Upload → Age Select → Processing → Result is the strict linear happy path
- Result screen shows full aged image for free (no watermark) — paywall is at the download/share step only
- "Download + Share" on Result → Paywall modal if unpaid → on Stripe success → Download/Share screen
- "Try another age" on Result → returns to Age Target Select with same uploaded photo (no re-upload)
- Drag-reveal slider: left = original face, right = aged face; divider draggable across full width
- Share video on Download screen: triggers native share sheet with the looping GIF/video file attached
- Async path (high latency): Processing screen collects email → job queue runs model → Resend delivers result link via email → user clicks link → goes directly to Result screen

**Data each screen needs:**
- **Landing:** static sample images only, no dynamic data
- **Upload:** file blob (client-side only), consent checkbox state
- **Age Target Select:** uploaded photo thumbnail (local preview URL), selected age bracket (state)
- **Processing:** job ID (for polling or async tracking), elapsed time (local timer), email (if async path)
- **Result:** original image URL (temp, server-side), aged image URL (temp, server-side), age bracket label, payment status (free/paid)
- **Paywall:** aged image preview URL, Stripe payment intent client secret
- **Download/Share:** full-res image URL (post-payment), GIF/video URL (post-payment), shareable link
- **Error:** error type (face-not-detected / API-failure / format-error / minors-detected), retry state

## Version History
- v1: Original idea
- v2.1: Critique — FaceApp overlap too strong, "scientifically accurate" claim untenable, zero retention, privacy risk, mobile complexity bloat
- v2: Improved — pivoted to "kids grown up" hero use case, web-first, zero-retention as feature, drag-reveal sharing mechanic, monetize-on-first-use
- v2.2: Critique — model quality is go/no-go gate (validate first), kids/minors legal exposure unresolved, drag-reveal needs native video export not screen-record, Stripe content policy risk, latency needs async design from day one
- v3: Improved — adults-only v1 (cut legal risk), model validation as Step 0 before building, native GIF/video export as designed feature, async email fallback from day one, paywall moved to download step (show result free, charge for download)
