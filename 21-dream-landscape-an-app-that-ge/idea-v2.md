# Idea #21: Dream Landscape (Improved)
## Version: v2
**Date:** 2026-03-27 21:14
**Status:** improved

## Original Idea
Dream Landscape: An app that generates a unique surreal isometric pixel art city based on your sleep data. Weather in the city = how well you slept. Time of day = when you woke up. Buildings reflect your dream patterns. Check your 'dream city' each morning like checking the weather - your subconscious manifested as a world you can explore. No two cities are alike.

## What Changed and Why
- **Dropped HealthKit / wearable dependency in v1** — "dream patterns" data doesn't exist in consumer APIs. Instead, v1 uses a simple manual sleep log (bedtime, wake time, quality 1–5). This cuts Apple's permission hell, App Store entitlement overhead, and multi-device testing from the critical path entirely. HealthKit integration becomes a v2 upgrade once the concept is validated.
- **Replaced "exploration" with "living snapshot + history"** — "explore" implied a traversable game world, which is a multi-month solo project. Instead, the city is a daily generated portrait you can pinch-zoom, scroll through historically, and share. The emotional hook stays; the scope doesn't blow up.
- **Nailed the data-to-city mapping so it's legible, not arbitrary** — the critique called out that arbitrary mappings feel like noise art. Every visual element now maps to a real, documented sleep metric users can see and understand: sleep duration → city size (small town vs. metropolis), quality score → weather (sunny / overcast / stormy), REM % → color saturation (vivid vs. muted palette), wake time → lighting (golden dawn, noon, dusk), consistency streak → skyline height. The mapping legend is always one tap away.
- **Made shareability the core loop, not an afterthought** — the viral mechanic IS the product. Morning push notification → open app → see city → share to Instagram/X with one tap. Built the share mechanic before anything else in the feature priority order.
- **Pre-built pixel art tile library, rule-based assembly — no AI art generation** — consistent art requires a fixed tile set (licensable packs exist for ~$20–$50) assembled procedurally on an HTML5 canvas. No diffusion model dependency, no inconsistency night-to-night. The city looks the same aesthetic every day while the layout changes.

## Improved Description
Dream Landscape is a web app (mobile-first PWA) that turns your sleep log into a unique isometric pixel art city portrait, generated fresh every morning. You enter last night's sleep in 30 seconds — bedtime, wake time, and a simple quality rating — and the app renders your personal city: a cozy hamlet after a short night, a sprawling neon metropolis after deep restorative sleep. Sunny skies follow good rest; storms follow bad nights. The color palette shifts from muted grey-blues (low REM) to saturated purples and golds (high REM). The lighting angle reflects when you woke up.

Every city is saved to your History — a scrollable archive of every night as a thumbnail portrait. Watch your city evolve over a week of good sleep, or see it shrink after a rough patch. The share button generates a clean full-bleed export with subtle branding, designed to be dropped into Instagram Stories or posted to X. The city is the content. The app is the generator.

No wearable required. No App Store review. No HealthKit entitlements. Just open the browser, log your sleep, get your city.

## Why This Is Worth Building (Solo + AI)
A web PWA with manual input, a fixed pixel art tile library, and canvas-based rule-driven generation is buildable in under 3 weeks with AI coding tools — the isometric rendering logic, tile assembly rules, and share export flow are all well-documented patterns Claude can scaffold rapidly. The concept has a built-in viral loop (shareable art = free distribution) and a daily habit mechanic (morning check-in) that gives it genuine retention without requiring a team or ad budget. Once the web version proves people actually share it, adding HealthKit auto-fill is a contained v2 feature.

## Solo MVP Scope
- **What's in v1:**
  - Manual sleep input form (bedtime, wake time, quality 1–5)
  - Isometric pixel city generator: rule-based tile assembly on HTML5 canvas using a licensed pixel art tile pack
  - Legible mapping legend (what each visual means)
  - City History: scrollable archive with thumbnails and sleep score
  - Share export: full-res PNG with branding overlay, native share sheet
  - Morning push notification (PWA notification API)
  - Mobile-first PWA (installable, works offline after first load)

