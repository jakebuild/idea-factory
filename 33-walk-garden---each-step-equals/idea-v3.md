# Idea #33: Walk Garden (Improved)
## Version: v3
**Date:** 2026-03-31 00:43
**Status:** improved

## Original Idea
Walk Garden - each step equals 1 gallon water - water trees in your garden - watch your garden bloom as you walk - trees need 500-2000 gallons to grow - unlock new tree species as you grow more - bonsai, cherry blossom, oak - watch tree grow visually - no streak penalty - build your forest

## What Changed and Why

- **Added a seasonal collection system to fix the day-21 cliff.** The critique's single most damaging observation was: "Why does the user come back after day 21? Right now the honest answer is: maybe they do not." v2 had no answer. v3 introduces four seasonal garden themes — Spring, Summer, Fall, Winter — each with its own visual palette and seed pack. Each 21-day run produces one seasonal garden. The user builds a set of four over time. The motivation is now "finish your seasonal collection," not "watch the same garden again." The architecture supports this from day 1; the MVP ships Spring only, with Summer visually teased on the History screen. This adds bounded art scope (one extra palette per season, not 4x content upfront) and answers repeat-run motivation without requiring any network effects, UGC, or social infrastructure.

- **Replaced Comeback Clover with Peak Day Poppy.** The critique flagged Comeback Clover as a rule that "normal users misread" and that risks teaching users to intentionally game failure. It is cognitively unlike every other seed. v3 replaces it with Peak Day Poppy: bloom by walking 10,000+ steps on at least 1 day this week. One condition, one sentence, no failure edge cases. The four seeds now all follow the same "hit X steps on Y days" grammar, making the strategy layer legible at a glance rather than requiring an explanation.

- **Added daily micro-feedback to fix midweek dead air.** The critique identified that midweek usage felt like: "open app, see number, nothing dramatic changes, close app." v3 adds a daily sync reaction to each active seed — a one-line status blurb that updates whenever Apple Health data comes in. "Your Steady Fern soaked up 6,400 steps today. 3 of 5 days done." This is zero extra logic (the data is already computed) and costs only one label component per seed card. It gives users a small reward for checking in midweek without adding any new state machine complexity.

- **Made art validation a pre-build gate.** The critique said "if the illustrations are mediocre, the whole thing collapses." v3 makes the Spring palette postcard a build prerequisite, not a build output. Before writing a line of SwiftUI, the solo dev produces one finished garden postcard at full resolution and validates it with 5 people. If it does not look beautiful, the app does not get built. This replaces the optimistic assumption that art will be "good enough" with an explicit kill switch.

## Improved Description

Walk Garden v3 is a calm 21-day walking challenge for iPhone that produces a finished seasonal garden — a personal artifact that reflects exactly how you walked. Each run is themed to the season you start it in: Spring brings cherry blossoms and ferns, Summer brings sunflowers and tall grasses, Fall brings maples and late blooms, Winter brings pine and frost-tolerant moss. The visual palette changes between runs, so each garden in your archive looks and feels distinct.

Apple Health provides the daily step count automatically. Each week, the user chooses 2 seeds from a pack of 4 and plants them in 2 newly unlocked plots. Each seed has a legible walking rule:

- **Calm Moss** — bloom by walking 5,000+ steps on 3 days this week
- **Steady Fern** — bloom by walking 5,000+ steps on 5 days this week
- **Long Walk Lily** — bloom by walking 8,000+ steps on at least 2 days this week
- **Peak Day Poppy** — bloom by walking 10,000+ steps on at least 1 day this week

The strategy is choosing the right seed for your week, not grinding a single optimal path. Seeds that miss their goal turn into neutral stone planters — the garden records reality, not a fantasy version of your week.

Every day, the Home screen shows a short status update per active seed: how far along each is based on today's synced steps. This makes midweek check-ins feel rewarding rather than empty.

On day 21 the user gets a finished postcard of their garden. The History screen shows all completed seasonal gardens. After Spring, the Summer garden is teased with a lock icon, giving users an explicit next thing to earn. The collection arc is: four seasons, four gardens, one archive. That is the retention loop.

**Why would users come back in week 2?** Because the garden is visibly incomplete — 4 plots are filled, 2 remain, and week 2 seed choices will change the final composition. **Why would users start a second run?** Because Summer is already waiting, it looks visually different from Spring, and the archive now has a gap to fill.

