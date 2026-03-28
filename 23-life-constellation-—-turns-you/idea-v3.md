# Idea #23: Life Constellation (Improved)

## Version: v3
**Date:** 2026-03-28 12:29
**Status:** improved

## Original Idea
Life Constellation — turns your weekly movement patterns into a personal star map. Places you visit become stars (brighter = more visits), paths between them become constellation lines, home is always the brightest star. No input needed — it maps automatically in the background. Each week's constellation is saved to your collection and shareable. The emotional hook: seeing your week as a constellation reveals how small or wide your world actually was.

## What Changed and Why

- **Hardcoded fallback for "Always Allow" denial: "While Using App" is now a first-class mode, not a failure state.** The critique identified that 70–80% of users won't grant "Always Allow" on the initial prompt — iOS forces them to go into Settings manually after the first dialog. Instead of treating this as breakage, v3 explicitly supports "While Using App" mode with honest messaging: "Your constellation updates when you open the app — tap to check in." This is a check-in model (open app when you go somewhere interesting) as a legitimate fallback, not a hidden degraded state. Users who grant "Always Allow" get automatic tracking; those who don't get a lighter but functional experience. This eliminates the Achilles heel critique by removing the binary dependency on a single permission level.
- **Simulated first-week constellation in onboarding, not just an empty state label.** The critique nailed the Day 1 retention cliff. V3 ships with a pre-built "demo week" of sample data showing a beautiful, aspirational constellation — labeled clearly as "An example week." Users see exactly what they're building toward before their first real data exists. After granting location permission, the demo fades out and real tracking begins with the "Your first star will appear today" prompt. This converts the weakest moment of the app (first open, nothing there) into the strongest (here's what's coming).
- **Adaptive GPS clustering with two modes: City (50m) and Default (150m), auto-selected based on first detected location density.** The critique correctly flagged that a fixed 100m radius produces one giant star for all of Manhattan and phantom stars from GPS jitter in parking structures. V3 auto-detects approximate location density on first launch (tight GPS readings = City mode, spread readings = Default mode) and exposes this as a single toggle in Settings. This solves the "urban early adopter sees boring star map" problem without requiring the user to configure anything.
- **"Reserve your Year View" early IAP available from Day 1, alongside the standard post-4-weeks unlock.** The critique identified that by week 4, most users have churned — the paywall is optimized for the 10% of superfans who made it, while missing motivated early buyers. V3 shows a Year View preview card in the Archive from day 1 with a "Reserve Year View — unlock now, use it when you have data" CTA at full price. Buyers who want to commit when excitement is highest can do so. The standard post-4-weeks unlock still exists for the rest.
- **Explicit privacy reassurance step before the share sheet — baked into the flow, not an afterthought.** The critique identified that the target demographic (reflective, privacy-conscious) will hesitate before sharing a movement-derived image. V3 shows a one-second overlay before the share sheet: "No location data in this image — just your constellation pattern." One copy line that unlocks the viral loop for the exact users most likely to share.
- **Core Location API decision locked: Visits Monitoring is primary, Significant Location Change is the fallback.** The critique flagged this as an unresolved open question with major product impact. V3 commits: Visits Monitoring is the primary API (detects arrival/departure, best quality for constellation building). If the device or permission level doesn't support it, fall back to Significant Location Change with a clear in-app notice ("Your constellation may miss short stops"). This decision must be tested in week 1 with a real device before any UI work.

## Improved Description

Life Constellation is an iOS app that turns your weekly movement patterns into a personal star map — entirely on your device, no account, no data ever leaves your phone.

Places you visit become stars. The more you visit somewhere, the brighter it glows. Paths between places you visited in sequence that day become constellation lines. Home is always the brightest star.

**It works with the location access you're comfortable giving.** Grant "Always Allow" and the constellation builds automatically in the background — completely hands-off. Grant "While Using App" and the app updates your constellation whenever you open it — a light check-in habit instead of silent tracking. Either way, you get a weekly star map.

When you first open the app, you see a demo constellation — a beautiful example week showing exactly what you're building toward. Then you grant location access, the demo fades, and your first real star appears that day.

The constellation forms live throughout the week. Stars brighten as you return to familiar places. New stars appear when you go somewhere new. On Sunday night, the week seals and joins your archive. A tight, bright cluster of two or three stars isn't a failure — it's "rooted." The app never judges. It just shows you.

