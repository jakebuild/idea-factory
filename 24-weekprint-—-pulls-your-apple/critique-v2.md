# Critique v2 — WeekPrint

**Verdict:** WORTH BUILDING
**Score:** 8/10

## What's Actually Good

- **Scope discipline is real this time.** v2 actually listened. Dropping Apple Calendar, watercolor styles, NLP categorization, and native apps was the right call. The scope is now genuinely 2-week solo territory.
- **Calendar-name-to-color is the right call.** Dumb and reliable beats clever and fragile. Users understand "Work = blue" immediately. No ML, no parsing errors, no edge cases to debug at 2am.
- **Printful monetization fits the product.** "Print your week" is a natural action at the share moment. $15/poster is impulse-buy territory. No subscription friction, no paywall on the core value. This is how physical print SaaS is supposed to work.
- **The deterministic-vs-seeded-random question was correctly identified.** Seeded-random with regenerate is the right UX — gives agency, keeps it personal, doesn't feel like a slot machine.
- **The Design Handoff is thorough and actionable.** 9 screens, clear data requirements, interaction flows mapped. A designer could start immediately. This is unusually well-scoped for an MVP doc.

## Brutal Feedback

- **"Generative art" is still the core risk and it's still underspecified.** The idea says "geometric grid poster" and mentions Mondrian-style vs. treemap-by-duration as open questions. These are not equivalent outputs. One looks like art. One looks like a calendar heatmap. You have not validated that *any* geometric algorithm produces output people actually want to hang on their wall. "Need to prototype 2–3 options" is optimistic — this is where the time sink lives. Budget 5 days minimum, not 3.
- **The value prop has a silent assumption: people care what their week looked like.** Most people have no desire to visualize their calendar. The target user is a small intersection of: (a) uses Google Calendar religiously, (b) has aesthetic taste, (c) would pay to print a visual of their schedule. This is a niche of a niche. The product doesn't fail because it's bad — it fails because 95% of people shrug.
- **Sparse week problem is still unsolved.** The idea flags it but doesn't answer it. "Minimum density fill" is handwaved. A vacation week or a sick week produces a white poster. The user's first print order after a light week will look like a mistake, not art. This is a real UX failure mode that needs a real design decision before launch, not a buffer-week afterthought.
- **Printful integration is not "a day."** First-time Printful API integration involves: webhook setup, product variant IDs, print-ready file spec (bleeds, DPI, color profile), order status callbacks, address validation, and Stripe. Budget 3–4 days realistic, not one.
- **Archive of past weeks is v1 scope creep.** You listed it as v1. If users connect and it's week 1, the archive is empty. It provides zero value until week 3+. Build it in week 3 if you have time — it is not core to the poster-generation loop.
- **"Every Sunday, a new poster generates automatically" requires a background job.** A cron that runs OAuth-authenticated Google Calendar reads for every user, generates a poster, and stores it — this is real backend infrastructure. At scale-zero it's fine, but it's not zero-complexity. If your backend is serverless/edge, scheduled jobs need deliberate handling.
- **No viral loop.** The share UX is there, but Instagram Stories with "Week of Mar 24 — #weekprint" is not a growth engine. The poster needs to be beautiful enough that people share it unprompted. If the geometric output looks generic, no one shares it. The entire growth model depends on art quality, which is still unvalidated.

## Key Questions

- Have you prototyped any geometric layout algorithm and shown it to 5 people? This is the single most important pre-build validation and the idea doesn't mention doing it.
- What's the retention mechanism after week 1? User connects, gets their first poster, downloads it. Why do they come back week 2? Email/push notifications are listed as out-of-scope. Without any re-engagement, weekly retention is passive at best.
- What does an "empty" or "sparse" week look like? Have you designed this state?
- Is $15 the right price point? Artifact.co and Chatbooks charge $20-40 for photo prints. Poster-format art prints go higher. You may be underpricing.
- Do you need to store poster images server-side, or regenerate on demand? Storing high-res PNGs for every user every week is non-trivial storage. On-demand regeneration requires the user's calendar OAuth token to be stored securely.

## Suggestions

