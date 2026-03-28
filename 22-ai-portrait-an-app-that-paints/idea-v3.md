# Idea #22: AI Portrait (Improved)

## Version: v3
**Date:** 2026-03-28 07:09
**Status:** improved

## Original Idea
AI Portrait — An app that paints your daily selfie with AI in different artistic styles. Take a selfie every day → AI transforms it into art → Builds a collection of your year as art.

## What Changed and Why

- **Streak freeze added (1 per month, free)** — The v2 streak mechanic was a rage-quit machine. Missing one week while traveling resets months of perceived progress, which is a churn trigger not a retention feature. Duolingo has streak freezes for exactly this reason — steal it without apology. One free freeze per month; power users can earn extras by sharing portraits. Streak becomes a motivation tool rather than a punishment trap.

- **Reframed face consistency promise** — "Your face stays recognizable" is an aspirational claim IP-Adapter can't reliably deliver across abstract styles like ukiyo-e and pixel art. v3 reframes this as "artistic interpretations of you" — the product promise shifts from face fidelity to artistic identity. This is actually a stronger hook (it's art, not a mirror) and it's honest. Marketing copy changes accordingly; less "looks just like you" and more "see yourself through the lens of history's greatest art movements."

- **Free trial shortened to 3 portraits** — 5 weeks of free portraits is burning API budget on users who aren't converting. Week 2–5 non-converters cost $0.20–$0.50 with zero revenue. Cut to 3 free portraits (3 weeks). Users who are emotionally invested will convert by portrait 3; users who aren't won't convert by portrait 5 either.

- **Price raised to $4.99/month / $24.99/year** — $2.99/month invites the "I could just use ChatGPT" mental comparison. $4.99/month is still impulse-buy territory but signals a premium, curated product. You need half as many subscribers to hit the same revenue. The demographic paying for an AI portrait journal will pay $5 — test at $4.99 first, drop to $3.99 only if conversion rate is unacceptably low.

- **Sharing as the acquisition strategy, not an afterthought** — v2 had no answer for "how does user #1–100 find this." v3 makes sharing first-class: every portrait result screen leads with an "Share to Instagram Story" button that exports a branded portrait card (subtle watermark + app name). This is the only realistic organic growth loop for a solo-dev app with zero marketing budget. The watermark is the ad.

- **"Inspiration portrait" themed push notifications** — Generic weekly reminders get silenced. Instead: "This week: paint yourself like Van Gogh's Starry Night period 🌙" or "This week: see yourself as a Klimt gold-phase portrait ✨". The notification becomes the creative hook that earns the tap, not just a poke.

## Improved Description

**AI Portrait Weekly** is an artistic portrait journal: once a week, you take a selfie and an AI renders an artistic interpretation of your face in a curated style — oil painting, watercolor, anime, ukiyo-e, pixel art, Renaissance, cyberpunk, Klimt, and more. Each portrait joins a growing gallery that becomes a visual record of your artistic identity over time.

The technical approach: you take a reference selfie on first use (stored locally, never sent as biometric data). Each week, that reference passes to fal.ai's FLUX IP-Adapter pipeline, which generates an artistic interpretation in the chosen style. The product promise is not "perfect face matching" — it's "see yourself through the lens of history's greatest art movements." Faces will be recognizable in realistic styles and impressionistic in abstract ones. That's a feature, not a bug.

The habit loop: themed weekly push notification ("This week: paint yourself like Monet's Water Lilies period") → open app → tap camera → style pre-selected from the notification or pick your own → watch generation (30s) → see your portrait → share to Instagram Story with branded card → gallery updated. Streak counter on home screen. One streak freeze per month so a vacation doesn't undo three months of work.

