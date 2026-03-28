# Idea #23: Life Constellation (Improved)

## Version: v2
**Date:** 2026-03-28 12:24
**Status:** improved

## Original Idea
Life Constellation — turns your weekly movement patterns into a personal star map. Places you visit become stars (brighter = more visits), paths between them become constellation lines, home is always the brightest star. No input needed — it maps automatically in the background. Each week's constellation is saved to your collection and shareable. The emotional hook: seeing your week as a constellation reveals how small or wide your world actually was.

## What Changed and Why

- **Privacy-first, on-device only as the headline feature, not a footnote.** The critique identified trust as a near-fatal concern for the exact demographic this targets — reflective, privacy-aware people. "Your constellation never leaves your phone" is now the core brand promise, not buried in settings. This also eliminates cloud infrastructure complexity (no auth, no sync server, no storage costs), which is critical for solo dev scope.
- **Constellation forms live throughout the week, solving the daily engagement gap.** The critique nailed a structural flaw: if the map only "completes" on Sunday, there's nothing to look at Monday–Saturday. Now the constellation is always live and in-progress — stars brighten in real time as you revisit places, new stars appear as you go somewhere new. Sunday becomes a "seal this week" moment, not the only moment. This makes the app worth opening daily without adding any input burden.
- **Constellation line logic locked to visit sequence within a day.** The critique identified this as an unresolved rabbit hole. Fixed: lines connect places in the order you visited them within each day, reset each morning. Simple, defensible, visually interesting — no proximity guessing, no arbitrary clustering algorithms beyond basic GPS radius grouping.
- **"Rooted" framing for repetitive constellations.** The critique flagged that most users (commuters, routine-driven people) will see a 2–3 star constellation every week and feel bad. Copy and visual design now frames a tight constellation as "rooted" with its own aesthetic — a dense, bright cluster is beautiful too. This cuts churn from the majority use case.
- **Monetization built in: one-time unlock for the Year View.** The critique noted no visible monetization. Year View (all 52 weeks overlaid) is the premium feature — it's the most emotionally powerful screen and a natural upsell after a few weeks of use. One-time IAP, no subscription pressure.
- **Cut: Android support, cloud sync, Apple Watch integration, user-defined star renaming in v1.** Scoping hard to iOS-only, on-device. Every cut feature eliminates a week of complexity.

## Improved Description

Life Constellation is an iOS app that turns your weekly movement patterns into a personal star map — entirely on your device, no account needed, no data ever leaves your phone.

Places you visit become stars. The more you visit somewhere, the brighter it glows. Paths between places you visited in sequence that day become constellation lines. Home is always the brightest star. No input required — it runs quietly in the background using iOS location services.

The constellation forms in real time throughout the week, so you can open the app any day and see your week taking shape. Stars brighten as you return to familiar places. New stars appear when you go somewhere new. On Sunday night, the week seals and joins your archive.

Each sealed constellation is saved to your collection. After a month, you have four constellations to compare. After a year, the premium Year View overlays all 52 weeks — the contrast between a wide-ranging month and a quiet one is the most emotionally striking thing the app can show.

The emotional hook is dual: a sprawling constellation shows you that your world was wide this week. A tight, bright cluster of two or three stars isn't a failure — it's "rooted." The app never judges. It just shows you.

Privacy is the product promise. All GPS data is processed and stored on-device using Core Data. The constellation rendering is local. The share export is a pre-rendered image — no coordinates, no raw data leaves the phone.

## Why This Is Worth Building (Solo + AI)

The core technical stack is proven and AI-codeable: Core Location + Core Data + SwiftUI Canvas for rendering. A solo dev with AI tools can generate the constellation rendering, the GPS clustering logic, and the archive UI in 3 weeks, leaving a week for App Review submission and iteration. The on-device-only constraint eliminates the hardest infrastructure problems (auth, sync, backend) that would otherwise double the build time.

The shareable weekly export is a built-in distribution loop that costs nothing to maintain, and the one-time IAP Year View gives a clear monetization path without requiring a subscription backend.

## Solo MVP Scope

- **What's in v1:**
  - Background location tracking (iOS, "Allow all the time" permission)
  - GPS clustering into place nodes (simple radius-based: visits within 100m = same star)
  - Constellation rendering: stars sized/brightened by visit count, lines by daily visit sequence
  - Home star auto-detection (most-visited location, or manual set on first run)
  - Live in-progress constellation view (current week, always up to date)
  - Weekly seal mechanic (Sunday midnight auto-seals the week)
  - Constellation archive (last 8 weeks, thumbnail grid)
  - Tap a star → place detail (visit count, first visit)
  - Share export (pre-rendered square image for Instagram/Stories)
  - Settings: location permission status, "all data on device" info, delete all data
  - One-time IAP: Year View (unlock after 4 weeks of data)