- **Validate the art before writing a line of app code.** Build a standalone Canvas renderer, feed it fake event data, generate 20 posters, put them in a group chat. If 3+ people say "that's actually cool," proceed. If they say "neat concept," kill it.
- **Cut the archive from v1.** Ship with: connect → color setup → current week poster → download/share → print order. That's the full loop. Archive is week 3 or v2.
- **Add a weekly email as the re-engagement hook.** "Your Week 14 poster is ready" with a thumbnail. One email per week per user. This is simpler than push notifications and survives app abandonment. Build this in week 2, not week 3.
- **Solve the sparse week problem explicitly before launch.** Options: (1) minimum N geometric elements regardless of event count — fill with neutral/grey shapes, (2) "rest week" visual treatment with a distinct sparse aesthetic, (3) "your lightest week this month" framing in the UI. Pick one.
- **Consider Gelato over Printful for EU users.** If you expect any European audience, Gelato's EU fulfillment is meaningfully faster. Both have similar API complexity.
- **Seeded-random should use the ISO week number as the seed component**, not just event data. This makes the same week always reproducible but still unique to that user's calendar. Prevents "I regenerated and lost the one I liked."

## Solo Dev Reality Check

- **Can one person ship this in 2-4 weeks with AI coding tools?** YES — but only if the art quality question is answered in week 1. The OAuth flow, event normalization, and Printful integration are all well-trodden paths with good AI training data. The geometric renderer is the unknown variable. If prototyping takes 5+ days, the timeline slips to 3-4 weeks.
- **Biggest solo complexity traps:**
  - Generative art rabbit hole — "just one more tweak to the layout algorithm" can eat a week
  - OAuth token storage and refresh — Google access tokens expire; silent refresh for background poster generation is fiddly
  - Printful print-ready file spec — your PNG export needs specific DPI, bleed margins, and color profile or the print looks bad; first-time Printful setup takes longer than expected
  - Background job for Sunday auto-generation — needs a cron or scheduled function with per-user auth context
  - Sparse/empty week edge cases — visually untested states that will surface immediately with real users

---

## Design Handoff

### Screens Needed

1. **Onboarding / Calendar Connect** — App name, one-line pitch, "Connect Google Calendar" CTA, 2-bullet data transparency note, OAuth trigger
2. **Color Setup (First-Run)** — List of user's calendars, color swatch per row (tappable → color picker), "Generate My Poster" CTA, skip option with auto-assigned defaults
3. **Home / This Week's Poster** — Full-bleed poster view, week label header, bottom action bar (Save / Share / Print), "Regenerate" text button, loading/spinner state while rendering
4. **Poster Fullscreen / Zoom** — Fullscreen pinch-to-zoom, tap/swipe-down to dismiss, long-press reveals legend overlay
5. **Legend Overlay (Bottom Sheet)** — Color swatch + calendar name + hours per calendar, total meeting hours vs. free hours, dismiss by tap-outside or swipe
6. **Archive / History** — 2-column thumbnail grid of past weeks, week label on each card, "This Week" pinned at top, tap to open fullscreen
7. **Settings** — Calendar list with color swatches (editable), disconnect Google Calendar option, future: notification prefs
8. **Share Sheet / Export** — Format selector (Stories 9:16 / Square 1:1 / Wallpaper), preview thumbnail, Download + Copy to Clipboard, auto-suggested caption
9. **Print Order Flow** — Print preview mockup, size options + pricing, shipping address form, Stripe payment, order confirmation with delivery estimate

### Key UI Interactions

- New user flow: Onboarding → Color Setup → Home (poster generates with spinner) → action bar visible once rendered
- Returning user: Opens to Home with current week poster already rendered (cached)
- Sparse week state: Home poster still fills screen — needs explicit visual design for low-event weeks
- Regenerate: taps button → spinner overlay on poster → new layout renders in place, no page reload
- Share: Home → Share button → Share Sheet → format select → download or clipboard copy
- Print: Home → Print button → Print Order (multi-step: preview → size → address → payment → confirmation)
- History: Archive tab → grid → tap card → fullscreen → long-press → legend overlay
- Color edit: Settings → tap calendar row → color picker modal → save → poster regenerates in background with loading indicator

### Data Each Screen Needs

- **Onboarding:** no data — static screen
- **Color Setup:** list of user's Google Calendar objects (id, name, color default); user's color assignment map (saved to DB on "Done")
- **Home:** current ISO week date range; generated poster image (PNG or SVG); calendar-to-color map; loading/error state
- **Fullscreen:** same poster image; calendar-to-color map for legend
- **Legend Overlay:** per-calendar: name, color, total event hours this week; derived total: meeting hours, free hours
- **Archive:** list of past poster records (week label, thumbnail URL, ISO week); current week highlighted
- **Settings:** user's connected Google Calendar list with assigned colors; user account info (email)
- **Share Sheet:** poster image in selected aspect ratio; week label string for caption
- **Print Order:** poster image (print-ready high-DPI version); size/price options from Printful product catalog; user shipping address; Stripe payment intent
