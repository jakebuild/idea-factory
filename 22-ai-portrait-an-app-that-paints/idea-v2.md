# Idea #22: AI Portrait (Improved)

## Version: v2
**Date:** 2026-03-28 07:05
**Status:** improved

## Original Idea
AI Portrait — An app that paints your daily selfie with AI in different artistic styles. Take a selfie every day → AI transforms it into art → Builds a collection of your year as art.

## What Changed and Why

- **Dropped "face learning" entirely** — It was vaporware. v2 uses a single reference selfie per session as an IP-Adapter input for style transfer. No persistent facial embeddings, no biometric storage, no GDPR/BIPA liability. Process on the fly, discard after generation. This is technically honest and shippable in 2 weeks.

- **Shifted from daily to weekly portrait** — Daily felt aspirational but killed unit economics ($0.10/image × 365 = $36.50/user/year before any other costs). Weekly drops API costs 7× and is actually achievable as a habit. "Your face as art, every week" is still a strong hook with far better retention math.

- **Monetization from day one with a hard paywall** — Free tier: 5 portraits. Then $2.99/month or $14.99/year. At $0.10/generation and weekly cadence, a paying user costs ~$0.50/month in API costs — leaving real margin. No burn-and-pray freemium.

- **Added streak + milestone rewards** — Miss a week, streak resets. Hit 4 weeks, unlock a new style pack. Hit 12 weeks, unlock a shareable "Season Gallery" card. The emotional payoff is no longer 365 days away — it's 30 days away. This is the actual retention mechanic the v1 was missing.

- **Explicit differentiation from Lensa** — Lensa's Magic Avatars was a one-shot batch job (upload 20 photos, wait, get 50 avatars). This is a longitudinal portrait journal — one per week, same face over months, different styles, watching yourself change. The gallery IS the product, not the individual image.

## Improved Description

**AI Portrait Weekly** is a portrait journal app: once a week, you take a selfie, pick an artistic style (or let the app assign one), and an AI renders a full painting of your face in that style — oil painting, watercolor, anime, ukiyo-e, pixel art, Renaissance, etc. Each portrait joins your personal gallery, which grows week by week.

The technical approach: you upload a reference selfie on first use (stored locally, never sent to a server as biometric data). Each week, that reference image is passed to fal.ai's FLUX IP-Adapter pipeline as a style-transfer reference, ensuring your face stays recognizable across wildly different styles without any persistent identity modeling.

The habit loop: weekly push notification → open app → tap camera → pick/confirm style → watch generation (30s) → see your portrait → gallery updated. Streak counter on home screen. At 4 weeks: unlock a style pack. At 12 weeks: shareable "Season of You" collage auto-generated.

Monetization: 5 free portraits, then $2.99/month or $14.99/year. One pricing screen, no dark patterns. The paywall hits after the user has already seen 5 portraits and is emotionally invested in their gallery.

No facial embeddings stored server-side. Images are processed and the result URL is returned; the original selfie lives only on the user's device. Privacy-safe by architecture.

## Why This Is Worth Building (Solo + AI)

The core loop — selfie → API call → styled image → gallery display — is standard Expo + fal.ai work that AI coding tools can scaffold in days. Weekly cadence kills the API cost problem and makes streak mechanics achievable. The paywall-from-day-one model means you're profitable at ~6 paying users/month, so there's no pressure to scale before the product is proven.

## Solo MVP Scope

- **What's in v1:**
  - Reference selfie capture (stored locally on device)
  - Weekly portrait generation via fal.ai FLUX IP-Adapter
  - 8 artistic styles (oil painting, watercolor, anime, pixel art, ukiyo-e, sketch, Renaissance, cyberpunk)
  - Gallery view (grid + list) with week labels
  - Streak counter on home screen
  - 4-week and 12-week milestone screens with shareable collage
  - Weekly push notification reminder
  - Hard paywall after 5 portraits (RevenueCat for subscription management)
  - Settings: notification toggle, subscription status, reference selfie reset

