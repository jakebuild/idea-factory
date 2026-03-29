# Idea #29: Design Swipe — Tinder for App Designs (Improved)
## Version: v2
**Date:** 2026-03-29 11:54
**Status:** improved

## Original Idea
Design Swipe Tinder for app designs - swipe to BUILD or DROP. Designers submit their app mockups/screenshots. Developers swipe through designs: RIGHT = I'd build this, LEFT = Drop. See the actual design, not just text. Designers get paid when someone commits to build. Developers get designs they can actually build. It's a marketplace for pre-designed app concepts.

## What Changed and Why
- **Kill the marketplace for v1, ship swipe + BUILD queue as a free tool first** — The critique nailed it: payments + designer payouts + licensing = 3 months minimum for a solo dev. Instead, v1 is a free personal curation tool for vibe coders. Prove the swipe mechanic works, build an audience, then bolt on monetization. This eliminates the chicken-and-egg cold start problem and the entire legal/financial complexity layer.
- **Seed the feed with the existing 822 screenshots — be honest they're inspiration, not specs** — The critique called out the massive gap between "inspiration screenshots" and "buildable specs." v1 leans into this honestly: it's a swipe-to-save inspiration tool powered by the existing database. Add a "Request Full Stitch Project" CTA on each card to gauge demand for the paid tier — this double-functions as the demand signal for the marketplace later.
- **Target specifically: solo devs and vibe coders, not "developers" broadly** — The critique flagged the audience being too broad. Narrowing to solo devs / indie hackers / vibe coders changes the whole tone and feature priority. These users want fast inspiration + save-to-queue, not enterprise licensing workflows. The "built with this design" showcase becomes social proof within this exact community.
- **Make export concrete: one-click "Open in Stitch"** — The critique destroyed the hand-waved "export it and start building" claim. v1 fixes this by partnering (or just deep-linking) with Stitch: every design card that has a Stitch project ID gets a real "Open in Stitch" button. This makes the export story concrete and differentiated from Dribbble/Behance immediately.
- **Add lightweight curation gate upfront** — No open uploads in v1. All content comes from the curated 822-screenshot database plus a small invite-only designer beta. This eliminates content moderation complexity and quality noise while keeping the feed high-signal.

## Improved Description
Design Swipe is a swipe-to-save inspiration tool for solo devs and vibe coders. You open the app, swipe through beautifully designed app concepts (full-screen cards showing real mockups), swipe RIGHT to add to your BUILD queue, LEFT to pass. Your BUILD queue becomes your personal design library — a curated collection of app ideas you actually want to build.

The feed is seeded with 133 app concepts drawn from the existing 822-screenshot inspiration database, organized into full-screen swipeable cards. No signups for designers, no payments, no marketplace complexity — just the core loop.

What makes it different from Dribbble/Behance: every card is framed as a buildable app concept with category, platform, estimated screen count, and a key feature summary. When a design has a Stitch project behind it, there's a one-click "Open in Stitch" button that drops you directly into a working prototype. This is the moat — pure inspiration sites don't give you something you can actually build from.

Monetization comes in v2: unlock the full Stitch project export for $9. Designers can submit designs for a revenue share (70/30). But none of that is in v1. v1 just needs to prove people swipe and save.

## Why This Is Worth Building (Solo + AI)
The swipe UI + BUILD queue is a 1-week build with AI coding tools — it's a card stack, a gesture handler, and a local save list. The seed content (822 screenshots) already exists, so day-one content is solved. By stripping out payments and marketplace, the entire legal and financial complexity is deferred, leaving a shippable product that can validate the core mechanic and build an audience before any monetization risk.

## Solo MVP Scope
- **What's in v1:**
  - Swipe feed with full-screen design cards (swipe left/right, keyboard shortcuts)
  - Design detail view — tap to see all screens in the flow, category, platform, description
  - BUILD queue — saved designs, persistent per user (local or simple auth)
  - "Open in Stitch" button on cards that have a linked Stitch project
  - "Request Full Specs" button — emails/logs the request, counts demand signals
  - Seed content: 133 app concepts from the existing 822-screenshot database
  - Basic filter by category and platform (iOS/Android/Web)
  - "Built with this design" showcase — users can submit a link after building

