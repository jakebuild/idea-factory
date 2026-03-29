# Critique v1 — Idea #28: Idea Swipe (Tinder for Ideas)

**Verdict:** NEEDS MAJOR WORK
**Score:** 5/10

---

## What's Actually Good

- The swipe mechanic is genuinely fun and lowers friction for engaging with ideas — it's a proven UX pattern that works
- "Tinder for ideas" is immediately legible as a concept; you don't need to explain it twice
- The saved queue (BUILD pile) is genuinely useful — a personal backlog of ideas you actually like is a real problem
- Community surfacing has real signal value: crowdsourced filtering of ideas is better than one person's curation
- Daily feed refresh is a smart retention hook — gives users a reason to come back

---

## Brutal Feedback

- **The cold start problem is catastrophic.** A swipe feed with no ideas is worthless. You need hundreds of quality ideas seeded before launch or users open it, see 5 cards, run out, and never return. Where do those ideas come from on day 1? From you, manually? That's a content grind, not a product.
- **"AI learns what you like" is vaporware at this stage.** What does this actually mean? A recommendation model trained on swipe data? You need thousands of swipes per user before any ML signal emerges. For a solo dev at MVP stage this is pure scope creep — don't build it, promise it, or even hint at it until you have 10k+ active users generating real swipe data.
- **Community voting ≠ quality ideas.** Reddit, Product Hunt, and every upvote platform have proven that voting surfaces popular, not good. You'll end up with "Uber for dogs" type ideas dominating the feed. The feed quality problem is existential.
- **Your target user is unclear.** Solo devs who want build ideas? Startup founders? Hobbyists? The "submit for others to swipe on" mechanic only works if there's a community. Without that, it's a solo bookmarking app dressed up as a social platform.
- **You're competing with Product Hunt, Indie Hackers, and every "ideas" newsletter.** None of them have solved the signal-to-noise problem. You're adding a swipe mechanic on top of the same unsolved problem.
- **The swipe mechanic is mobile-native. Your users are likely on desktop.** Developers, your core audience, work on desktop. Swipe UX on desktop is awkward — you'll need keyboard shortcuts or click-drag, which immediately degrades the "Tinder feel" that makes the concept click.
- **Drop = LEFT means those ideas just disappear.** No reconsideration, no second chances, no "I was wrong" — this is fine for dating, brutal for ideas. People grow and their taste changes. Tinder for ideas needs an undo/revisit mechanic or you're permanently discarding potentially good ideas.
- **Submitting ideas for others opens a spam/quality moderation problem immediately.** Without curation, the feed fills with garbage. With curation, you're a full-time content moderator. Pick your poison.

---

## Key Questions

- What's in the feed on day 1 before users submit anything?
- How do you prevent low-quality idea spam?
- Is the primary use case personal (bookmarking tool) or social (community platform)? Because they require completely different architectures and go-to-market strategies.
- What does "AI learns what you like" actually ship as in v1 — a tag filter? A content-based recommendation? Be honest.
- Who submits ideas and why? What's their incentive?
- Mobile-first or web-first? This changes the entire UX approach.

---

## Suggestions

- **Strip it to the personal tool first.** Launch as a curated idea bookmarker — pre-seed 200 high-quality ideas, let users swipe, and save their BUILD pile. No community, no AI, no submissions. Validate that people actually use the saved queue.
- **Seed the feed yourself** with curated ideas from Indie Hackers, Product Hunt, Reddit r/SideProject. You control quality at the start.
- **Drop "AI learns what you like" entirely from v1.** Replace it with simple tag filtering (mobile app, SaaS, tools, games, etc.) — that's honest and actually useful.
- **Add an undo/revisit mechanic** — users will swipe left on things they regret.
- **Web-first with keyboard shortcuts** (→ keep, ← drop) to serve the developer audience properly.
- **Community features are v2.** Get 100 people using the personal tool first, then unlock submissions and voting.

---

## Solo Dev Reality Check

- **Can one person ship this in 2–4 weeks with AI coding tools?** MAYBE — the core swipe + save mechanic is shippable in 1–2 weeks. The community + AI learning layer is a separate 2–3 month project. Scope ruthlessly.
- **Biggest solo complexity traps:**
  - Content cold start: you'll spend more time curating ideas than building features
  - ML recommendation: don't touch it in v1, it's a rabbit hole
  - Moderation system: user-submitted content + community voting requires spam/abuse handling
  - Feed algorithm: community voting + recency + personalization is 3 separate ranking signals to balance
  - Mobile vs desktop UX split: swipe mechanic needs separate implementation for touch vs mouse/keyboard

---

## Design Handoff

### Screens needed

1. **Swipe Feed** — main screen, one idea card at a time, swipe left/right or keyboard arrows, progress indicator, daily count
2. **Idea Card** — concept title, tagline, 2–3 sentence description, tags/category, "who would build this" label
3. **BUILD Queue / Saved Ideas** — grid or list of kept ideas, sortable, with ability to remove or archive
4. **Idea Detail** — full expanded view of a single idea (from the queue or feed), with notes field for user to add context
5. **Submit Idea** — form to submit your own idea: title, tagline, description, tags
6. **Onboarding / Category Picker** — first-run screen to pick interests/tags to seed the initial feed
7. **Empty States** — feed exhausted (come back tomorrow), empty BUILD queue (start swiping)

### Key UI interactions

- Swipe left (drop) / swipe right (keep) — with visual feedback (red X / green checkmark overlay)
- Keyboard shortcut support: ← drop, → keep, ↑ maybe/save for later
- Undo last swipe button
- Tap/click idea card to expand full detail without swiping
- Pull-to-refresh (mobile) or manual "load more" (web) for feed

### Data each screen needs

- **Swipe Feed:** ordered queue of unseen idea cards for this user; swipe history to exclude seen cards; daily card limit counter
- **Idea Card:** id, title, tagline, description, tags, submitter (optional), community vote count
- **BUILD Queue:** list of idea IDs user swiped right on, with timestamps; full idea data for each
- **Idea Detail:** full idea object + user's personal notes on that idea
- **Submit Idea:** form fields → idea object; user auth context
- **Onboarding:** tag/category list; user preference storage
