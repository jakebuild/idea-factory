# Idea #28: Idea Swipe (Improved)

## Version: v2
**Date:** 2026-03-29 10:38
**Status:** improved

## Original Idea
Tinder for ideas — swipe RIGHT to keep (build it later), swipe LEFT to drop. Browse app ideas one by one, save a personal BUILD queue, with community voting and AI learning what you like.

## What Changed and Why

- **Dropped community voting and AI learning from v1** — the critique nailed it: community voting surfaces popular not good, and ML recommendations need thousands of swipes before any signal emerges. Both are scope creep that bloat a 2-week build into a 3-month project. Replaced with simple tag filtering (SaaS, mobile, tools, games, etc.) — honest, useful, shippable day 1.
- **Solved the cold start problem upfront** — the critique called this "catastrophic." v2 ships with 200 pre-seeded, hand-curated ideas pulled from Indie Hackers, Product Hunt, and r/SideProject. No reliance on user submissions to fill the feed at launch. User submissions are deferred to v2.
- **Web-first with keyboard shortcuts, not mobile swipe** — developers (the core audience) are on desktop. The swipe mechanic on desktop is awkward. v2 uses keyboard arrow keys (→ keep, ← drop) as the primary interaction, with click-based fallback. This makes it feel native for the actual user, not a mobile port.
- **Added undo and a "Maybe" pile** — permanently discarding an idea with a left swipe is brutal. v2 adds a ← MAYBE state (ideas you're unsure about) and an undo button for the last swipe. Users' taste changes; the tool should reflect that.
- **Reframed as a personal productivity tool, not a social platform** — the critique exposed the identity crisis: personal bookmarker vs. community platform require entirely different architectures. v2 commits to being a personal BUILD queue manager. The social layer is explicitly v2.

## Improved Description

Idea Swipe is a curated, keyboard-driven web app for developers who collect too many "I should build this someday" ideas and never act on them.

You open it, and there's a card: one idea, a tagline, a 2-sentence description, and a category tag. Press → to add it to your BUILD queue. Press ← to drop it. Press ↑ to put it in your MAYBE pile. That's it.

The feed is pre-seeded with 200 high-quality, real-world buildable ideas across categories: SaaS tools, mobile apps, indie games, dev tools, consumer apps. Ideas are curated, not crowdsourced — quality is controlled at the source.

Your BUILD queue is your actual backlog. You can open any saved idea, add personal notes, mark it as "actively building," or archive it. It's not a wish list — it's a prioritized to-do list for your side projects.

Tag filtering lets you tell the app what you actually build (web, mobile, AI, games) and the feed surfaces relevant ideas first. No ML, no black box — just a filter you control.

Daily refreshes add new curated ideas to the feed. You come back because there's always something new, and because your BUILD queue is the thing keeping your side project pipeline organized.

The undo button and MAYBE pile exist because ideas you dismiss today might be the right fit in 3 months when your skills or interests shift.

## Why This Is Worth Building (Solo + AI)

The core mechanic — card rendering, swipe/keyboard interaction, and a saved queue — is a tight, well-scoped UI problem that AI coding tools handle exceptionally well. The content problem (seeding 200 ideas) is a one-time manual effort, not a recurring engineering challenge. This is a tool that solves a real problem the builder personally has — that's the best validation signal for a solo project.

## Solo MVP Scope

- **What's in v1:**
  - Pre-seeded feed of 200 curated ideas (manually gathered, stored as JSON/markdown)
  - Keyboard-driven swipe interface (→ keep, ← drop, ↑ maybe, Z undo)
  - BUILD queue — list of saved ideas with timestamps
  - MAYBE pile — separate list for undecided ideas
  - Idea detail view with personal notes field
  - Tag filter to narrow feed by category
  - Daily new ideas counter (show "X new today")
  - Simple auth (email magic link or GitHub OAuth)
  - Empty states for exhausted feed and empty queue

- **What's out of v1:**
  - Community voting (v2)
  - User idea submissions (v2)
  - AI/ML recommendations (v3 or never)
  - Social sharing or public profiles
  - Mobile native app
  - Feed algorithm / personalization
  - Notifications / email digest

- **Estimated build time with AI coding tools:** 10–14 days

## Open Questions

- Where does the ongoing idea content come from after the initial 200? Need a sustainable curation strategy (manual vs. scraping vs. eventually user submissions with moderation).
- What's the right daily refresh cadence — 5 new ideas/day? 10? Too few feels stale, too many overwhelms.
- Does the app need auth at all for v1, or can it be local-storage-only until you validate retention? (Local storage removes the auth complexity entirely.)
- What's the monetization path? Freemium with a limit on BUILD queue size? One-time purchase? This informs whether to build auth at all.

## Design Handoff

List every screen/view the app needs:

- **Screen 1: Swipe Feed** — main screen, one idea card centered on screen, keyboard shortcut hints visible (← drop, → keep, ↑ maybe, Z undo), category filter chips at top, daily new count badge, progress indicator (X ideas remaining today)
- **Screen 2: Idea Card Component** — title (large), tagline (medium, italicized), 2–3 sentence description, category tag pill, estimated build complexity tag (Weekend / 2 Weeks / Month), "who would build this" label (indie dev / startup / hobbyist)
- **Screen 3: Idea Detail / Expanded View** — full idea content, personal notes textarea (auto-saved), status selector (Saved / Actively Building / Archived), back button, move-to-maybe / move-to-build actions
- **Screen 4: BUILD Queue** — list/grid of saved ideas, sortable by date saved / category / complexity, each item shows title + category tag + date saved, click to open detail, bulk archive action, empty state illustration
- **Screen 5: MAYBE Pile** — same layout as BUILD queue but for undecided ideas, "revisit" action moves card back into feed, empty state
- **Screen 6: Tag / Category Filter** — modal or sidebar, checkbox list of categories (SaaS, Mobile, Dev Tools, Games, Consumer, AI, Hardware), "show all" default, saved as user preference
- **Screen 7: Onboarding** — first-run only, 3 steps: (1) pick your builder type, (2) select categories you build in, (3) keyboard shortcut tutorial with a practice card
- **Screen 8: Empty States** — (a) feed exhausted: "You've seen everything for today. X new ideas tomorrow." with BUILD queue CTA; (b) empty BUILD queue: "Nothing saved yet. Start swiping." with feed CTA

Key interactions:
- **Primary flow:** Land on Swipe Feed → keyboard arrow or click to swipe → idea lands in BUILD or gets dropped → repeat until feed exhausted → visit BUILD queue to review/prioritize
- **Detail flow:** Click any card in feed (without swiping) → Idea Detail opens → read, add notes → swipe action from detail view → return to feed
- **Queue management flow:** Open BUILD Queue → click idea → Idea Detail → change status to "Actively Building" or archive → back to queue
- **Revisit flow:** Open MAYBE Pile → click revisit → card re-enters feed as next card to swipe
- **Filter flow:** Click category filter → select tags → feed updates immediately to show matching ideas first

## Version History

- v1: Original idea — swipe mechanic + community voting + AI learning + submissions
- v1.1: Critique — cold start problem, AI vaporware, social vs personal identity crisis, desktop UX mismatch
- v2: Improved — personal-first tool, pre-seeded feed, web-first keyboard UX, MAYBE pile + undo, no community features until v2
