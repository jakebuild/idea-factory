Verdict: WORTH BUILDING
Score: 8/10

What's Actually Good:
- The v2 pivot is exactly right — dropping community voting and AI recommendations removes the two biggest scope traps from v1. This is disciplined product thinking.
- Pre-seeding 200 ideas solves cold start without engineering. It's a content problem solved with manual effort, not code. Smart.
- Web-first keyboard UX is a genuine insight. Developers ARE on desktop. Arrow keys feel native; mobile swipe on a laptop is annoying friction.
- MAYBE pile + undo is thoughtful UX. Permanently discarding ideas with no recourse was a real usability flaw in the original concept.
- The "personal BUILD queue, not social platform" reframe is clean. One job. One user. No network effects needed to deliver value on day 1.
- Local-storage-only question in Open Questions shows the builder is actually thinking about cutting auth complexity — that's the right instinct.
- Scope is genuinely tight: card render + keyboard handler + a list view. This is a weekend project dressed up as a 2-week project.

Brutal Feedback:
- The core mechanic is a glorified bookmarking app. "Swipe to add to a list" is Pocket with a keyboard shortcut. The swipe gamification is the differentiator — but on desktop, without the physical flick of a thumb, it's just pressing an arrow key to save a link. The dopamine hit is weaker than it sounds.
- 200 ideas sounds like a lot until you realize power users will exhaust the feed in 2–3 sessions. What keeps them coming back on day 4? "Daily refresh" is mentioned but the content strategy is completely unresolved. This is the #1 retention killer and it's hand-waved away in Open Questions.
- The BUILD queue is solving a problem most developers already have a solution for — Notion, Obsidian, a sticky note, a GitHub repo of ideas. What makes Idea Swipe's queue better than the thing they already use? The answer "it's integrated with the swipe feed" is fine but thin.
- Tag filtering is listed as a feature but it's only useful if the 200 seeded ideas are well-tagged and balanced across categories. If 150 of them are SaaS tools, the filter is useless. Content quality and distribution are a product problem masquerading as an engineering problem.
- "Estimated build time with AI coding tools: 10–14 days" — the app logic is maybe 3 days. The actual time sink is curating 200 genuinely good ideas. That's research, writing, categorization, and quality control. Plan for that to take as long as the code.
- No clear answer on whether auth is needed for v1. This is the most important architectural decision and it's left open. Local storage is obviously the right call for v1 — decide it, don't defer it.
- The monetization question is completely unresolved. Freemium with BUILD queue size limits punishes the exact power users you want most. One-time purchase works only if you can drive traffic. Without a monetization model, this is a fun project, not a product.
- "Daily new ideas counter" assumes a content pipeline that doesn't exist yet. Don't ship a feature that requires ongoing manual labor unless you've committed to doing that labor indefinitely.

Key Questions:
- Who is the actual repeat user? A developer who opens this daily needs a reason to return. What is it, concretely, after the first week?
- What is the content pipeline after the initial 200? Manual curation at what cadence? Who does it? You?
- Why wouldn't a developer just use a Notion database with a "swipe" toggle column? The honest answer to this question is the product's entire value proposition.
- Is the real product the feed, or the BUILD queue? Because a great BUILD queue manager with curated content as the input is a different product than a curated feed with a queue as the output.
- Local storage vs. auth: what's the decision? This should be resolved before writing a single line of code.

