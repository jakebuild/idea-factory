# Idea #20: Habit Creature (Improved)

## Version: v2
**Date:** 2026-03-27 20:50
**Status:** improved

## Original Idea
Habit Creature — An app where you care for a pixel creature by completing your habits. Miss a habit = your creature gets sick. Streak = creature evolves into new forms. The creature is your accountability partner - it literally dies if you don't do your habits. Shareable evolution timeline.

## What Changed and Why
- **Replaced permadeath with a "recovery arc" mechanic** — The critique correctly identified that permadeath kills retention (users quit the moment they lose progress) and soft death removes all stakes. The fix: creature degrades over missed days but never fully dies — instead it enters a "critical" state where a nurse-back mini-routine unlocks recovery. Stakes stay real, users don't ragequit.
- **Narrowed to one creature, one evolution path, 5 pixel art states for MVP** — The art bottleneck was the biggest solo dev trap. With AI-generated pixel art (Midjourney/DALL-E pixel art prompts), 5 states (egg → baby → healthy → evolved → legendary) is achievable in days, not weeks. Content expansion becomes post-launch.
- **Added a social differentiator: auto-generated daily "creature card"** — Habitica exists but never nailed shareable moments. Every day you complete habits, the app auto-generates a shareable creature card (static image, no GIF complexity) with your streak and evolution stage. This is the viral loop Habitica missed — it's a daily status post, not a one-time share.
- **Focused on one habit category (fitness) for app store positioning** — Generic multi-habit trackers compete with everyone. "Fitness companion creature" has a clear search intent, a defined user (someone who wants gym accountability), and enables tighter copy/onboarding.
- **Added a clear monetization model from day one** — Cosmetic creature skins (holiday variants, color palettes) as one-time IAP. Simple, proven in mobile games, doesn't gate core functionality.

## Improved Description
Habit Creature is a mobile app where you raise a pixel creature by completing your daily fitness habits. Check in your workout → your creature glows and levels up. Skip a day → it starts wilting. Skip a week → it enters critical state and needs a recovery routine to nurse back to health.

The creature has 5 distinct pixel art states, AI-generated and crisp on any screen. You pick up to 3 fitness habits on day one (workout, steps, water). Every time you complete all habits, the app auto-generates a shareable "creature card" — your creature's current form, your streak, your stage — ready to post to Instagram Stories or X with one tap. No manual screenshot needed.

The differentiator from Habitica: Habitica is an RPG with a habit layer on top. This is a creature companion with social sharing baked in. The emotional hook is the creature, not the XP. The viral loop is the daily card post, not a guild leaderboard.

Monetization from day one: cosmetic skins (seasonal variants, color palettes) as $0.99–$2.99 IAP. Core creature and all evolution stages are always free.

## Why This Is Worth Building (Solo + AI)
The habit tracking logic and creature state machine are both simple finite state machines — exactly the kind of thing AI coding tools nail in hours, not days. AI-generated pixel art removes the art bottleneck that would sink a solo dev trying to commission sprites. The social sharing mechanic is a static image render (HTML canvas or server-side OG image) — no GIF pipeline, no ffmpeg hell.

## Solo MVP Scope
- **What's in v1:** One creature, 5 pixel art states (AI-generated), 3 fitness habit slots, daily check-in flow, creature health state machine, auto-generated shareable daily card (static image), push notifications for habit reminders + creature distress, recovery arc for critical state, cosmetic skin IAP (1–2 skins at launch)
- **What's out of v1:** Multiple creature types, custom habit categories beyond fitness, animated GIF sharing, social feed/friends, creature battles, guild/multiplayer features, web version, Apple Watch/widget integration, habit streaks per-habit (one global streak for MVP), evolution timeline history view (add post-launch)
- **Estimated build time with AI coding tools:** 2.5–3 weeks (Week 1: state machine + check-in flow + notifications; Week 2: pixel art assets + creature display + sharing card; Week 3: IAP + polish + TestFlight)

