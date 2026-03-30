# Idea #33: (Improved)
## Version: v2
**Date:** 2026-03-30 22:36
**Status:** improved
## Original Idea
Walk Garden - each step equals 1 gallon water - water trees in your garden - watch your garden bloom as you walk - trees need 500-2000 gallons to grow - unlock new tree species as you grow more - bonsai, cherry blossom, oak - watch tree grow visually - no streak penalty - build your forest
## What Changed and Why
- Replaced the endless passive "forest grows as you walk" loop with a finite 21-day garden challenge made of 6 plots. This fixes the biggest product problem in the critique: users now have a clear finish line, a visible artifact to complete, and a real reason to return after the novelty of day 1 wears off.
- Added actual decisions by turning plants into seed cards with different walking rules and by letting users choose which seeds to plant in which plots each week. This fixes the "wallpaper with a progress bar" problem because users are no longer just waiting for steps to fill a meter; they are shaping the garden through tradeoffs.
- Cut the art and platform risk down to something a solo developer can ship: iPhone-only, Apple Health step read only, 4 seed types, 3 plant states each, static layered illustrations instead of animation-heavy tree growth. This fixes the biggest solo-dev traps the critique identified: sync friction, arbitrary balancing, and an asset treadmill that would make the app feel cheap if under-produced.
## Improved Description
Walk Garden v2 is a calm 21-day walking challenge for iPhone, not an endless pet game. The user is building one small personal garden with 6 planting plots, unlocking 2 new plots each week for 3 weeks. Apple Health supplies the daily step count automatically. Each week, the user chooses 2 seeds from a small pack of 4, then decides where to plant them in the garden.

Each seed has a different and legible walking rule:
- Calm Moss: bloom by hitting 5,000 steps on 3 days this week
- Steady Fern: bloom by hitting 5,000 steps on 5 days this week
- Long Walk Lily: bloom by hitting 8,000 steps on 2 days this week
- Comeback Clover: bloom by missing a day, then hitting 5,000 steps on the next 2 days

This gives the user a real decision: play for consistency, aim for one or two bigger days, or use a forgiving comeback pattern if life gets messy. Missed goals do not destroy the garden. Failed seeds turn into neutral stone planters, so the garden still records reality without guilt.

The product promise is no longer "watch a tree slowly fill up." It is "finish a 21-day garden that reflects how you actually walked." By day 21, the user has a complete small garden composition they can save or share as a finished postcard. The value is the finished artifact plus the daily feeling of steering it, not endless species unlocks.

Why would users come back in week 2? Because week 1 resolves into the first permanent part of their garden, 2 new plots unlock, and the week-2 seed choices meaningfully change the final composition. They are midway through making something with a visible end state, not restarting the same loop.