Suggestions:
- Cut auth entirely from v1. Local storage. Ship faster, validate retention without backend complexity. Add auth in v2 when you know people want to sync across devices.
- Solve the content pipeline problem before shipping, not after. Either commit to manual curation (5 ideas/week, calendar block, non-negotiable) or don't promise daily refreshes.
- Add a "Submit your own idea" escape hatch from day 1 — not a full submissions feature, just a simple form that emails you. Lets you harvest user-generated ideas for free curation.
- The monetization path that makes sense: free forever with unlimited swipe/queue, charge for a "Pro" export feature (export BUILD queue to Notion, GitHub Issues, or markdown). Developers will pay for workflow integration. They won't pay for a bigger list.
- Consider making the initial 200 ideas public and open source (a GitHub repo). This builds credibility, drives SEO, and creates a contribution pathway without building submission infrastructure.
- The "who would build this" label on idea cards (indie dev / startup / hobbyist) is underrated — lean into it. Filtering by "who should build this" is more useful than category filtering for your audience.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? YES — the UI is tight and well-scoped. Card component, keyboard handler, two list views, one detail view, local storage persistence. AI tools handle this in a weekend. The actual risk is content, not code.
- Biggest solo complexity traps:
  - Content creation: 200 well-written, well-categorized, genuinely useful ideas is 20–40 hours of non-coding work. Budget for it or the product ships empty.
  - Feature creep from the deferred list: community voting, submissions, and AI recs are all "v2" — but they'll feel urgent once the feed feels thin. Stay disciplined.
  - Auth temptation: the moment you add auth you add sessions, email flows, forgot-password, and 3x the backend surface. Local storage first.
  - Daily refresh mechanics: "X new ideas today" counter implies a backend job adding ideas on a schedule. Either pre-load all ideas and mark them with a "release date" field, or skip the counter entirely. Don't build a cron job for v1.
  - Retention without re-engagement: no notifications, no email digest (explicitly out of scope) means users who stop coming back are gone. This is fine for v1 but means you need organic traffic to sustain growth.

Design Handoff:
- Screens needed:
  1. Swipe Feed — main screen, single idea card centered, keyboard shortcut bar at bottom (← Drop | → Keep | ↑ Maybe | Z Undo), category filter chips at top, ideas-remaining count, daily new badge
  2. Idea Card Component — title (large, bold), tagline (medium, italic), 2–3 sentence description, category pill, complexity badge (Weekend / 2 Weeks / Month), audience label (Indie Dev / Startup / Hobbyist)
  3. Idea Detail / Expanded View — full idea content, personal notes textarea (auto-save on blur), status selector (Saved / Actively Building / Archived), action buttons (Move to Maybe / Move to Build / Drop), back to feed navigation
  4. BUILD Queue — card grid or list, sortable by date saved / category / complexity, each item shows title + category + date + status indicator, click to open detail, bulk archive, empty state
  5. MAYBE Pile — same layout as BUILD Queue, "Put back in feed" action per item, empty state
  6. Category Filter Panel — slide-in or dropdown, checkbox list of categories, "All" toggle, saved to local storage as preference
  7. Onboarding Flow (3 steps) — Step 1: pick builder type (indie / startup / hobbyist / student); Step 2: select categories you build in; Step 3: interactive keyboard shortcut demo with a live practice card
  8. Empty States — (a) Feed exhausted: "You've seen everything. X new ideas drop tomorrow." + BUILD queue CTA; (b) Empty BUILD queue: "Nothing saved yet." + feed CTA; (c) Empty MAYBE pile: "No maybes." + feed CTA

- Key UI interactions:
  - Keyboard primary: → keep, ← drop, ↑ maybe, Z undo — must feel instant with no lag
  - Card swipe animation: card slides and fades in the direction of the action (not just disappears)
  - Click fallback: three buttons below the card for non-keyboard users
  - Filter chips update feed instantly without page reload
  - Notes field in detail view auto-saves on blur, shows "Saved" micro-feedback
  - "Put back in feed" in MAYBE pile inserts card as next card in queue (not randomly)
  - Undo reverses last action and re-shows the card with a brief animation
  - Onboarding step 3 requires at least one practice swipe before "Start" button activates

- Data each screen needs:
  - Swipe Feed: idea array filtered by selected categories + not-yet-seen IDs (stored in local storage), current index, daily-new count
  - Idea Card: id, title, tagline, description, category, complexity, audience, date_added
  - Idea Detail: full idea object + user notes (local storage, keyed by idea id) + user status for this idea
  - BUILD Queue: all ideas where user status = "saved" or "building", sorted by user preference, timestamps
  - MAYBE Pile: all ideas where user status = "maybe", timestamps
  - Category Filter: list of all unique categories from idea dataset, user's saved filter preference (local storage)
  - Onboarding: user builder type selection, selected categories — written to local storage as user profile on completion