- **What's out of v1:**
  - Face learning / persistent identity modeling (cut entirely)
  - Daily portrait cadence (too expensive, too demanding)
  - Social feed or following other users
  - In-app style previews with live camera
  - Style voting / community-voted weekly style
  - Year-end 365-portrait gallery (mid-term milestones replace this)
  - Web app / desktop version

- **Estimated build time with AI coding tools:** 2–3 weeks (1 week core loop + API, 1 week gallery + milestones, 3–5 days paywall + notifications + polish)

## Open Questions

- Which fal.ai model gives the best face consistency across styles without fine-tuning — FLUX IP-Adapter or SDXL with ControlNet face? Need a test run before committing.
- Does Apple App Store flag IP-Adapter reference image flows as "biometric data collection"? Review the privacy nutrition label requirements before submitting.
- Is weekly frequency enough for retention, or does the habit loop die between sessions? Consider an optional "bonus portrait" mechanic for engaged users.
- RevenueCat vs. direct StoreKit for subscription — RevenueCat adds complexity but saves time on receipt validation and cross-platform; worth it for solo dev.

## Design Handoff

- **Screen 1: Onboarding / Welcome** — Value prop ("Your face as art. Every week."), style sample montage showing 4–6 style examples using a single face, CTA "Start Your Portrait Journal". No sign-up gate on first screen.

- **Screen 2: Reference Selfie Setup** — One-time screen. Explains that a reference selfie helps the AI keep your face consistent. Front-facing camera, capture + retake, confirm. Privacy note: "Stored only on your device." Shown once; can be reset in Settings.

- **Screen 3: Home / Gallery** — Primary screen. Top: streak counter ("Week 7 streak") + "Today's Portrait" CTA button if current week not done. Below: grid of all past portraits (thumbnail, week label, style tag). Empty state: "Your gallery starts with your first portrait."

- **Screen 4: Style Picker** — Shown after tapping "This Week's Portrait" CTA. Horizontal scroll of style cards (name + sample thumbnail). "Surprise me" option. Locked styles shown greyed with "Unlock at 4 weeks" label.

- **Screen 5: Generation Loading** — Full-screen. Animated placeholder (shimmer or lottie). "Painting your portrait..." copy. Shows estimated wait (30s). Displays a random past portrait or style sample while waiting to reduce perceived wait time.

- **Screen 6: Portrait Result** — Full-screen generated portrait. Style label + date overlay at bottom. Action bar: Save to Camera Roll, Share, Add to Gallery (auto-added). Swipe up to go to gallery.

- **Screen 7: Portrait Detail** — Full-screen view of one portrait. Date, style, week number. Toggle: "See original selfie" (shows split-screen or crossfade). Share/download buttons.

- **Screen 8: Milestone Screen** — Triggered at 4-week and 12-week thresholds. Shows auto-generated collage of portraits from that period. "Share your Season" button generates a shareable image card. Unlock announcement for new styles at 4-week mark.

- **Screen 9: Paywall / Subscribe** — Shown after 5th portrait is generated. Clean single screen: two options ($2.99/month, $14.99/year with "Best Value" badge). Shows user's current portrait count. One CTA button per plan. Restore purchases link at bottom.

- **Screen 10: Settings** — Notification toggle + time picker for weekly reminder, current streak, subscription status (plan name + renewal date), "Reset reference selfie" option (re-runs Screen 2), privacy note.

**Key interactions:**
- Weekly push notification → deep link directly to Style Picker (skip home screen)
- Home screen "This Week's Portrait" CTA → Style Picker → Loading → Result → back to Home with new portrait in gallery
- Long-press any gallery portrait → quick actions: View Detail, Share, Delete
- 4-week milestone auto-shown after portrait is added that completes the streak; can't be skipped (celebratory moment)
- Paywall appears inline after generation completes on 5th portrait (not as a gate before generation — let them see what they're paying for)
- Style Picker "Surprise me" picks a random available style and skips confirmation (one tap, go)

## Version History
- v1: Original idea
- v1.1: Critique — vaporware face learning, no monetization, no habit mechanics, legal landmines
- v2: Improved — weekly cadence, hard paywall from day one, milestone rewards at 4/12 weeks, reference-image style transfer (no biometric storage), clear differentiation from Lensa
