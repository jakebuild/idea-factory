# Idea #32: (Improved)
## Version: v4
**Date:** 2026-03-30 20:50
**Status:** improved
## Original Idea
Walk City - your steps build a city that grows with you - Every day your steps generate a unique cityscape - Walk 5000 steps modest town, 15000 steps towering metropolis - different times create different lighting - miss a day city sleeps - AI procedurally generates based on your data - collection grows daily - revisit at night to see where you walked
## What Changed and Why
- Changed the product from an endless weekly city-builder into a 28-day neighborhood challenge with a clear finish line. This fixes the biggest structural problem: the app no longer depends on infinite novelty or guilt to survive after week 2. The promise is now concrete: complete one month of walking and finish one neighborhood you can keep.
- Removed manual step entry from the shipped MVP and made the app iPhone-first and HealthKit-first. This fixes the friction problem the critique identified. If passive step sync is not reliable, the correct response is to delay or kill the public MVP, not pretend a self-reported admin flow is still magical.
- Cut content breadth and changed the emotional tone. V1 now uses 3 project archetypes, 4 weekly milestones, and neutral paused lots instead of unfinished shame states. This makes content prep realistic for one person, gives week-to-week progression a longer arc, and reduces the risk that the app makes users feel behind.
## Improved Description
Walk City v4 is no longer trying to be a forever habit app or a toy city simulator. It is a 28-day walking challenge for iPhone that turns one month of consistent walking into one finished neighborhood. The user starts a single neighborhood run, then completes one civic project per week for four weeks. Each successful week adds a meaningful district upgrade and changes the final identity of the neighborhood.

The core loop is simpler and stronger:
- Start a 28-day run and pick a neighborhood theme starter
- Each day, the app reads steps from HealthKit
- A day counts if the user reaches the fixed threshold of 6,000 steps
- Reach 5 counted days in a week and that week's civic project is completed
- At the end of 4 weeks, the user has a finished neighborhood with a name, skyline silhouette, and shareable final poster

This changes the product from "watch a cute city grow forever" into "finish one satisfying month-long artifact." That is a better market story for a solo builder because it is understandable, bounded, and testable. It does not need to out-coach fitness apps or out-sim city builders. It only needs to answer one question: does turning a month of walking into a permanent place make the behavior more compelling?

The product now has a real week-2 answer. Users come back in week 2 because they are midway through a finite challenge, not restarting the same loop. Week 2 unlocks a new civic project choice that changes the final neighborhood identity, and the home screen shows clear progress toward finishing the month. The return reason is completion of an in-progress artifact, not guilt and not endless content promises.

V1 content is deliberately narrow:
- 3 civic project archetypes: green space, culture, and street life
- 4 weekly project slots per 28-day run
- 1 fixed daily threshold: 6,000 steps
- 3 neighborhood endings based on the dominant project mix
- 12 total milestone illustrations/states that must be prepared up front

The emotional model is cleaner too. If the user misses the weekly target, the lot becomes paused, not abandoned or broken. They can still continue the 28-day run, but that week contributes a simpler filler block instead of a signature project. The app records reality without trying to punish the user into returning.