After a month, you have four constellations to compare. The premium Year View (one-time unlock) overlays all your weeks — the contrast between a wide-ranging stretch and a quiet one is the most emotionally striking thing the app can show. You can reserve Year View from day 1 when you're most excited about it, or unlock it later when you have data to see.

Privacy is the product promise. All GPS data is processed and stored on-device using Core Data. The share export is a pre-rendered image — no coordinates, no raw data, just your constellation.

## Why This Is Worth Building (Solo + AI)

The core stack — Core Location + Core Data + SwiftUI Canvas — is proven and AI-codeable with no backend infrastructure. The "Always Allow" fallback plan converts the biggest technical risk (permission denial) from an app-breaking failure into a defined product mode, eliminating the scenario where 80% of users get a broken experience. The demo constellation in onboarding solves the Day 1 retention cliff without any new technical complexity — it's just sample data displayed with the same renderer already being built.

## Solo MVP Scope

- **What's in v1:**
  - Background location tracking with two permission modes: "Always Allow" (automatic) and "While Using App" (check-in mode with explicit messaging)
  - Core Location Visits Monitoring as primary API, Significant Location Change as fallback
  - GPS clustering: City mode (50m radius) auto-detected, Default mode (150m radius), toggle in Settings
  - Constellation rendering: stars sized/brightened by visit count, lines by daily visit sequence
  - Home star auto-detection (most-visited overnight location)
  - Demo constellation in onboarding (pre-built sample week, labeled as example, fades on permission grant)
  - Live in-progress constellation view (current week)
  - Weekly seal mechanic (Sunday midnight, device local time)
  - Constellation archive (thumbnail grid, last 8 weeks)
  - Tap a star → place detail (visit count, first visit, brightness rank)
  - Share export with one-second privacy reassurance overlay before share sheet
  - One-time IAP: Year View — available from Day 1 as "reserve" + standard unlock after 4 sealed weeks
  - Settings: location mode badge (Always Allow / While Using / Denied with fix instructions), clustering mode toggle (City / Default), "all data on device" info, delete all data

- **What's out of v1:**
  - Android
  - Cloud sync / cross-device access
  - Apple Watch / HealthKit integration
  - User-defined star renaming
  - Push notifications (v1.1)
  - Social features / friend constellations
  - Custom themes or line styles
  - Google Places API (reverse geocoding only)
  - International travel / multi-home detection

- **Estimated build time with AI coding tools:** 3–4 weeks (week 1 is Core Location + data layer testing before any UI — non-negotiable)

## Open Questions

- Does Visits Monitoring fire reliably for stops under 5 minutes (coffee shop, ATM) on current iOS hardware, or do these always get missed? This is a week-1 test, not a later question.
- What does the app do when the user is traveling internationally and their home star would be on a different continent? Auto-detect new home, or freeze home until they return? Needs a decision before building home detection logic.
- Is the 3–4 week App Review turnaround for "Always Allow" location apps consistently that long in 2026, or is it improving? Worth checking developer forums before submission to set expectations.
- Does SwiftUI Canvas handle 50+ stars with glow animations at 60fps on an iPhone 13 without frame drops? Need a performance spike in week 1 alongside the Core Location test.

## Design Handoff

- **Screen 1: Onboarding — Demo Constellation** — full-screen dark canvas. Animated demo star map displayed with a beautiful pre-built sample constellation (7–10 stars, clearly labeled "An example week" in small italic text at top). Headline centered below: "Your life, mapped as stars." Sub-copy: "Places you visit become stars. Your constellation forms quietly in the background. Everything stays on your phone." Two CTAs stacked: primary "Start mapping" (triggers location permission dialog), secondary small link "How does tracking work?" (expands inline FAQ). No skip.

- **Screen 2: Onboarding — Permission Mode Explanation (shown after permission dialog)** — shown once, immediately after iOS permission dialog closes. Two-column card layout: left card "Automatic mode" (icon: star with orbit ring) — "Grant Always Allow in Settings for hands-off tracking." Right card "Check-in mode" (icon: star with tap indicator) — "Open the app when you visit somewhere new." Bottom CTA: "Got it." This screen only appears if user granted "While Using App" (not "Always Allow"). If Always Allow granted, skip to Home directly.

- **Screen 3: Home — Live Constellation (Current Week)** — primary screen. Dark canvas with star map. Stars: circles with glow rings, size + glow intensity proportional to visit count. Constellation lines: thin, slightly glowing, connecting in daily-visit-sequence order. Week label top-left ("Mar 24–30"). Day progress top-right ("Day 4 of 7"). Share button bottom-right. If "While Using App" mode: subtle banner below nav "Check-in mode — constellation updates when you open the app." Bottom nav: Constellation (active) | Archive. Empty state Day 1: single dim home star pulsing gently, "Your first star is home. More will appear today." (replaces "forming" label — more confident tone).

