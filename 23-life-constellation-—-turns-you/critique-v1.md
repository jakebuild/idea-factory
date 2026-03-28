# Critique v1 — Idea #23: Life Constellation

**Verdict:** WORTH BUILDING
**Score:** 7/10

## What's Actually Good

- The visual metaphor is genuinely beautiful and emotionally resonant. "Your week as a star map" is something people will screenshot and share, which is free marketing.
- Zero manual input is the right call — passive tracking is the only way lifestyle apps retain users. The moment you ask for input, 80% churn immediately.
- Home as brightest star is a poetic, instantly understood anchor that makes the map readable without a legend.
- "Shareable weekly constellation" is a native viral loop — it's designed for Instagram Stories and Twitter before you even think about it.
- The emotional hook is real and differentiated: the existential gut-punch of seeing a tiny, repetitive constellation versus a sprawling one is something users will actually feel. That's rare.
- Comparable to Spotify Wrapped's formula: make personal data beautiful, make it shareable, watch it spread.

## Brutal Feedback

- **Background location tracking is a minefield.** iOS and Android both make this increasingly painful. iOS will show a blue bar or location permission prompt constantly. Android 10+ requires explicit "Allow all the time" permission — most users will deny it. Apple App Review scrutinizes background location use heavily. One policy change and your core feature dies.
- **The "constellation" visual is conceptually cool but technically ambiguous.** How do you connect stars into constellation *lines*? If you visit 15 places in a week, do you draw lines between them in visit order? That's just a timeline with dots. If you draw lines based on proximity, that's arbitrary. If you connect frequently co-visited places, that's more interesting but harder to explain and build.
- **Location clustering is an unsolved UX problem for this use case.** "Home" is easy. But if you visit a Starbucks twice and a different Starbucks once, are those one star or three? Do you use Google Places to resolve addresses? That's an API dependency with cost and reliability risk.
- **The emotional hook works for ~20% of users.** People with wide, interesting lives will love it. People who commute to the same office every day will see a depressing two-star constellation every week. That's most users. Do they churn? Do they feel bad? This is not a small concern.
- **Weekly cadence might be too sparse for engagement.** If the constellation only "completes" once a week, what does the user look at every day? There's no daily engagement loop unless you add one, and adding one complicates the passive-simplicity promise.
- **Privacy is a serious trust problem.** You're asking users to hand over their complete real-world movement data. Even if you handle it perfectly, a significant chunk of your target demographic (the reflective, introspective person this appeals to) will be spooked. You need a bulletproof "never leaves device" story or you're dead in the press.
- **The history collection ("each week saved") has no clear purpose beyond nostalgia.** What do I *do* with 52 constellations? Compare them? There's no interaction designed around this yet.
- **No monetization angle is visible.** Subscription? One-time purchase? Free with export paywalled? This matters for App Store positioning and solo dev sustainability.

## Key Questions

- What happens to users with boring, repetitive movement patterns? (The majority of your user base)
- How do you handle location clustering — is "Starbucks on 5th" the same star as "Starbucks on 8th"?
- On-device processing only, or cloud sync? If cloud: who sees this data and how do you communicate that?
- How do you draw constellation lines — visit order, proximity, frequency, or user-defined?
- What does daily engagement look like while a week's constellation is still forming?
- iOS background location permission approval rate — have you looked at benchmarks? It's around 30-40%.

## Suggestions

- **Lead with "on-device only" as a feature, not a footnote.** Make privacy the headline. "Your constellation never leaves your phone" is a selling point that defeats the trust problem.
- **Show the constellation *forming* in real time as the week progresses.** This solves the daily engagement problem — users can watch stars brighten as they revisit places. Completion on Sunday becomes a moment.
- **Add a "year constellation" view** that superimposes all 52 weeks. The delta between a lockdown year and a travel year would be staggering. This is your premium upsell.
- **Give the boring constellation dignity.** Don't make a two-star week feel like a failure. Frame it as "steady" or "rooted." Copywriting matters here.
- **Consider Apple Watch / Health app integration** as an alternative to raw GPS — it may ease permission friction.
- **Constellation line logic should be "visit sequence within a day"** — connect places you visited in the order you visited them. Simple, defensible, visually interesting.

## Solo Dev Reality Check

- **Can one person ship this in 2-4 weeks with AI coding tools?** MAYBE — a basic iOS app with Core Location, weekly snapshot rendering, and share sheet is doable in 3-4 weeks. The constellation rendering can be done with SwiftUI Canvas or a simple WebGL/Canvas layer. The hard parts are background location reliability and location clustering, which could each eat a week alone.
- **Biggest solo complexity traps:**
  - Background location permissions: different behavior across iOS versions, requires entitlements, App Review scrutiny adds weeks to shipping
  - Location deduplication/clustering: building even a naive "these GPS coords are the same place" system is 3-5 days of edge case debugging
  - Constellation line algorithm: sounds simple, becomes a rabbit hole when you have 15 places and need to make it look beautiful
  - Cross-platform ambition: picking iOS-only is the right call — Android background location is even harder; committing to both solo is a mistake
  - Offline-first architecture: if you go cloud sync, you inherit auth, storage, sync conflicts; if you go on-device only, you lose cross-device access and need to manage local DB carefully

## Design Handoff

### Screens Needed

1. **Onboarding / Permission Request** — single screen explaining the concept, asks for background location permission. Visual: animated star map forming. CTA: "Start mapping my life."
2. **This Week's Constellation (Home Screen)** — the main live constellation view. Stars visible with brightness indicating visit count. Constellation lines drawn. Week date range shown. Progress indicator (e.g., "Week 4 of 52"). Share button prominent.
3. **Star Detail Sheet (tap a star)** — bottom sheet showing: place name/address, visit count this week, first visited date, how bright it is vs. last week.
4. **Constellation Archive / Collection** — grid or scroll of past weekly constellations, each as a small thumbnail star map. Tap to expand. Filter by month/year.
5. **Expanded Past Constellation** — full-screen view of a historical week's constellation with date, total places visited, total distance traveled.
6. **Year Overview (Premium / Upsell)** — overlay of all constellations from a year, showing growth or contraction of movement. Locked with "Unlock full history" paywall.
7. **Settings** — location permission status, data storage info ("all data stays on your device"), export data option, notification preferences (weekly "your constellation is complete" nudge), delete all data.
8. **Share Sheet / Export View** — styled export card: constellation centered on dark background, week label, subtle branding. Square crop for Instagram, wide crop for Twitter/Stories.

### Key UI Interactions

- Tap a star → slide-up detail sheet with place info
- Long-press a star → rename the location (e.g., "Work" instead of "475 Market St")
- Pinch to zoom the constellation canvas
- Swipe left/right in archive to navigate weeks
- "Share this week" → opens native share sheet with pre-rendered image
- Weekly push notification: "Your constellation for [week] is ready"

### Data Each Screen Needs

- **Home Screen:** current week's GPS clusters (lat/lng + visit count), place labels (resolved via reverse geocoding or user-set), constellation line path data, week start/end timestamps
- **Star Detail Sheet:** place name, address, visit count, first/last visit time, brightness rank vs. last week
- **Archive Grid:** list of past weeks — each needs a thumbnail-renderable snapshot: star positions, line paths, week label
- **Expanded Past Constellation:** full snapshot data + aggregate stats (unique places count, total km traveled)
- **Year Overview:** all 52 weekly snapshots merged into one coordinate space
- **Settings:** permission status from CoreLocation, local DB size, last sync timestamp
- **Share Card:** rendered bitmap of constellation (pre-generated on week close), week label, optional username watermark
