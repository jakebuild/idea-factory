# Idea #32: (Improved)
## Version: v3
**Date:** 2026-03-30 20:46
**Status:** improved
## Original Idea
Walk City - your steps build a city that grows with you - Every day your steps generate a unique cityscape - Walk 5000 steps modest town, 15000 steps towering metropolis - different times create different lighting - miss a day city sleeps - AI procedurally generates based on your data - collection grows daily - revisit at night to see where you walked
## What Changed and Why
- Reframed the product from a passive daily city reveal into a weekly walking commitment: one active civic project at a time, completed by hitting 5 walk days in 7 days. This fixes the weak retention loop because users are now working toward a legible near-term goal instead of just watching wallpaper appear.
- Cut arbitrary landmark and district reward math from v1 and replaced it with six handcrafted project types with clear themes and rules: park, market, library, plaza, riverwalk, and transit stop. This makes progression easier to understand, reduces content-prep overhead, and avoids the feeling of random gamification glue.
- Treated HealthKit as a risky integration, not the core value. V1 now includes a manual daily step entry fallback and a seeded demo mode, so the product can still be tested and shipped if native health sync becomes a solo-dev time sink.
## Improved Description
Walk City v3 is a weekly walking companion, not a step wallpaper app. Each week the user picks one small civic project to build in their city, such as a park or library. Every day they hit their chosen step threshold, that project gains one visible construction stage. Reach 5 walk days within 7 days and the project is completed permanently on the city map. Miss the week and nothing is destroyed, but the project remains unfinished and the city clearly shows what was left incomplete.

The product goal is now explicit: help users complete 5 meaningful walk days per week. The city is the motivation layer, not the product by itself. That makes the positioning cleaner: "Build one real part of your city each week by completing your walk plan."

The core loop is simple:
- Monday or first open of the week: choose one civic project
- Each day: sync or enter step count, see whether today counted as a walk day, and watch the active project advance one stage
- End of week: complete the project and place it on the permanent city map, or carry an unfinished construction site into the next week as a visible reminder

The rules are explainable and thematic:
- A walk day counts only if the user hits their selected threshold for that week: 4k, 6k, or 8k steps
- Each counted day adds one build stage to the active project
- 5 counted days completes the project
- Different project types require the same effort but create different permanent city functions and visuals
- The city grows one meaningful piece at a time, not one arbitrary block per day

This is a tighter product because it answers the retention problem directly. Why would users come back in week 2? Because they have a new weekly build to choose, a visible standard to hit 5 times, and an accumulating city that reflects completed weeks versus abandoned ones. The return reason is not novelty. It is finishing the next civic project and avoiding a city full of half-built sites.

The build also stays honest about risk:
- HealthKit sync is optional for the product pitch, not the moat
- Manual entry fallback keeps the prototype viable
- Seeded demo mode lets the builder test the loop before platform work is done
- Content scope is capped at 6 project types x 5 build stages each, with fixed copy and metadata prepared up front

3 biggest assumptions that could kill the product:
- UNVERIFIED: Users will care enough about completing one weekly civic project to return for at least 2 weeks. Fallback plan: if early testers do not finish 2 consecutive projects, reposition the app as a short novelty prototype and stop before adding more content.
- UNVERIFIED: HealthKit step reads can be integrated cleanly in the chosen stack within a 2-day spike. Fallback plan: ship v1 with manual entry plus demo mode and treat health sync as a post-validation upgrade, not part of the moat.
- FALSE: A passive "open app, see what got built" reveal is strong enough to retain users. The critique already invalidated this, so v1 no longer depends on passive reveals as the main loop.
## Why This Is Worth Building (Solo + AI)
This version is worth building because it has one concrete behavior goal, one weekly retention loop, and a tightly limited content system that a solo developer can produce with AI-assisted coding and asset generation. It is viable in 2-4 weeks because the renderer only needs to support six handcrafted project templates with five stages each, and the app can still validate the loop without waiting on perfect HealthKit integration.
## Solo MVP Scope
- What's in v1: iPhone-first app, weekly project selection, 4k/6k/8k walk-plan choice, one active project at a time, permanent city map, 6 handcrafted project types with 5 construction stages each, step sync attempt via HealthKit, manual daily step entry fallback, seeded demo mode, simple weekly recap, and lightweight analytics for project completion and week-2 return.
- What's out of v1: GPS/history, route maps, "see where you walked," AI-generated art, infinite block variants, district detail screens, daily summary screen, social features, sharing loops beyond a native screenshot, notifications beyond one optional weekly reminder, Android, web, UGC, moderation, payments, streak punishments, and any marketplace or community layer.
- Estimated build time with AI coding tools: 3 to 4 weeks total, including 2 days for HealthKit spike, 2 days for manual-entry and demo fallback flows, 5-6 days for UI build, 4-5 days for city/project renderer tuning, 2 days for weekly progression logic and recap states, 2-3 days for content prep and metadata structuring for 6 project types x 5 stages, 1 day for analytics/instrumentation, and 3-4 days for device testing, polish, and TestFlight setup.
## Open Questions
- Does the builder choose SwiftUI to reduce HealthKit risk, or is there already a proven React Native stack that makes shipping faster?
- Which weekly threshold produces the best balance of challenge and success for early testers: 4k, 6k, or 8k?
- Are unfinished construction sites motivating enough, or do they read as guilt-inducing clutter in user tests?
- Which 6 civic project themes feel most desirable and universal for v1 without requiring custom illustration work beyond the available content budget?
- If HealthKit is unreliable in the real stack, is manual entry acceptable for the first TestFlight, or does that kill the product's perceived value?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Onboarding — explain the weekly loop, show one example city with a finished and unfinished project, let the user choose a first goal style, and set expectations around walk-day counting.
- Screen 2: Data Setup — request Health permissions, explain manual entry fallback, show success/error states, and offer seeded demo mode if sync is skipped.
- Screen 3: Home / Active Project — primary screen showing current week, selected step threshold, today's counted/not-counted status, active project with current construction stage, progress to 5 walk days, and CTA to add/sync steps.
- Screen 4: Add Steps / Sync Result — focused flow for pulling today's steps or entering them manually, then confirming whether the day counted and how the project changed.
- Screen 5: City Map — overview of the permanent city showing completed civic projects, unfinished sites, and current active build in context.
- Screen 6: Choose Next Project — selection screen shown at the start of a week or after completion, with 3 available project cards, short thematic descriptions, and preview of how each changes the city.
- Screen 7: Weekly Recap / Project Complete — end-of-week summary showing counted walk days, project outcome, city update, and CTA to pick next week's build.
- Screen 8: Settings — permissions status, demo mode toggle, manual entry defaults, optional reminder setting, reset city, and privacy/help text.
Key interactions:
- User completes onboarding, connects HealthKit or selects fallback mode, then lands on Home with one active weekly project.
- From Home, user syncs or enters steps, sees whether the day counted, and watches the active project advance.
- User opens City Map to inspect completed and unfinished projects in the persistent city.
- At the start of a new week or after project completion, the user goes to Choose Next Project and starts the next build cycle.
- At week end, Weekly Recap explains the outcome and routes back into the next project choice.
## Version History
- v1: Original idea
- v2.1: Critique - daily reveal loop was too toy-like, rewards were arbitrary, retention was weak, and HealthKit/content scope were underestimated
- v3: Improved - weekly civic project loop with manual fallback, tighter content scope, and a clearer reason to return in week 2