What makes this better than a step counter plus a wallpaper-level reward is the combination of passive step sync, constrained weekly choices, and a finished outcome. Apple Health already counts steps; this app gives those steps a small strategic layer and a calm, expressive result.
## Why This Is Worth Building (Solo + AI)
This is viable for a solo developer because the scope is now narrow and concrete: one native iPhone app, one HealthKit read-only integration, one 21-day loop, and a tiny authored content set. AI coding tools can accelerate the SwiftUI state machine, HealthKit queries, garden rendering, and share export, while the content prep stays bounded enough to finish before launch.
## Solo MVP Scope
- What's in v1: iPhone-only app, Apple Health read-only step sync, one 21-day run at a time, fixed thresholds of 5,000 and 8,000 steps, 6 total plots, 4 seed types, 3 plant states per seed (seedling, growing, bloom), neutral stone-planter outcome for failed seeds, garden map view, end-of-run postcard export, and lightweight analytics for permission success, day-7 return, and day-14 return.
- What's out of v1: Android, web, manual step entry for public users, endless forest mode, species unlock ladders, dozens of tree types, weather systems, seasons, animation-heavy growth, social feed, friends, UGC gardens, moderation, monetization, payments, widgets, watch apps, AI-generated art, and any marketplace/community loop.
- Estimated build time with AI coding tools: 3 to 3.5 weeks total, including 2 days to define the 4 seed rules and 6-plot progression, 2-3 days to prepare the plant art set and metadata, 3 days for Apple Health integration and permission/error states, 5-6 days for SwiftUI screens and garden-state logic, 2 days for postcard export and share flow, 1 day for analytics setup, and 4-5 days for device testing, sync edge cases, polish, and TestFlight packaging.
## Open Questions
- 3 biggest assumptions that could kill the product:
- VERIFIED: Apple HealthKit exposes `stepCount` data and supports read queries for daily step totals, so an iPhone-only read-only step source is technically real. It is still an implementation risk, but not vaporware. Source checked on Apple Developer documentation on 2026-03-30.
- UNVERIFIED: Users will care enough about finishing a 21-day garden to come back in week 2 and week 3. This cannot be claimed as a moat yet. Fallback plan: test a crude prototype with fake or internal step data and 10-15 users before investing in more art or any post-MVP content.
- FALSE: Unlocking more tree species is a strong retention mechanic on its own. The critique correctly showed this is weak collector bait, so species unlocks are removed from the MVP pitch rather than reframed under a different name.
- Does the Comeback Clover rule feel motivating in testing, or does it confuse users compared with simpler consistency goals?
- Is 5,000 / 8,000 the right pair of thresholds for a broad casual audience, or does one threshold need to move after the first tester cohort?
- Are 4 seed types enough for 21 days of variety, or does the app need one extra seed rule before it stops feeling repetitive?
- If Health permission acceptance or sync reliability is materially worse than expected, should the product stop at private prototype stage rather than forcing a weaker public fallback?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Welcome / 21-Day Pitch — explain the concept in one screen, show a sample finished 6-plot garden, and communicate that the app reads Apple Health step counts to grow a garden over 21 days.
- Screen 2: Apple Health Access — request read permission for step count, explain exactly what is read and what is not, and show success, denied, and retry states.
- Screen 3: Start Run / Garden Preview — show the empty 6-plot garden layout, explain the 3-week structure, show the two step thresholds, and start the user's first 21-day run.
- Screen 4: Weekly Seed Pack — show the 4 available seed cards for the current week, each with plant illustration, rule text, difficulty cue, and bloom reward preview; user picks 2 seeds for this week.
- Screen 5: Plot Placement — let the user place the 2 chosen seeds into the newly unlocked plots for that week; show the current garden map and simple slot previews.
- Screen 6: Home / Current Garden — primary dashboard showing today's synced step count, this week's seed progress, current week number, current garden state, and whether today's activity advanced any seed.
- Screen 7: Seed Detail — focused view for one seed showing its rule, progress this week, state illustration (seedling, growing, bloom, or stone planter), and short copy explaining what will happen by week end.
- Screen 8: Week Resolution — end-of-week summary showing which seeds bloomed, which became stone planters, how the garden changed visually, and the CTA to start the next week's seed selection.
- Screen 9: Final Garden / Postcard — completion screen for day 21 showing the finished 6-plot garden, run summary, garden title, and save/share postcard action.
- Screen 10: History / Past Gardens — simple archive of completed runs with thumbnail postcard, completion date, and tap-through to full final garden view.
- Screen 11: Settings — Apple Health status, optional daily reminder toggle, analytics/privacy copy, reset current run, and help.
Key interactions:
- User opens the app, reads the 21-day pitch, grants Apple Health access, and starts a run.
- At the start of each week, the user selects 2 seeds from the weekly pack and places them in the newly unlocked plots.
- Home updates automatically from Apple Health data and shows how today's steps affected each active seed.
- At week end, the app resolves each active seed into bloom or stone planter, updates the garden, and moves the user into the next week's seed pack.
- On day 21, the user lands on the final postcard screen and can save or share the finished garden.
- The user can open History at any time after completing at least one run to compare finished gardens.
## Version History
- v1: Original idea
- v1.1: Critique - pleasant metaphor but not a product, no week-2 retention, no decisions, art/content burden too high, Health integration risk ignored
- v2: Improved - reframed as a finite 21-day iPhone challenge with weekly seed choices, a 6-plot expressive garden, bounded assets, and a clear week-2 return reason