## Why This Is Worth Building (Solo + AI)

A solo dev can ship this because the scope is narrow, the content set is bounded, and the architecture is additive: one seasonal palette at launch, three more as post-MVP drops. The art-first validation step protects the investment — if the Spring postcard does not look good enough to share, the builder stops before sinking 5 weeks into a broken premise. The seasonal collection arc solves the core retention problem the critique identified without adding servers, social graphs, or moderation.

## Solo MVP Scope

- **What's in v1:** iPhone-only app, Apple Health read-only step sync, one 21-day run at a time, Spring seasonal theme only (Summer teased on History), 4 seed types with single-condition rules (Calm Moss / Steady Fern / Long Walk Lily / Peak Day Poppy), 6 total plots, 3 plant states per seed (seedling → growing → bloom), neutral stone-planter for failed seeds, daily seed status blurb on Home screen, garden map view, end-of-run postcard export, History archive of completed runs, lightweight analytics for permission rate / day-7 return / day-14 return / run-2 start rate.
- **What's out of v1:** Summer / Fall / Winter seasonal themes (architecture supports, content ships later), Android, manual step entry, endless forest mode, species unlock ladders, animation-heavy plant growth, Comeback Clover or any rule with a "miss a day" condition, social feed, friends, UGC gardens, moderation, monetization, payments, widgets, watch app, AI-generated art.
- **Estimated build time with AI coding tools:** 4.5–5 weeks total, broken down as: 2 days art validation (produce one finished Spring postcard, show 5 people, go/no-go decision), 3 days Spring content prep (4 seed definitions, 6-plot layout, 3-state plant art per seed = 12 plant illustrations + 4 stone planter states + background), 1 day seasonal palette architecture (content-addressed so future seasons are a data drop), 3 days Apple Health integration and permission/error states, 6–7 days SwiftUI screens and garden-state machine (seed selection, plot placement, daily sync, week resolution, postcard, history), 2 days daily micro-feedback logic and Home screen polish, 2 days postcard export and share flow, 1 day analytics setup, 5–6 days device testing, HealthKit sync edge cases, onboarding polish, and TestFlight packaging.

## Critical Assumptions Check

1. **VERIFIED:** Apple HealthKit exposes `stepCount` as a readable quantity and supports per-day queries for rolling 7-day windows. The integration is a real implementation risk (edge cases around permission denial, background sync timing, first-session data availability) but is not vaporware.

2. **UNVERIFIED:** The Spring garden postcard looks beautiful and distinctive enough that users want to share it and start Summer to complete the collection. This is the entire product bet. It cannot be assumed before the art is made. **Fallback:** art validation is a pre-build gate in this plan. If the postcard fails the "would I share this?" test with 5 real people, the idea is shelved rather than built.

3. **UNVERIFIED:** Users will start a second run after completing Spring. The seasonal tease and archive gap are the proposed mechanic, but this remains unproven. **Fallback:** treat run-2 start rate as the primary success metric in the first release. If it is near zero after 4 weeks of data, do not build Summer content — retire the app and document the learning.

## Open Questions