- **Screen 4: Star Detail Sheet (modal, bottom sheet)** — slides up on tap. Shows: place name (reverse-geocoded address or "Unknown place"), visit count this week, first visited date, brightness rank ("Your 2nd brightest star this week"), mini star visual showing glow intensity. Dismiss by swipe down or tap outside. No edit in v1.

- **Screen 5: Constellation Archive** — grid of past weekly constellations. Each cell: dark thumbnail with miniaturized star map, week date label below, visit count badge. Most recent first. Year View card pinned to top always — locked state shows blurred preview with "Reserve Year View" CTA (if < 4 weeks data) or "Unlock Year View" (if ≥ 4 weeks). Empty state if 0 sealed weeks: "Seal your first week to start your archive."

- **Screen 6: Expanded Past Constellation** — full-screen view of a past week. Non-interactive (no star tapping). Week date range at top. Stats bar: "X places · Y days active." Share button top-right. Swipe left/right to navigate weeks with peek animation. "← Older / Newer →" hint labels on first view.

- **Screen 7: Year View — Locked (Reserve State)** — accessible from Archive card from Day 1. Full-screen blurred demo of what Year View looks like (uses demo data, labeled "Example year"). Headline: "See your whole year as one constellation." Sub-copy: "All your weeks, overlaid. Reserve it now — it'll be waiting when you have data." Price badge. "Reserve Year View" primary CTA. "Restore purchase" link below. This is the early-buyer screen.

- **Screen 8: Year View — Locked (Unlock State)** — same layout as Reserve State but shown after 4+ sealed weeks exist. Blurred overlay now shows actual user data (blurred). Headline: "Your year is ready." CTA: "Unlock Year View." Same price.

- **Screen 9: Year View — Unlocked** — all weekly constellations overlaid in one coordinate space. Stars at same location merge and brighten. Constellation lines from all weeks shown faintly. Pinch-to-zoom. Stats bar: most-visited place of the year, most active week, total places. Color-tinted week differentiation (subtle).

- **Screen 10: Settings** — list view. Sections: Location (permission status badge — green "Automatic" / yellow "Check-in mode" / red "Location off" with "Fix in Settings" button), Tracking Mode (City 50m / Default 150m toggle with explanation), Data ("All data stored on this device" · local DB size · "Export my data"), Danger Zone ("Delete all my data" with confirmation dialog). No account, no login.

- **Screen 11: Share Export Card (generated asset)** — generated image: dark background, constellation centered with padding, week date label bottom-left in small sans-serif, "Life Constellation" wordmark bottom-right. Square 1:1. Generated locally via SwiftUI Canvas snapshot. Before share sheet opens: 1-second full-screen overlay "No location data in this image — just your constellation pattern" with a subtle checkmark icon, then native iOS share sheet opens automatically.

Key interactions:
- **Onboarding → Home:** Grant permission → if Always Allow, animate demo fading to empty Home with dim home star. If While Using App, show Permission Mode Explanation screen first, then Home.
- **Home → Star Detail:** Tap star → bottom sheet slides up; dismiss by swipe down or tap outside.
- **Home → Share:** Tap share icon → 1-second privacy reassurance overlay → iOS share sheet with pre-rendered image.
- **Home → Archive:** Tap Archive in bottom nav.
- **Archive → Year View card → Reserve/Unlock screen:** Tap pinned Year View card.
- **Archive → Expanded week:** Tap any week thumbnail → full-screen push.
- **Expanded → Adjacent week:** Swipe left/right.
- **Year View Reserve → Purchase:** Tap CTA → StoreKit 2 purchase flow → success lands on Year View Unlocked screen (with demo data if < 4 weeks, real data if ≥ 4 weeks).
- **Week seal:** Automatic Sunday midnight (device local time) — snapshot saved to Core Data, new canvas begins.

## Version History
- v1: Original idea
- v2.1: Critique — background location permission Achilles heel, Day 1 retention cliff, GPS clustering naive for urban users, Year View paywall timing wrong, share privacy friction
- v3: Improved — "While Using App" as first-class check-in mode, demo constellation in onboarding, adaptive GPS clustering (City/Default), Day 1 Year View reservation, privacy reassurance baked into share flow
