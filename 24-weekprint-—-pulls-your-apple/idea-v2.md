# Idea #24: WeekPrint (Improved)
## Version: v2
**Date:** 2026-03-28 14:56
**Status:** improved

## Original Idea
WeekPrint — pulls your Apple/Google Calendar and turns your week into a generative art poster you can save and share.

## What Changed and Why

- **Single visual style (geometric only), not multiple** — The critique correctly identified that "geometric grids AND watercolor" implies two separate rendering engines. Watercolor simulation is a full project on its own. v1 ships only the geometric renderer; watercolor becomes a locked premium style added post-launch once the core is solid.
- **Web app only (Google Calendar OAuth), drop Apple Calendar for v1** — Apple Calendar has no web OAuth. Trying to support Apple natively means iOS/macOS native apps, tripling the architecture scope. v1 targets web + Google Calendar exclusively. Apple Calendar users who sync to Google (most do) still work. True Apple-only users are a v2 problem.
- **Calendar names as categories, not NLP inference** — The critique nailed that event data is shallower than expected. Instead of fragile NLP on event titles, the app maps calendar names directly to colors (Work calendar = blue, Personal = green, etc.). Users assign colors in Settings; the first-run UX guides them through it in 30 seconds. This is dumb, reliable, and ships in days instead of weeks.
- **Physical print ordering (Printful/Gelato) as the monetization hook** — "Print your week" at $15/poster solves the no-monetization problem cleanly. It aligns with the poster format, it's a natural upsell on the share moment, and it doesn't gate the free shareable image. Freemium: free digital poster, paid physical print + premium art styles later.
- **Timebox the art quality iteration** — Set a hard 3-day limit on generative art polish before the first deploy. Ship with "good enough" geometry, then improve with user feedback. The goal is a functional render loop, not gallery-quality output at launch.

## Improved Description

WeekPrint is a web app that connects to your Google Calendar and generates a weekly geometric art poster from your events. Each calendar (Work, Personal, Family) maps to a color you choose. The art uses those colors and event durations to build a dense, structured grid — busier weeks produce tighter, more complex patterns; open weeks breathe.

Every Sunday, a new poster generates automatically. You can view it, zoom in, and download a high-res PNG to share or save as a wallpaper. The one paid action: order a physical print for $15 shipped via Printful. After a few months, you can order a "Year in Review" print — a grid of all 52 weeks.

There's no tracking, no journaling, no metrics dashboard. The art IS the insight. If your week looked chaotic, the poster looks chaotic. If you had a clear week, it shows. No annotation required.

## Why This Is Worth Building (Solo + AI)

A web app with Google Calendar OAuth + a Canvas/SVG geometric renderer is squarely in 2-week solo territory with AI coding tools — the OAuth flow, event data normalization, and geometric layout algorithm all have abundant training data for AI assistants to generate quickly. The monetization path (Printful API integration) is a well-documented webhook pattern that can be bolted on in a day. The product's value is in the visual output, and that visual quality can be iterated post-launch without breaking anything.

## Solo MVP Scope

- **What's in v1:**
  - Google Calendar OAuth connect
  - Calendar-name-to-color mapping (user assigns colors in Settings)
  - Geometric grid poster renderer (Canvas 2D or SVG, single style)
  - Current week poster auto-generated on login
  - Download as PNG (high-res)
  - Share image (pre-cropped for Instagram Stories and square)
  - Printful physical print order flow (pay per print, no subscription)
  - Archive of past weeks (thumbnails, click to view full poster)
  - Regenerate button on current week poster

- **What's out of v1:**
  - Apple Calendar / CalDAV integration
  - Watercolor or any non-geometric art style
  - iOS/Android native app
  - NLP event categorization
  - Year-in-review print (add after 4+ weeks of user data)
  - Subscription or premium tier (add post-validation)
  - Social features, community, comments
  - Notification/email for Sunday delivery (nice-to-have, add in week 3 if time allows)

- **Estimated build time with AI coding tools:** 2–3 weeks
  - Week 1: Google OAuth + event data pipeline + geometric renderer
  - Week 2: Archive, PNG export, share UX, color settings
  - Week 3 (buffer): Printful integration, polish, deploy