- Does the seasonal tease (Summer locked on History) land as motivating or frustrating? Test with the Spring postcard prototype before committing to the full flow.
- Are 4 seed types enough variety across 3 weeks of the same run, or does week 3 feel like a re-selection of the same two good seeds? Consider whether a fifth rule is needed for v1 or is truly post-MVP.
- What is the right threshold for Peak Day Poppy for a casual audience — 10,000 steps may be too high for some users. Should this be user-adjustable or fixed?
- If Apple Health permission is denied, what is the fallback? Manual daily step entry for beta/TestFlight only, with no public fallback, is the correct call — but confirm this before building the denied state screens.
- Does the stone-planter failure state need copy explaining why (seed didn't meet its rule) or is the visual sufficient? Decide before designing Seed Detail.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Welcome / Seasonal Pitch** — introduce the concept with a sample finished Spring garden postcard, explain that Apple Health steps grow real plant illustrations over 21 days, and show the four seasonal garden icons (Spring / Summer locked / Fall locked / Winter locked) as a collection preview. CTA: Get Started.
- **Screen 2: Apple Health Access** — full-screen permission request explaining exactly what is read (step count, nothing else), with three states: default ask, permission granted (with brief success animation), permission denied (with fallback instructions to enable in Settings). No manual entry offered.
- **Screen 3: Start Run / Empty Garden Preview** — show the 6-plot garden layout for the Spring theme with all slots empty, explain the 3-week structure (2 new plots unlock each Monday), highlight the two active week-1 plots, and show the CTA to pick this week's seeds.
- **Screen 4: Weekly Seed Pack** — show the 4 seed cards for the current week. Each card: plant illustration in seedling state, seed name, rule text in one sentence, difficulty indicator (low / medium / high), and bloom preview. User selects 2. Deselected 2 fade but remain visible. CTA: Plant These.
- **Screen 5: Plot Placement** — show the current garden map with this week's 2 newly unlocked plots highlighted. User drags or taps to assign each chosen seed to a plot. Confirmation state shows the two placements before committing.
- **Screen 6: Home / Current Garden** — primary daily screen. Top: today's synced step count and a one-line sync status ("Updated 12 minutes ago"). Middle: current garden illustration with all active and resolved plots visible. Bottom: cards for each active seed showing name, rule, progress bar, and daily status blurb ("Your Steady Fern soaked up 6,400 steps today — 3 of 5 days done"). Tap any seed card to go to Seed Detail.
- **Screen 7: Seed Detail** — focused view for one seed. Full-size plant illustration in current state, rule text, 7-day step history for the current week (bar chart, each day colored if it counted toward the rule), current progress (e.g., 3 of 5 days), and projected outcome copy if the week ended now. Read-only — no actions except Back.
- **Screen 8: Week Resolution** — end-of-week summary triggered when the week closes. Show each resolved seed with its final state (bloom or stone planter), one line of copy per outcome ("Your Calm Moss bloomed." / "Your Long Walk Lily became a stone planter — you hit 8,000 on 1 of 2 needed days."), animated transition of the garden updating, and CTA to begin next week's seed pack. Week 3 resolution flows directly to Final Garden.
- **Screen 9: Final Garden / Postcard** — full-screen finished Spring garden with all 6 plots resolved. Garden title (auto-generated from season + completion date, editable). Summary stats: total steps walked, plots bloomed vs stone planters. Save to Photos and Share Sheet buttons. Postcard framing (fixed aspect ratio, clean border, season watermark).
- **Screen 10: History / Seasonal Archive** — grid of all completed gardens as postcard thumbnails. Spring (completed, tappable), Summer (locked, greyed, "Start after Spring"), Fall (locked), Winter (locked). Tapping a completed garden opens its final postcard full-screen. Tapping a locked season shows a preview of its color palette and a prompt to finish the current run.
- **Screen 11: Settings** — Apple Health connection status and re-request button, optional daily reminder time picker, current run reset (with explicit confirmation), privacy / analytics copy, app version.

**Key interactions:**

- **Onboarding to first seed pick:** Welcome → Health permission → empty garden preview → weekly seed pack → plot placement → Home. This is the first-session critical path and must be completable in under 3 minutes.
- **Daily check-in loop:** Open app → Home shows updated step count and per-seed status blurb → optionally tap seed card for detail → close. Designed to be satisfying in 30 seconds.
- **Week resolution:** Triggered the first time the user opens the app after the 7-day window closes (not a push notification forced flow). Week Resolution screen → garden updates visually → CTA to next week's seed pack.
- **Run completion:** Week 3 resolution → Final Garden postcard → save/share → History shows Spring as completed → Summer teased.
- **Second run start:** User taps locked Summer on History → sees palette preview and "Start Summer Garden" CTA → begins a new 21-day run with Summer theme and a new seed pack variant.

## Version History
- v1: Original idea
- v1.1: Critique — pleasant metaphor but not a product, no week-2 retention, no decisions, art/content burden too high, Health integration risk ignored
- v2: Improved — reframed as a finite 21-day iPhone challenge with weekly seed choices, 6-plot garden, bounded assets, and a clear week-2 return reason
- v2.1: Critique — retention after day 21 still unanswered, Comeback Clover rule is confusing, midweek dead air, art dependency unresolved, build estimate optimistic
- v3: Improved — seasonal collection arc answers day-21 cliff, Comeback Clover replaced with simpler Peak Day Poppy rule, daily micro-feedback added, art-first pre-build gate added, honest 4.5–5 week estimate