- **What's out of v1:**
  - Designer uploads (invite-only, zero self-serve)
  - Payments and designer payouts (Stripe Connect entirely deferred)
  - Licensing terms and IP enforcement
  - Export to Figma, React code, or image packs (only Stitch deep-link)
  - Team / shared queues
  - Full text search
  - Mobile app (web-first, responsive)

- **Estimated build time with AI coding tools:** 1–2 weeks

## Open Questions
- Do the 822 screenshots map cleanly to distinct app concepts, or do they need manual grouping before they can be turned into swipeable cards?
- Is a Stitch deep-link (open project by ID) technically available, or does this require a formal partnership/API?
- What's the right signup gate — fully anonymous swipe with optional account to save the BUILD queue, or require signup upfront?
- What engagement signal triggers the v2 monetization build — number of BUILD queue saves, "Request Full Specs" clicks, or return visitor rate?

## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Swipe Feed (Home)** — Full-screen design card stack. Shows app name, category badge, platform tag (iOS/Android/Web), screen count, one-sentence description, thumbnail of the hero screen. Swipe right = BUILD, swipe left = DROP. Keyboard: → / ← arrows. Tap card to expand detail. Bottom nav: Feed / Queue / Profile.
- **Screen 2: Design Detail View** — Scrollable horizontal carousel of all mockup screens for this app concept. Above carousel: app name, category, platform, estimated screen count, short description. Below: "Add to BUILD Queue" button + "Open in Stitch" button (shown only if Stitch project exists) + "Request Full Specs" button. Back arrow to return to feed.
- **Screen 3: BUILD Queue** — List of all right-swiped designs. Card per item: thumbnail, app name, category, date saved, status badge (Saved / Opened in Stitch / Built). Tap to open Detail View. Mark as Built button — triggers a modal to submit a URL/screenshot of what they built.
- **Screen 4: "Built With This" Showcase** — Public wall of things that got built using designs from the feed. Grid of cards: screenshot of built app, link, design concept it was based on, builder name (optional). Social proof engine.
- **Screen 5: Filter / Browse** — Filter the feed by category (Social, Productivity, Finance, Health, etc.), platform (iOS/Android/Web), and sort by Most Swiped / Newest / Most Built. Used before entering the main swipe loop.
- **Screen 6: Onboarding (first launch)** — 3-slide intro: what the app does, how the swipe works, what the BUILD queue is for. Skip button prominent. No role selection in v1 (everyone is a builder).
- **Screen 7: Empty States** — BUILD queue empty (prompt to go swipe), no results for filter (suggest clearing filters), feed exhausted (you've seen everything — here's your BUILD queue).
- **Screen 8: Profile / Settings** — Basic: display name, BUILD queue count, built count. Settings: notification preferences, clear queue, sign out. Minimal — not a social profile in v1.

**Key interactions:**
- Swipe card right → card animates off right, green BUILD flash, next card slides in, item silently added to queue
- Swipe card left → card animates off left, red DROP flash, next card slides in
- Tap card → expands to Detail View with all screens scrollable horizontally
- "Open in Stitch" button → deep-links to Stitch project in new tab, logs the event
- "Request Full Specs" → button turns to "Requested ✓", logs demand signal to DB
- "Mark as Built" → modal with URL + optional screenshot upload, posts to Showcase wall
- Filter applied → feed resets to top of filtered subset with new card stack

## Version History
- v1: Original idea
- v1.1: Critique — marketplace payment trigger undefined, chicken-and-egg cold start, 822 screenshots aren't specs, export undefined, no moat vs ThemeForest
- v2: Improved — kill marketplace for v1, ship free swipe+queue tool seeded with existing database, make export concrete via Stitch deep-link, target solo/vibe devs specifically