## Open Questions

- What geometric layout algorithm produces consistently beautiful output? Grid-based Mondrian-style? Treemap by duration? Need to prototype 2–3 options and pick the one that looks best before committing.
- How to handle weeks with almost no events (vacation, sick leave)? The poster could look embarrassingly empty — need a minimum density fill or a "sparse week" visual treatment.
- Printful vs. Gelato — which has better US shipping times and simpler API for poster prints?
- Should the poster be deterministic (same week always produces same poster) or seeded-random (same data, slightly different layout each regenerate)? Seeded-random with a "regenerate" button feels right — gives user agency without chaos.
- What's the minimum viable geometric style that looks intentional and beautiful, not like a placeholder? Need to validate this with 5 real people before launch.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding / Calendar Connect** — First-run screen for new users. Shows app name + one-line pitch ("Your week, as art"). Single CTA: "Connect Google Calendar." Explains in 2 bullets what data is read (calendar names, event times — no event titles read or stored). OAuth button triggers Google sign-in flow.

- **Screen 2: Color Setup (First-Run Only)** — Shown once after OAuth, before first poster generates. Lists the user's connected calendars by name. Each row has a color swatch (tappable to open color picker) and the calendar name. "Done" button at bottom generates the first poster. Skippable with default colors assigned automatically.

- **Screen 3: Home / This Week's Poster** — Full-bleed poster for the current week. Week label at top (e.g. "Mar 24–30, 2026"). Poster fills most of the screen. Action bar at bottom: Save (download PNG), Share (open share sheet), Print (open print order flow). Subtle "Regenerate" text button below the poster. Tap poster to enter fullscreen zoom view.

- **Screen 4: Poster Fullscreen / Zoom** — Fullscreen poster with pinch-to-zoom. Tap anywhere or swipe down to dismiss. Optional: long-press reveals legend overlay (which color = which calendar, hours per calendar).

- **Screen 5: Legend Overlay** — Slides up over the poster (modal bottom sheet). Shows color swatch + calendar name + total hours for the week per calendar. Brief stats: "X hours of meetings, Y free hours." Dismiss by tapping outside or swiping down.

- **Screen 6: Archive / History** — Grid of past weekly posters (thumbnail cards, 2-column grid). Each card shows the week label and the poster thumbnail. Tap any card to open that week's poster in fullscreen + legend. "This Week" is pinned at top or highlighted.

- **Screen 7: Settings** — Manage connected calendars (list with color swatches, editable). Color picker per calendar. "Disconnect Google Calendar" option. Future: notification preferences. Clean, minimal — not a settings dump.

- **Screen 8: Share Sheet / Export** — Triggered from the Share action on the Home screen. Shows format options: Instagram Stories (9:16 vertical), Square (1:1), Wallpaper (device resolution). Preview thumbnail updates to selected format. "Download" and "Copy to clipboard" buttons. Suggested caption auto-filled (e.g. "Week of Mar 24 — #weekprint").

- **Screen 9: Print Order Flow** — Triggered from "Print" CTA. Shows print preview (poster image on mock-up of physical print). Size options (e.g. 8x10, 11x14). Price displayed ($15–$25). Shipping address form. Payment (Stripe). Order confirmation screen with estimated delivery. Simple checkout, no account required beyond the calendar OAuth.

**Key interactions:**
- New user: Onboarding → Color Setup → Home (poster auto-generates, spinner while rendering)
- Returning user: Opens app → lands on Home with current week's poster already rendered
- Sharing flow: Home → tap Share → Share Sheet → pick format → download/copy
- Print flow: Home → tap Print → Print Order → address + payment → confirmation
- History browsing: tap Archive tab → grid → tap thumbnail → fullscreen poster → legend overlay
- Color change: Settings → tap calendar row → color picker → save → poster regenerates in background

## Version History
- v1: Original idea
- v1.1: Critique — art quality rabbit hole risk, no Apple Calendar web OAuth, no monetization path, multi-style scope creep
- v2: Improved — geometric-only style, web+Google OAuth only, calendar-name categories, Printful print orders as monetization
