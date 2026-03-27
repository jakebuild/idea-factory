# Idea #20: Habit Creature (Improved)

## Version: v3
**Date:** 2026-03-27 20:54
**Status:** improved

## Original Idea
Habit Creature — An app where you care for a pixel creature by completing your habits. Miss a habit = your creature gets sick. Streak = creature evolves into new forms. The creature is your accountability partner - it literally dies if you don't do your habits. Shareable evolution timeline.

## What Changed and Why

- **Answered the Finch question head-on** — Finch is a self-care journaling app. You complete "goals" like "be kind to myself today." This is a fitness accountability app with a social sharing mechanic. The pitch in one sentence: "Finch is therapy. This is your gym streak made visible." The hook isn't a creature — it's a publicly shareable proof of consistency. Finch users don't share their birds. That's the wedge.
- **Replaced AI pixel art strategy with human artist for base design** — The critique correctly identified that 5 consistent states from diffusion models (same character, same proportions, same palette, different energy) is genuinely hard. The fix: commission a Fiverr pixel artist for the base creature's 5 states ($100–$200, one week turnaround), then use AI (Midjourney img2img with the base as reference) for seasonal skin variants only. This eliminates the biggest pre-launch delivery risk and de-risks the art bottleneck entirely.
- **Fixed the monetization model** — Cosmetic skins at $0.99–$2.99 in a 90-second daily session will not convert. Added a premium tier: $2.99/month or $9.99/year unlocks ad-free, a second creature slot, and a monthly skin pack. This gives users who love it a way to pay more than $2.99 total lifetime. Skins remain as a lightweight IAP gateway, but the real revenue is subscription.
- **Resolved recovery arc edge case** — Mid-recovery miss now pauses the counter (not resets). "You completed 2 of 3 recovery days — pick up where you left off anytime." No expiry. This removes the punishment spiral while still requiring real effort, and it's a simple rule that can be written into the state machine in one pass.
- **Added a push-free engagement fallback** — iOS 16+ WidgetKit home screen widget shows the creature's current state without requiring push permission. Users who deny push still see their creature degrading on their home screen every time they pick up their phone. This is the fallback engagement loop the v2 critique flagged as missing. Widget also shows today's habit check count (1/3, 2/3, 3/3).
- **App Store category: Lifestyle, not Health & Fitness** — Sidesteps the Apple IAP scrutiny that cosmetic skins in Health apps can trigger. "Lifestyle" positions alongside companion apps, not clinical tools, and aligns with how Finch is listed.

## Improved Description

Habit Creature is a fitness accountability app where you raise a pixel creature by completing your daily habits. Check in your workout → creature glows and levels up. Skip a week → creature enters critical state and needs a recovery routine to come back. Every milestone (Day 7, 14, 30, 60) auto-generates a shareable creature card — your creature's evolved form, your streak number, your stage name — one tap to Instagram Stories.

The core differentiator from Finch: Finch is a self-care journaling app. No one shares their Finch bird. This app is built around public proof of consistency. The creature card is the product, the habits are the mechanic.

The creature has 5 pixel art states drawn by a human pixel artist (not AI — removes the biggest delivery risk). Seasonal skin variants use AI generation with the human-drawn base as a reference image, cutting art cost to near-zero post-launch. A home screen widget (iOS 16+) shows creature state without requiring push permission — the creature degrading on your home screen every time you open your phone is the fallback engagement loop.

Monetization: Premium tier at $2.99/month or $9.99/year (second creature slot, monthly skin pack, ad-free). Cosmetic skins as $0.99–$1.99 one-time IAP for users not ready to subscribe.

Recovery arc rule: creature never fully dies. Critical state triggers after 5 missed days. Recovery requires 3 consecutive completion days, counter pauses (not resets) on mid-recovery misses.

## Why This Is Worth Building (Solo + AI)

The state machine, check-in flow, notification logic, and widget are all AI-codeable in a week. Commissioning a pixel artist for the base 5 states ($100–200 on Fiverr) eliminates the art delivery risk entirely and frees the solo dev to focus on the product. The shareable card is on-device canvas render (react-native-view-shot for RN, RepaintBoundary for Flutter) — no backend, no cold start, no infra cost.

## Solo MVP Scope

- **What's in v1:** One creature, 5 human-drawn pixel art states, 3 fitness habit slots, daily check-in flow, creature health state machine, home screen widget, auto-generated shareable card at milestone streaks (Day 7/14/30/60) + daily card option, push notifications + widget fallback, recovery arc with pause-not-reset counter, premium subscription IAP ($2.99/mo or $9.99/yr), 1–2 cosmetic skins as one-time IAP
- **What's out of v1:** Multiple creature types, animated GIF sharing, social feed/friends, creature battles, custom habit categories beyond fitness presets, per-habit streaks (one global streak), Apple Watch, evolution timeline history view, Android (iOS first), web version
- **Estimated build time with AI coding tools:** 3 weeks (Week 1: state machine + check-in + notifications + widget; Week 2: pixel art delivery + creature display + shareable card; Week 3: IAP + premium tier + polish + TestFlight)

## Open Questions

