# Critique v1 — WeekPrint

**Verdict:** WORTH BUILDING
**Score:** 7/10

## What's Actually Good

- The emotional hook is real and differentiated. "Your week as art" is a genuinely novel angle on the tired "productivity tracker" genre — it's introspective without being preachy
- Generative art per week = built-in virality loop. People will share these on Instagram/Twitter because they look cool, not because they're compelled to grind metrics
- The event → visual mapping (meetings = dense grids, free time = watercolor washes) is concrete and communicable — a designer can run with this immediately
- Calendar integration is a solved problem. Apple EventKit and Google Calendar API are mature, well-documented, and OAuth flows are standardized
- The scope is genuinely solo-shippable. This is a read-only data consumer + visual renderer, not a two-sided marketplace or real-time collaboration tool
- The "one tap save/share" use case is razor-focused — no bloat temptation

## Brutal Feedback

- **The generative art is the entire product and it's the hardest part.** "Dense geometric grids" and "open watercolor washes" sound poetic in a pitch but translating that into actual beautiful output is a design and algorithmic rabbit hole. Most solo devs underestimate how much iteration it takes to make generative art look *good* rather than just technically correct. You'll ship ugly art first. You'll ship it 20 more times before it looks Instagram-worthy.
- **Calendar data is shallower than you think.** Events have a title, duration, maybe a color. There are no "categories" unless the user manually labels them or you're inferring from event titles (fragile NLP) or calendar names. The richness of the art depends on data richness that may not be there.
- **The "texture of how you spent your time" insight is weak at MVP.** A grid of colored blocks is not a revelation — it's just a calendar with different aesthetics. The emotional payoff requires the visual output to be *actually surprising or beautiful*, and that's not guaranteed.
- **Apple Calendar OAuth on iOS vs macOS vs web is three different integration paths.** EventKit works on native iOS/macOS. Web apps need Google Calendar API + Apple Calendar (which has no web OAuth — users would need to export/sync). Pick a lane early or you'll build three half-done integrations.
- **Retention is a question mark.** You get one poster per week. That's 52 interactions per year max. What keeps users coming back after the novelty wears off? There's no feedback loop, no progression, no community — just a pretty image you'll eventually stop saving.
- **The art style diversity (geometric grids AND watercolor) implies multiple rendering engines.** That's not trivial — watercolor simulation vs. crisp geometric tiling are completely different visual systems. Trying to do both at launch is scope creep in disguise.
- **No obvious monetization path.** Free app with no hook = charity. Paywall behind the weekly poster = friction on the one thing that drives shares. Premium art styles? Maybe, but thin.

## Key Questions

- How do you handle calendars with zero categorization? Most users have a chaotic mix of personal, work, and shared calendars with inconsistent naming. What does the art look like for that?
- iOS native app, Android, or web? This determines the entire architecture and which calendar APIs you can access.
- How are event types/categories inferred? Manual tagging by user, calendar color, event title NLP, calendar name? Each has tradeoffs.
- What's the render engine? Canvas 2D, SVG, WebGL, p5.js, custom shader? This is a huge technical choice with UX implications.
- Is the poster deterministic (same week = same poster) or randomized? Users will expect consistency but randomness could drive re-generation engagement.
- What's the monetization model? Free/paid/freemium?

## Suggestions

- **Start with a single visual style, not multiple.** Pick geometric — it's easier to implement cleanly and looks more intentional. Add watercolor as a premium style later.
- **Build a web app first.** Google Calendar OAuth on web is the path of least resistance. Skip Apple Calendar for v1 — add it later via CalDAV or iOS native.
- **Use calendar *names* as categories, not event titles.** "Work", "Personal", "Family" calendars map cleanly to colors without NLP. It's dumb but it works.
- **Add a "regenerate" button** — it costs nothing and makes users feel like they have agency, which drives engagement.
- **The save/share UX is the product demo.** Make the poster look stunning before you worry about anything else. One beautiful screenshot will do more marketing than any feature.
- **Offer physical print ordering (Printful/Gelato API) as a paid tier.** This is the cleanest monetization hook — "print your year" at $15/poster. Aligns perfectly with the poster format.

## Solo Dev Reality Check

- **Can one person ship this in 2–4 weeks with AI coding tools?** MAYBE — The calendar integration + basic geometric rendering is genuinely 2-week territory. The risk is aesthetic quality: getting the art to look *actually good* could blow the timeline. Timebox the art iteration ruthlessly.
- **Biggest solo complexity traps:**
  - Art quality rabbit hole — you will keep tweaking the visual output indefinitely because it never looks quite right
  - Multi-platform calendar auth — Apple Calendar on web doesn't exist natively; don't let scope expand to iOS native in week one
  - Category inference — if you go beyond calendar names into NLP or AI categorization, complexity explodes
  - Watercolor simulation — skip it entirely for v1, it's a full project on its own
  - State management for "which week am I viewing" + historical archive could sneak up in complexity

## Design Handoff

### Screens Needed
1. **Onboarding / Calendar Connect** — connect Google Calendar (and optionally Apple via CalDAV); permission request + brief explanation of what data is read
2. **Home / This Week's Poster** — full-screen poster for the current week; tap to expand/zoom; action bar with Save and Share buttons
3. **Poster Detail / Legend** — expanded view with a legend showing which calendar/category maps to which color/texture; optional "about this week" stats (X hours of meetings, Y free hours)
4. **Settings** — manage connected calendars; customize color palette per calendar; toggle art style (if multiple); notification preference for Sunday delivery
5. **Archive / History** — grid of past weekly posters; tap to view any past week's poster + detail
6. **Share Sheet / Export** — high-res export options; pre-composed share image for Instagram Stories (vertical), Twitter/X (square), and standard wallpaper size

### Key UI Interactions
- Calendar OAuth connect flow (modal or dedicated screen)
- Full-screen poster pinch-to-zoom
- Long-press or swipe on poster to reveal the legend overlay
- Tap any archive thumbnail to open that week's poster detail
- "Regenerate" button (subtle, not prominent) on poster detail
- Color picker per calendar in Settings
- Share action sheet with format options

### Data Each Screen Needs
- **Home:** current week's calendar events (title, start/end, calendar name/color), rendered poster image (cached), week label (e.g. "Mar 24–30, 2026")
- **Poster Detail/Legend:** same event data + aggregated stats (total hours per calendar), poster image
- **Settings:** list of connected calendars with current color assignments, notification toggle state
- **Archive:** list of past week records (week label, thumbnail image URL or cached render)
- **Share:** high-res poster render, week label string for caption suggestion