3 biggest assumptions that could kill the product:
- UNVERIFIED: Users will care enough about finishing a 28-day neighborhood to keep walking through week 2 and week 3. Fallback plan: run a fake-data prototype with 10-15 testers before polishing HealthKit edge cases; if most do not complete at least 2 weeks, stop and do not expand content.
- UNVERIFIED: HealthKit permissions and daily reads will be reliable enough in a SwiftUI iPhone app to keep the core loop passive. Fallback plan: use manual entry only for internal testing, not as the public MVP; if HealthKit remains flaky after the first implementation spike, pause the build because the shipped experience would be too weak.
- FALSE: Manual entry is an acceptable public fallback for the core experience. The critique exposed this as structurally damaging, so v1 no longer treats self-reported steps as a valid shipped mode.
## Why This Is Worth Building (Solo + AI)
This version is worth building because it is a bounded product, not a vague platform: one iPhone app, one 28-day challenge, one passive data source, and a small handcrafted content set. A solo developer can ship and test this quickly with AI-assisted coding because the scope is now mostly state management, simple HealthKit reads, and a compact art/content system rather than an endless city simulator.
## Solo MVP Scope
- What's in v1: iPhone-only app built in a native stack, HealthKit onboarding and daily read flow, one active 28-day run, fixed 6,000-step walk-day rule, 3 project archetypes, 4 weekly project slots, neutral paused-lot handling for missed weeks, final neighborhood summary/poster, and basic analytics for week-2 return, week-4 completion, and HealthKit permission success.
- What's out of v1: manual entry for public users, variable thresholds, infinite weekly play, six project types, GPS/history, route maps, "see where you walked," AI-generated art, social features, UGC, moderation, notifications beyond one optional daily reminder, Android, web, payments, streak punishments, marketplace/community loops, and any deep city-management layer.
- Estimated build time with AI coding tools: 3.5 to 4 weeks total, including 2-3 days for product/content design and structuring the 3 archetypes x 4 weekly slots x 3 endings, 3-4 days for milestone art and metadata prep, 3 days for HealthKit integration and permission/error handling, 5-6 days for the core UI build, 3-4 days for progression logic and final-poster generation, 2 days for analytics and reminder setup, and 4-5 days for device testing, polish, and TestFlight packaging.
## Open Questions
- Do fake-data testers actually feel attached to finishing a neighborhood by the end of week 2, or is the artifact still too abstract?
- Is 6,000 steps the right single threshold for a broad enough audience, or does it need to be lower to keep completion rates viable without turning the challenge into a joke?
- Does the paused-lot state feel neutral in testing, or do users still read it as failure clutter?
- Can the 12 milestone states and 3 neighborhood endings be made visually distinct enough without turning art direction into the real project?
- If HealthKit permission acceptance is materially lower than expected, is there still a viable public launch, or is this only a prototype worth testing privately?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Welcome / 28-Day Pitch — explain the one-month challenge, show a sample finished neighborhood, and set the expectation that the app works with Apple Health step data.
- Screen 2: Health Access Setup — request HealthKit permission, explain what data is read, show success and failure states, and make clear that passive sync is required for the public experience.
- Screen 3: Start Run / Theme Pick — let the user begin a new 28-day run, preview the 3 project archetypes, choose the first week's focus, and show the fixed 6,000-step rule plus weekly 5-day target.
- Screen 4: Home / Current Week — primary dashboard with current week number, today's synced step count, counted walk days this week, active project stage, next milestone, and CTA to refresh sync.
- Screen 5: Weekly Project Choice — shown at the start of weeks 2-4, offering the next project's 3 archetype options, explaining how each affects the final neighborhood identity, and previewing placement on the map.
- Screen 6: Neighborhood Map — persistent overview of the current 28-day run showing completed weekly projects, paused lots, current active slot, and the emerging neighborhood identity.
- Screen 7: Week Result — end-of-week summary showing whether the user completed the week's project, what changed on the map, how close they are to finishing the month, and the action to continue.
- Screen 8: Final Neighborhood / Share Poster — completion screen for day 28 showing the finished neighborhood name, project mix summary, full visual composition, and a save/share poster CTA.
- Screen 9: Settings — Health permission status, reminder toggle, analytics/privacy copy, reset run, and help.
Key interactions:
- User opens the app, understands the 28-day challenge, grants Health access, and starts a run with a first-week project choice.
- Each day, Home refreshes Health step data and updates whether the day counts toward the weekly target.
- At the end of each week, the user sees a week result, then chooses the next week's project if the run continues.
- User can open Neighborhood Map at any time to inspect progress and see how project choices are shaping the final identity.
- After week 4, the user lands on the final neighborhood poster screen and can save or share the result.
## Version History
- v1: Original idea
- v3.1: Critique - weekly project loop was clearer, but retention was still unproven, manual entry weakened the magic, and content/HealthKit risk were still underestimated
- v4: Improved - reframed the app as a finite 28-day HealthKit-first challenge with smaller content scope, neutral failure states, and a stronger month-long arc