- **What's out of v1:**
  - Android (background location is even harder, doubles build time)
  - Cloud sync / cross-device access
  - Apple Watch / HealthKit integration
  - User-defined star renaming
  - Push notifications (add in v1.1 after core loop validates)
  - Social features / friend constellations
  - Custom constellation line styles or themes
  - Google Places API integration (use reverse geocoding only, no external dependency)

- **Estimated build time with AI coding tools:** 3–4 weeks

## Open Questions

- What is the actual background location permission grant rate for iOS apps in this category? Need to check if a "When in use + significant location change" fallback is sufficient or if the experience degrades too much.
- How tight should the GPS clustering radius be? 100m works for most urban use cases but fails in dense city centers (adjacent restaurants become one star). Need to test with real data early.
- Does Core Location's significant-change monitoring give enough data points for a meaningful constellation, or does it miss short stops? May need to test "visits monitoring" API instead.
- Is a one-time IAP viable on the App Store in 2026 for a utility app, or has the market shifted fully to subscription? Worth validating before building the paywall.
- How do you handle the first week UX — a user with one day of data has almost no constellation. Need a "your constellation is forming" empty state that doesn't feel broken.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding / Permission Request** — single full-screen view, dark background with animated star field slowly forming. Headline: "Your life, mapped as stars." Sub-copy: "Places you visit become stars. Your constellation forms quietly in the background. Everything stays on your phone." Single CTA button: "Start mapping." Triggers iOS background location permission dialog. No skip option.

- **Screen 2: Home — Live Constellation (Current Week)** — the main screen. Dark canvas with star map rendered. Stars are circles, size + glow intensity = visit count. Constellation lines are thin, slightly glowing. Week label top-left ("Mar 24–30"). Progress indicator top-right ("Day 4 of 7"). Share button bottom-right. Tap any star to open detail sheet. Bottom nav: Constellation (active) | Archive.

- **Screen 3: Star Detail Sheet (modal)** — slides up from bottom when tapping a star. Shows: place name (reverse-geocoded or "Unknown place"), address, visit count this week ("Visited 4 times"), first visited date, small mini-star visual showing brightness. "This is your [rank] brightest star this week." Dismiss by swipe down.

- **Screen 4: Constellation Archive** — grid of past weekly constellations, each as a small dark-background thumbnail with the star map miniaturized. Week label below each. Most recent first. Tap to expand. Bottom nav: Archive (active) | Constellation. Empty state if fewer than 2 weeks of data.

- **Screen 5: Expanded Past Constellation** — full-screen view of a historical week. Same rendering as Home but non-interactive (no star tapping). Week date at top. Stats bar at bottom: "X places visited · Y days active." Share button. Swipe left/right to navigate between weeks.

- **Screen 6: Year View (Premium / Locked)** — accessible from Archive after 4 sealed weeks. All constellations overlaid in one coordinate space. Stars at the same location merge and grow brighter. Faint lines from all weeks. Shows movement range visually. Locked overlay if IAP not purchased: "See your year constellation — One-time unlock." After purchase: fully interactive.

- **Screen 7: Settings** — list view. Sections: Location (permission status badge, "Required for tracking"), Data ("All data stored on this device only" with local DB size), Notifications (off by default, toggle for weekly "your constellation sealed" nudge — v1.1), Danger Zone ("Delete all data" destructive button). No account, no login.

- **Screen 8: Share Export Card** — not a full screen, but a generated image: dark background, constellation centered, week label bottom-left, subtle app wordmark bottom-right. Square crop (1:1) for Instagram. Generated locally, opened via native iOS share sheet. No upload step.

Key interactions:
- **Onboarding → Home:** after granting location permission, user lands on Home with "Your constellation is forming..." empty state that shows a single dim star (home, auto-detected from overnight location)
- **Home → Star Detail:** tap any star → bottom sheet slides up; dismiss by swipe or tap outside
- **Home → Share:** tap share button → iOS share sheet opens with pre-rendered square image attached
- **Home → Archive:** tap Archive in bottom nav
- **Archive → Expanded:** tap any week thumbnail → full-screen expanded view
- **Archive → Year View:** tap "Year View" card (pinned to top of archive after 4 weeks) → locked or full view depending on IAP state
- **Expanded → next/prev week:** swipe left/right
- **Week seal:** automatic on Sunday at midnight — current week snapshot is saved to Core Data, new week begins with fresh canvas

## Version History
- v1: Original idea
- v1.1: Critique — background location permission risk, unclear constellation line logic, no daily engagement loop, privacy trust gap, no monetization
- v2: Improved — on-device privacy as headline, live forming constellation for daily engagement, locked line logic (daily visit sequence), "rooted" framing for routine users, one-time IAP Year View
