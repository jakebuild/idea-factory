# Idea #25: ShakeShot (Improved)
## Version: v2
**Date:** 2026-03-28 17:10
**Status:** improved

## Original Idea
ShakeShot Each morning you get one shake to generate an AI art portrait of your day, synthesized from your calendar events (meeting density, keywords, open vs packed schedule). Shake intensity affects art style — hard shake = chaotic, gentle = minimal. No reshakes. At end of day, add a one-word caption for what actually happened and share the prediction vs reality pair.

## What Changed and Why
- **Removed hard calendar dependency as entry barrier.** The critique called calendar permission the #1 drop-off cliff. v2 makes calendar optional — users can type 3 words describing their planned day instead. This opens the app to anyone without requiring OAuth trust upfront. Calendar connection becomes a "level up" feature for power users, not a gate.
- **Replaced unreliable shake gesture with a intentional tap-hold mechanic.** Shake intensity calibration across devices is a known nightmare. Instead: a hold-and-release button where hold duration = art style intensity (hold longer = more chaotic). Same analog metaphor, 100% reliable, no accelerometer variance hell. Simpler to build, easier to explain, works on any device.
- **Made the share card the product, not a feature.** The prediction vs reality split card is the viral loop. v2 invests everything here — the card is gorgeous by default, generated instantly, and the evening caption is prompted via a single well-timed push notification. Without this loop, the app is a forgettable art toy; with it, it's a daily ritual with social stakes.
- **Added explicit monetization from day one.** Free tier = one generation/day at standard quality. Premium ($2.99/mo) = HD quality + style packs + unlimited history. This maps cleanly to the API cost structure: premium users cover their own generation costs.
- **Scoped to iOS-only for v1.** Avoid multi-provider OAuth complexity. Apple Calendar is one integration. Google Calendar is a second platform, not a v1 feature.

## Improved Description
Every morning you get one chance to predict your day. Type 3 words (or connect Apple Calendar to auto-fill) and press-hold a button — the longer you hold, the more chaotic the art style. Release, and your AI portrait generates. No do-overs.

That night, one push notification arrives: "How did your day actually go?" You type one word. Done. Your morning prediction and evening reality become a shareable split card — a tiny time capsule of whether you saw it coming.

The mechanic is deliberately constrained: one portrait, one caption, one share. The scarcity is the point. Over time your history feed becomes a visual diary of your year in days — chaotic Mondays, quiet Fridays, the weeks where everything went sideways.

The social hook is the prediction vs reality pair. Did your "focused deep work" day turn into "chaos"? That tension is inherently shareable. The card does the storytelling.

## Why This Is Worth Building (Solo + AI)
The core loop — 3 words → hold button → portrait → evening caption → share card — is four API calls and one clean UI flow. A solo dev with AI coding tools can have this running in 2 weeks. The hold-duration mechanic eliminates the accelerometer rabbit hole entirely, and iOS-only with optional calendar means no OAuth hell in v1. The viral loop (shareable split cards) is the distribution strategy, so there's no need for a separate growth plan.

## Solo MVP Scope
- **What's in v1:**
  - Manual 3-word day description (no calendar required)
  - Hold-to-generate button with duration → style intensity mapping
  - AI portrait generation (DALL-E 3 or Flux via Replicate)
  - Evening push notification at user-set time
  - One-word evening caption input
  - Prediction vs reality share card (static image, native share sheet)
  - Local history feed (last 30 days)
  - Free tier: standard quality. Premium: HD + 3 style packs ($2.99/mo via RevenueCat)

- **What's out of v1:**
  - Calendar integration (any provider)
  - Social feed / following other users
  - Reshare to Stories natively
  - Web version
  - Android
  - Custom style packs beyond the 3 included
  - Collaborative / friend comparison features

- **Estimated build time with AI coding tools:** 2–3 weeks

## Open Questions
- Which image generation API gives the best quality-to-cost ratio for portrait-style art? (DALL-E 3 vs Flux Schnell vs Stable Diffusion)
- What's the right hold duration range? (e.g., 0–3 seconds = minimal, 3–6 = moderate, 6+ = chaotic) — needs calibration with real users
- Does the evening notification retention actually work, or do we need a streak mechanic to force the habit?
- What's the cost per generation at 1,000 DAU — does free tier + premium conversion rate cover it?
- Does "3 words" give enough prompt signal to generate interesting art, or do we need a richer input?

## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding** — 3 screens max. Screen 1: value prop with 2-3 example portrait pairs (prediction vs reality). Screen 2: notification permission request. Screen 3: "What time should we remind you tonight?" time picker. No calendar permission here.

- **Screen 2: Morning Home (pre-generate)** — Today's date at top. Three text input chips for day description words (e.g. "focused", "meetings", "deadline"). Below: a large circular hold button with label "Hold to reveal your day." Subtle intensity meter around the button edge that fills as you hold. No portrait yet. Premium badge/upgrade entry point in top-right corner.

- **Screen 3: Hold Interaction** — Full-screen takeover while holding. Button pulses/grows. Intensity ring fills from calm blue → fiery orange as hold duration increases. Haptic feedback at intensity thresholds. Release = generation starts immediately.

- **Screen 4: Generating State** — Animated loading state (ink bleeding into paper, or paint swirling). Duration label appears ("Gentle" / "Moderate" / "Chaotic"). Takes 3–8 seconds. Cannot be cancelled.

- **Screen 5: Portrait Reveal** — Full-screen portrait with animated entrance (scale-up fade). Intensity label in bottom corner. Three keyword chips showing the words used. "Save to Photos" and "Come back tonight" CTAs. No share button yet — card is incomplete without evening caption.

- **Screen 6: Evening Caption** — Push notification deep-links here. Portrait shown at top (50% of screen). "One word for how your day actually went:" prompt. Single large text input field. Submit button. Character limit: 1 word enforced.

- **Screen 7: Prediction vs Reality Card** — The shareable artifact. Left/top: morning portrait + 3 morning words. Right/bottom: large single evening word. Date at bottom. App name + subtle branding. "Share" button triggers native share sheet with card as image. "Save to Photos" secondary action.

- **Screen 8: History Feed** — Scrollable grid (2 columns) of completed day cards. Incomplete days shown as greyed portrait without evening word (tap to complete). Tap any card = full prediction vs reality view. Streak counter at top.

- **Screen 9: Settings** — Notification time preference. Premium upgrade/management. Connect Apple Calendar (optional, for future auto-fill). Account (if needed for cross-device sync). About/feedback.

**Key interactions:**
- **Hold-to-generate flow:** Home → hold button (intensity fills) → release → Generating → Portrait Reveal. The hold and reveal are the emotional peak of the app — they should feel weighty and satisfying.
- **Evening completion flow:** Push notification → deep link to Evening Caption screen → submit word → Portrait Reveal transitions to Prediction vs Reality Card → share or save.
- **History tap flow:** History Feed → tap card → full Prediction vs Reality Card view with share option.
- **Onboarding exit:** After notification permission + time set, user lands directly on Morning Home ready to generate.

## Version History
- v1: Original idea
- v1.1: Critique — calendar permission as drop-off cliff, shake gesture reliability, no monetization plan, evening retention problem
- v2: Improved — optional calendar replaced with manual 3-word input, shake replaced with hold-duration mechanic, share card elevated as core product, monetization defined upfront, scoped to iOS-only