- **What's out of v1:**
  - HealthKit / Apple Health integration
  - Wearable APIs (Fitbit, Garmin, Oura)
  - Interactive exploration / camera movement
  - AI-generated art (no diffusion models)
  - Multiple art style themes / palette packs
  - Social features (following other users, feed)
  - Sleep coaching or actionable insights
  - Native iOS / Android app

- **Estimated build time with AI coding tools:** 2–3 weeks

## Open Questions
- Which pixel art tile pack to license — need isometric city tiles with enough variety (residential, commercial, nature, weather overlays) to feel non-repetitive across 30+ nights
- How many distinct city "states" to generate before users start seeing repeats — minimum viable variety without exponential complexity
- PWA notification reliability on iOS (Safari PWA notifications have quirks post-iOS 16.4) — need to test early
- Whether to use a simple parametric canvas renderer or an existing isometric JS library (e.g., IsometricJS) — pick before writing any generation code
- Pricing model: free with one-time unlock for History (>7 days), or free forever with optional "export hi-res" paid feature?

## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding** — purpose: explain the concept and get first sleep entry. Key elements: app name + tagline, 2–3 example city images (good sleep / bad sleep / average), CTA "Log last night's sleep", no signup required on first use.
- **Screen 2: Sleep Input Form** — purpose: log a night's sleep in 30 seconds. Key elements: bedtime time picker, wake time time picker, quality rating (1–5 stars or slider), optional notes field, "Generate My City" CTA button. Data: form state — bedtime, wake time, quality, notes.
- **Screen 3: Today's City (Main Hero)** — purpose: show today's generated city portrait. Key elements: full-bleed isometric city canvas (animated weather effects — rain particles, fog, sun rays via canvas), date + day label top-left, sleep summary bar (hours slept, quality badge), "View Details" tap target bottom, Share FAB button bottom-right. Data: generated city params, sleep duration, quality score, wake time.
- **Screen 4: City Details Panel** — purpose: explain what each visual element represents. Key elements: slide-up sheet over the city, mapping legend rows (e.g., "Sunny sky → 82 sleep score", "Neon metropolis → 7h 30m sleep", "Golden light → woke at 7:14am"), full sleep metrics (duration, efficiency, REM %, time in bed). Data: full sleep metrics, mapping legend config.
- **Screen 5: City History / Archive** — purpose: scroll back through past nights. Key elements: calendar strip at top (week view), grid of city thumbnails below (date label + sleep score badge), tap thumbnail → expands to full-screen hero view with details panel. Data: array of past entries — date, thumbnail URL, sleep score, city descriptor label.
- **Screen 6: Share Screen** — purpose: one-tap social export. Key elements: full-bleed city image preview, subtle "Dream Landscape" watermark bottom-right, date + sleep score badge overlay, "Share" button (native share sheet), "Copy Image" button, "Download" button. Data: hi-res rendered city PNG, date string, sleep score.
- **Screen 7: Settings** — purpose: preferences and notification config. Key elements: morning reminder time picker, toggle notifications on/off, "Clear all data" destructive action, link to mapping legend explainer, about/version info.

**Key interactions:**
- **Morning flow:** push notification at user's set wake time → tap → opens directly to Today's City screen (generated from last night's entry if already logged, otherwise redirects to Sleep Input first)
- **Log → Generate:** fill Sleep Input form → tap "Generate My City" → brief loading animation (1–2s canvas render) → transitions to Today's City hero screen
- **Details reveal:** tap anywhere on city or swipe up → City Details Panel slides up from bottom
- **Share flow:** tap Share FAB → Share Screen modal → tap Share button → native OS share sheet
- **History browse:** tap History tab → see archive grid → tap a thumbnail → full-screen past city view with swipe-up details
- **Zoom gesture:** pinch-to-zoom on city canvas (no camera movement, just zoom — gives "exploration" feel without game complexity)

## Version History
- v1: Original idea
- v1.1: Critique — vaporware data mapping, impossible "exploration" scope, no legible metaphor, HealthKit complexity trap
- v2: Improved — web PWA, manual sleep input, fixed tile library + rule-based generation, legible mapping legend, share-first loop, HealthKit deferred to v2