- React Native or Flutter? Flutter has better pixel rendering control for crisp sprites at non-standard scales; RN has better react-native-view-shot maturity for card generation
- What is the exact pixel artist brief? The 5 states need a name, a pose, and an energy level defined before the commission — this is a product decision, not just an art brief
- How do milestone share cards differ visually from the daily card? If they look the same, the milestone won't feel special enough to share — needs design consideration
- Subscription pricing: is $2.99/month strong enough value against a second creature slot? Test with TestFlight users before hard-coding

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding — Creature Hatch** — First-time flow. Egg hatches into baby creature (pixel art sprite, no GIF complexity — looped idle frames only). User names creature. Selects up to 3 fitness habits from preset list (Workout, Daily Steps, Drink Water, Morning Stretch, Evening Walk, Meditate). CTA: "Start caring for [Name]". Last step: widget setup prompt + notification permission request (in that order — widget first lowers the stakes of denying push). Data: empty habits array, creature name input.
- **Screen 2: Home / Dashboard** — Primary daily screen. Full-screen creature pixel art centered with idle loop. Below: 3 habit checkboxes with habit names and today's completion state. Top bar: streak counter (flame icon + number), creature stage label, tap to open Status Detail. Creature visual state changes based on health: healthy = vibrant full color, sick = slightly desaturated, critical = grayscale + red pulse border. Data: creature_state enum (healthy/sick/critical), habits[{id, name, completed_today}], streak_count, health_value, stage_name.
- **Screen 3: Habit Check-in Confirmation** — Triggered on checkbox tap. Creature bounces happy animation, checkmark fills. If all 3 done today: "Daily complete!" banner. If today is a milestone day (Day 7/14/30/60): milestone card auto-previews instead of daily card. Data: habit_id, all_habits_done_today bool, is_milestone_day bool, creature_reaction.
- **Screen 4: Shareable Creature Card — Daily** — Auto-generated on-device canvas card. Shows: creature sprite at current stage (centered), "[Name] — Day [streak]", evolution stage name, subtle app watermark. Native share sheet button + save to camera roll. Dismissible. Data: creature_sprite, creature_name, streak_count, stage_name.
- **Screen 5: Shareable Creature Card — Milestone** — Visually distinct from daily card. Larger creature render, gold border treatment, evolution stage reveal text, "Day [X] achieved" headline. Same share/save buttons. Designed to feel like a newsworthy event, not daily noise. Data: same as daily + milestone_label (e.g. "1 Month Strong").
- **Screen 6: Creature Status Detail** — Slide-up modal from tapping creature on Home. Health bar (0–100), streak count, current stage + next threshold ("Evolve at Day 30 — you're on Day 22"), last 7-day habit completion grid (GitHub-style heatmap). Data: health_value, streak_count, evolution_stage, next_stage_threshold, habit_history[7 days].
- **Screen 7: Evolution Celebration** — Full-screen interrupt at evolution threshold (Day 7, 14, 30, 60). Particle animation, old → new sprite transition, stage name reveal. Milestone shareable card auto-generated. CTA: "Share your evolution" + "Keep going". Data: new_stage_name, new_sprite, streak_at_evolution, milestone_card_preview.
- **Screen 8: Critical State / Recovery Screen** — Replaces Home as primary view when health = 0 (5+ missed days). Creature in grayscale with red pulse border. "Your creature needs you" copy. Recovery progress tracker: "Day X of 3 complete — pick up where you left off." Counter never resets on mid-recovery miss — it just pauses. Secondary CTA: "Start fresh" (resets creature to egg, preserves full history log). Data: days_missed, recovery_progress (0–3, pausable), creature_name, reset_confirmation bool.
- **Screen 9: Habit Management** — Add/edit/delete up to 3 habits. Preset picker (6–8 options) + custom name input. Per-habit reminder time picker. Data: habits[{id, name, reminder_time}], preset_list.
- **Screen 10: Shop / Skins** — Grid of cosmetic skins: default (free), seasonal variants (IAP $0.99–$1.99), premium skin packs (subscription-only badge). Tap any skin → live preview overlaid on your creature sprite. "Get this skin" → IAP or subscription upsell flow. Data: skins[{id, name, price, owned, is_premium, preview_sprite}], is_subscriber bool, current_creature_state.
- **Screen 11: Premium Upsell / Paywall** — Triggered from Shop or Settings. Two options: $2.99/month or $9.99/year. Feature bullets: second creature slot, monthly skin pack, ad-free. "Start 7-day free trial" CTA. Dismissible. Data: subscription_options[{price, interval, trial_days}], current_plan.
- **Screen 12: Home Screen Widget (iOS 16+ WidgetKit)** — Small widget. Shows: creature sprite at current state, streak number, habit check count today (2/3). Taps deep-link to Home. No push required. Data: creature_state, streak_count, habits_done_today, habits_total.
- **Screen 13: Settings** — Notification toggles per habit, widget setup guide link, creature name edit, manage subscription, restore purchases, feedback link. Data: notification_prefs, creature_name, subscription_status.

**Key interactions:**
- Tap creature on Home → Creature Status Detail slides up as modal
- Tap habit checkbox → Habit Check-in confirmation full-screen → returns to Home with updated creature
- All 3 habits done → Shareable Card auto-previews (dismissible), streak increments
- Milestone day (7/14/30/60) → Milestone card instead of daily card + Evolution Celebration if threshold crossed
- Health reaches 0 → Critical State screen replaces Home until 3-day recovery complete (counter pauses, not resets, on mid-recovery miss)
- Tap skin in Shop → skin preview overlays live on creature sprite before purchase
- Push notification tap → deep-links to Home
- Widget tap → deep-links to Home
- Premium feature access without subscription → triggers Premium Upsell paywall

## Version History
- v1: Original idea
- v2.1: Critique — Finch comparison unanswered, AI art consistency risk, skin-only IAP won't convert, push fallback missing, recovery arc edge case unresolved
- v3: Improved — Finch wedge articulated, human pixel artist for base art, premium subscription tier added, recovery counter pauses not resets, WidgetKit as push-free fallback, Lifestyle category
