# Idea #33: (Improved)
## Version: v3
**Date:** 2026-03-30 22:43
**Status:** improved
## Original Idea
Walk Garden - each step equals 1 gallon water - water trees in your garden - watch your garden bloom as you walk - trees need 500-2000 gallons to grow - unlock new tree species as you grow more - bonsai, cherry blossom, oak - watch tree grow visually - no streak penalty - build your forest
## What Changed and Why
- Replaced the six-plot weekly-resolution game with a 21-day garden page that updates every day. This fixes the dead-air problem from the critique: users now see a concrete visual change each day instead of waiting for week-end payoff.
- Removed the seed-rule layer from MVP, including the confusing comeback mechanic, and replaced it with four simple daily step bands that map directly to four plant outcomes. This is better because users can understand the system in seconds, and the app no longer depends on thin strategy pretending to be depth.
- Made the product an explicit retention experiment around a private archive of finished garden pages, not a “business” with fake moat language. This is more viable for a solo builder because the content set is small, the art can be modular, and the key question becomes measurable: do people start a second page after finishing the first?
## Improved Description
Walk Garden v3 is a 21-day walking scrapbook for iPhone. Each day, Apple Health reads the user’s step total and fills one tile on a 21-tile garden page. The tile outcome is immediate and legible:

- under 4,000 steps: pebble / dormant tile
- 4,000-6,999 steps: sprout tile
- 7,000-9,999 steps: bloom tile
- 10,000+ steps: flourish tile

Instead of managing seed rules, the user chooses one of two garden templates at the start of a run: Courtyard or Wild Patch. The template changes the layout, border art, and final postcard framing, but not the underlying step logic. Over 21 days, the page becomes a visual record of how the user actually moved, with each row representing one week. Weekly row completion adds a simple structural flourish such as a path segment, bench, or stone border so week 2 and week 3 feel like progression rather than more of the same.

This solves the critique’s pacing problem because the app has both daily and weekly feedback. Open the app midweek and today’s tile has changed. Finish a week and the page structure grows. Miss a day and the page still records it cleanly with a pebble tile instead of inventing a failure mini-game.

Why would users come back in week 2? Because the page is unfinished, daily tiles keep changing, and the second row visibly opens after day 7. The user is not waiting for a distant postcard; they are filling a page that already looks different every day.

Why would users come back after day 21? The honest answer is still unproven, so v1 treats this as a test. The app stores each finished 21-day page in a private Garden Archive, and the completion screen invites the user to start a second page with the other template. If users do not start a second run, the concept has failed the retention test and should not be expanded.

What makes the artifact more distinctive now is that it reflects daily variation, not just six resolved plot outcomes. Two users with similar totals can still end up with visibly different pages because their high-step and low-step days land in different positions across the 21-day grid. The share/export feature remains optional frosting, not the product promise.
## Why This Is Worth Building (Solo + AI)
This is worth building because it is now a sharp product test: can a simple, beautiful 21-day step artifact make users complete one page and start another. A solo developer can ship it with AI coding tools because the system is mostly straightforward SwiftUI state, one HealthKit read-only integration, a small modular art kit, and a bounded archive/export flow rather than a rule-heavy game.
## Solo MVP Scope
- What's in v1: iPhone-only app, Apple Health read-only step sync, one active 21-day run at a time, 21 daily tiles, 4 fixed step bands, 2 garden templates, 1 modular art kit for tiles and weekly row flourishes, daily auto-sync on app open, final postcard export, private Garden Archive of completed pages, and analytics for permission success, day-7 return, run completion, and second-run starts.
- What's out of v1: seed packs, plot placement, Comeback Clover or any miss-a-day mechanic, species unlock ladders, endless forest mode, social sharing loops beyond native export sheet, friends, community galleries, user-generated gardens, moderation, Android, web, widgets, Apple Watch app, monetization, payments, dynamic seasons, AI-generated art, and any feature that requires more content to fake retention.
- Estimated build time with AI coding tools: 3.5 to 4 weeks total, including 3 days for product rules, data model, and archive logic; 3-4 days to create and test the modular art kit plus template metadata; 3 days for HealthKit integration, permission states, and sync edge cases; 5-6 days for SwiftUI screens and tile/rendering logic; 2 days for archive and postcard export; 1 day for analytics setup; and 5-6 days for onboarding polish, device testing, permission-denial paths, delayed-sync handling, and one round of design iteration after seeing the artifact on device.
## Open Questions
- 3 biggest assumptions that could kill the product:
- VERIFIED: Apple HealthKit can provide read-only step-count data for an iPhone app, and Apple documents both authorization flow and step-count query support. This technical dependency is real, though still an implementation risk.
- UNVERIFIED: Users will care enough about a finished 21-day garden page to start a second run. This cannot be presented as the moat. Fallback plan: measure second-run starts in the first tester cohort; if they are weak, stop expanding templates and reposition the app as a one-shot seasonal experiment rather than a product.
- UNVERIFIED: A small modular art system can make the final page feel beautiful and personal enough without a large illustration budget. Because this is unverified, the first prototype should focus on the final page aesthetic before any extra feature work.
- Does the daily tile system feel motivating enough without any seed-choice layer, or does it become too passive in user testing?
- Are two templates enough to create a real second-run impulse, or do testers perceive them as cosmetic only?
- How often do Health permission denial or delayed step updates break the first-session experience?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Welcome / 21-Day Pitch — explain the product in one screen, show a sample finished 21-tile garden page, and frame the app as a 21-day walking scrapbook powered by Apple Health.
- Screen 2: Apple Health Access — request read access for step count, explain privacy boundaries, and show success, denied, retry, and “not available” states.
- Screen 3: Choose Template / Start Run — let the user pick Courtyard or Wild Patch, preview the empty 21-day page for each, show start date and end date, and start the run.
- Screen 4: Home / Current Page — primary dashboard with today’s step total, current day number, current week row, 21-tile garden page, legend for the 4 tile outcomes, and sync status.
- Screen 5: Daily Tile Detail — tap a tile to see date, synced step count, resulting tile state, short explanation of the threshold it hit, and whether that week row is complete.
- Screen 6: Weekly Row Complete — lightweight transition state shown on day 7 and day 14, revealing the new row flourish, summarizing that week’s tile mix, and returning the user to Home.
- Screen 7: Final Garden / Completion — show the finished 21-day page, completion stats, title field, “start next page” CTA, and save/share postcard action.
- Screen 8: Garden Archive — list past completed pages with thumbnail, template used, completion date, and tap-through into a finished-page detail view.
- Screen 9: Archived Page Detail — full-screen view of one completed page with title, date range, tile breakdown, and export action.
- Screen 10: Settings / Help — Health permission status, reminder toggle, privacy copy, FAQ for sync timing, reset current run, and support contact.
Key interactions:
- User reads the pitch, grants Apple Health access, chooses a template, and starts a 21-day run.
- Each day the app syncs steps, converts that total into one tile outcome, and updates the current garden page on Home.
- User taps any tile to inspect the day’s result and understand the thresholds.
- On day 7 and day 14, the app shows a weekly row-complete moment before returning the user to the growing page.
- On day 21, the user lands on the finished page, saves or shares it, and can immediately start a second run with the other template.
- After at least one completed run, the user can browse the Garden Archive and reopen any finished page.
## Version History
- v1: Original idea
- v2.1: Critique - retention after day 21 was unproven, the seed strategy was too thin, Comeback Clover added confusion, and the app still leaned too hard on art quality
- v3: Improved - reframed as a daily-updating 21-day garden scrapbook with simpler rules, stronger midweek feedback, and an explicit second-run retention test