## Open Questions
- React Native or Flutter? (Flutter has better pixel rendering control for crisp sprites at non-integer scales)
- App store category: Health & Fitness vs. Games? Affects discovery and review guidelines for IAP
- Does the recovery arc require a special "nurse-back" habit (e.g., do 5 pushups to revive) or just resuming normal habits? Needs UX decision before building
- How many AI-generated sprite variants look good enough at 64x64px? Need a test render before committing to this asset strategy

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding — Creature Hatch** — First-time flow. Egg animation hatches into baby creature. User names creature, selects up to 3 fitness habits from a preset list (Workout, Daily Steps, Drink Water, Morning Stretch, Evening Walk). CTA: "Start caring for [Name]". Data: empty habits array, creature name input.
- **Screen 2: Home / Dashboard** — Primary screen. Full-screen creature pixel art centered, animates idle. Below creature: today's 3 habit checkboxes with habit names. Top bar: streak counter (flame icon + number) + creature stage label. Creature state visually changes based on health (healthy = vibrant colors, sick = desaturated, critical = grayscale + red pulse border). Data: creature_state enum, habits[{id, name, completed_today}], streak_count, last_checkin_timestamp.
- **Screen 3: Habit Check-in** — Triggered by tapping a habit checkbox. Full-screen confirmation moment: creature animates (happy bounce), checkmark fills, XP bar increments. If all 3 habits done today: "Daily complete!" banner + auto-generates shareable card. Data: habit_id, habit_name, all_habits_done_today bool, creature_reaction_type.
- **Screen 4: Shareable Creature Card** — Auto-generated static card (share sheet or preview screen). Shows: creature pixel art at current stage, "[Name] — Day [streak] streak", current evolution stage name, app name/logo watermark. Share button opens native share sheet. Download button saves to camera roll. Data: creature_sprite, creature_name, streak_count, stage_name.
- **Screen 5: Creature Status Detail** — Tap creature on home screen to open. Shows: health bar (0–100), streak count, current evolution stage + next stage threshold ("Evolve at Day 30 — you're on Day 22"), last 7 days habit completion grid (GitHub-style). Data: health_value, streak, evolution_stage, next_threshold, habit_history[7 days].
- **Screen 6: Evolution Celebration** — Full-screen moment triggered at evolution threshold. Creature evolves with particle animation. Stage name revealed. Shareable card auto-generated. CTA: "Share your evolution" + "Keep going". Data: new_stage_name, new_sprite, streak_at_evolution.
- **Screen 7: Critical State / Recovery Screen** — Shown when creature health reaches 0 (missed 5+ days). Creature in grayscale, pulse animation. "Your creature needs you" copy. Recovery path: complete all 3 habits for 3 consecutive days to restore. Progress tracker shown (Day 1/3, Day 2/3, Day 3/3). Alternative: "Start fresh" resets creature to baby but preserves streak history. Data: days_missed, recovery_progress (0–3), creature_name.
- **Screen 8: Habit Management** — Add/edit/delete up to 3 habits. Preset habit picker (6–8 options) + custom name input. Per-habit reminder time picker. Data: habits[{id, name, reminder_time}].
- **Screen 9: Shop / Skins** — Cosmetic creature skin store. Grid of available skins: free (default), locked (IAP price tag). Tap to preview skin on your creature. "Get this skin" → triggers IAP flow. Data: skins[{id, name, price, owned, preview_sprite}].
- **Screen 10: Settings** — Notification on/off toggles per habit, creature name edit, restore purchases, about/feedback link.

**Key interactions:**
- Tap creature on Home → opens Creature Status Detail (slide-up modal)
- Tap habit checkbox → triggers Habit Check-in confirmation screen → returns to Home with creature updated
- All 3 habits done → Shareable Card auto-previews (dismissible), streak increments
- Streak hits evolution threshold (Day 7, 14, 30, 60) → Evolution Celebration full-screen
- Creature health reaches 0 → Critical State screen replaces Home as primary view until recovery
- Tap skin in Shop → preview overlaid on creature sprite in real time before purchase
- Push notification tap → deep-links to Home Dashboard

## Version History
- v1: Original idea
- v1.1: Critique — permadeath UX problem, Habitica differentiation gap, art asset volume risk, no monetization
- v2: Improved — recovery arc replaces permadeath, one creature/path/AI art, daily shareable card as viral differentiator, fitness niche focus, cosmetic IAP monetization