Monetization: 3 free portraits, then $4.99/month or $24.99/year. Paywall appears after the 3rd portrait result — you've already seen what the app does and you're three weeks invested. Sharing is always free (it's the growth mechanic, not a premium feature).

No facial embeddings stored server-side. Images are processed transiently and the result URL returned; the reference selfie lives only on the device.

## Why This Is Worth Building (Solo + AI)

The core loop is Expo + fal.ai API + RevenueCat + local storage — all established, well-documented integrations that AI coding tools scaffold fast. At $4.99/month with ~$0.50/month COGS per user, you hit break-even at 4 paying users and meaningful side income at 200+. The share-to-Instagram organic loop is the only acquisition strategy a solo dev can realistically run, and portrait art is inherently shareable — it's the right bet.

## Solo MVP Scope

- **What's in v1:**
  - Reference selfie capture (stored locally, one-time setup)
  - Weekly portrait generation via fal.ai FLUX IP-Adapter
  - 8 curated styles (oil painting, watercolor, anime, pixel art, ukiyo-e, sketch, Renaissance, cyberpunk)
  - Gallery view (grid) with week labels
  - Streak counter with 1 free freeze per month
  - 4-week milestone screen with shareable collage and style unlock
  - Themed weekly push notifications (style-named, not generic)
  - Hard paywall after 3rd portrait (RevenueCat)
  - Share to Instagram Story — branded portrait card with app watermark
  - Settings: notification toggle, subscription status, reference selfie reset

- **What's out of v1:**
  - Face learning / persistent identity modeling (cut entirely)
  - Daily portrait cadence
  - 12-week milestone (add in v1.1 if retention data supports it)
  - Social feed or following other users
  - In-app live style preview
  - Style voting / community-voted weekly style
  - Web app / desktop version
  - Earned streak freezes (only free monthly freeze in v1)
  - Milestone collage for 12-week (scope risk — add after launch)

- **Estimated build time with AI coding tools:** 2–3 weeks (Week 1: camera + fal.ai API + basic gallery. Week 2: streak + freeze mechanic + paywall + RevenueCat. Week 3: share card export + themed notifications + 4-week milestone + polish + App Store submission.)

## Open Questions

- What is the actual IP-Adapter output quality on 5–10 real faces across oil painting, ukiyo-e, and pixel art? Must test before writing any marketing copy — this is the product's core promise and needs empirical validation.
- Does Apple's App Store privacy label require a face-data disclosure for a reference selfie that is processed transiently by fal.ai (never stored server-side)? Needs legal/policy research before submission.
- What's week-8 retention for a weekly-cadence app in practice? If benchmarks are terrible (< 20%), consider adding an optional "bonus portrait" for engaged users mid-week to increase touch frequency without inflating COGS.
- Can the branded share card be generated cleanly in React Native without Canvas API pain? Consider server-side image compositing via a simple Cloudflare Worker instead.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding / Welcome** — Value prop ("See yourself as art. Every week."), style sample montage showing 4–6 artistic interpretations of the same face side-by-side (demonstrating range, not face fidelity), CTA "Start Your Portrait Journal". No sign-up gate on first screen.

- **Screen 2: Reference Selfie Setup** — One-time screen. Explains that a reference selfie helps the AI create your artistic interpretations. Front-facing camera, capture + retake + confirm. Privacy note: "Stored only on your device — never uploaded as personal data." Shown once; re-accessible via Settings.

- **Screen 3: Home / Gallery** — Primary screen. Top bar: streak counter ("7-week streak 🔥") + streak freeze button (visible when streak > 0, shows "1 freeze available"). Prominent CTA "This Week's Portrait" (disappears after weekly portrait is done, replaced by "See your latest"). Below: grid of all past portraits (thumbnail, week label, style tag). Empty state: illustrated placeholder with "Your gallery starts here."

- **Screen 4: Style Picker** — Shown after tapping "This Week's Portrait" CTA. Top: "This week's inspiration" banner showing the themed suggestion from the push notification. Horizontal scroll of style cards (style name + sample thumbnail). "Surprise Me" card first. Locked styles greyed with "Unlock at 4 weeks" label. Tapping any style goes straight to Loading.

- **Screen 5: Generation Loading** — Full-screen. Animated shimmer or lottie placeholder in portrait aspect ratio. Copy: "Painting you as [Style Name]..." Estimated wait: ~30 seconds. Shows one past portrait or a famous artwork in the selected style as ambient background while waiting.

- **Screen 6: Portrait Result** — Full-screen generated portrait. Style label + date overlay at bottom. Primary action: "Share to Instagram Story" (exports branded card). Secondary actions: Save to Camera Roll, Add to Gallery (auto-added). Subtle app watermark on exported share card. Swipe up or "Done" to go to Gallery. If this is the 3rd portrait, Paywall appears as a bottom sheet after 2 seconds.

- **Screen 7: Portrait Detail** — Full-screen single portrait view. Date, style name, week number. Toggle: "See original selfie" shows crossfade or split-screen comparison. Share button (same branded card export). Download button.

- **Screen 8: Milestone Screen (4-week)** — Triggered after the portrait that completes a 4-week streak. Full-screen takeover, celebratory confetti. Auto-generated 2×2 collage of the four portraits. Headline: "Your First Month as Art." CTA: "Share your month" exports the collage card. Secondary: "Unlock your next style" reveals the newly unlocked style. Can't be dismissed without tapping an action.

- **Screen 9: Paywall / Subscribe** — Bottom sheet appearing after 3rd portrait result. Clean two-option layout: $4.99/month and $24.99/year ("Best Value" badge, saves 58%). Shows "You have 3 portraits — keep building your gallery." One CTA per plan. Restore purchases link at bottom. Dismissable (no hard gate — let them use the app but remind them on each generation after limit).

- **Screen 10: Settings** — Notification toggle + time picker (for weekly reminder), streak counter (read-only), streak freeze status ("1 freeze available this month"), subscription status (plan name + renewal date), "Reset reference selfie" (re-runs Screen 2), App Store privacy note, version number.

**Key interactions:**
- Themed push notification ("This week: paint yourself like Monet 🎨") → deep links directly to Style Picker with that style pre-highlighted
- Home CTA → Style Picker → Loading → Portrait Result → (if 3rd portrait) Paywall bottom sheet → Gallery updated
- Portrait Result "Share to Instagram Story" → generates branded portrait card → native share sheet → Instagram
- Long-press any gallery portrait → quick actions: View Detail / Share / Delete
- 4-week Milestone Screen auto-appears full-screen after portrait completing the streak; requires tap to dismiss
- Streak freeze button on Home (one tap) → confirmation modal ("Use your monthly freeze? You have 1 remaining") → streak preserved, freeze count decrements
- Paywall is a bottom sheet (not a blocking modal) — user can dismiss it but it reappears on each generation attempt after the 3 free portraits are used

## Version History
- v1: Original idea
- v1.1: Critique — vaporware face learning, no monetization, no habit mechanics, legal landmines
- v2: Improved — weekly cadence, hard paywall from day one, milestone rewards at 4/12 weeks, reference-image style transfer
- v2.1: Critique — streak resets are churn triggers, IP-Adapter consistency overpromised, $2.99 perceived value weak, no acquisition strategy, 5-week free trial too long
- v3: Improved — streak freeze mechanic, honest "artistic interpretation" framing, 3-portrait free trial, $4.99/month price, sharing as first-class acquisition loop, themed push notifications
