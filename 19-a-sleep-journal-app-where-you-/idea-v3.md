# Idea #19: Sleep Journal (Improved)

## Version: v3
**Date:** 2026-03-27 17:20
**Status:** improved

## Original Idea
A sleep journal app where you log bedtime, wake time, and one word for how you feel — it shows weekly sleep patterns and streaks, no wearable needed

## What Changed and Why
- **Committed to one concrete acquisition channel before building** — The critique was right: "validate with a waitlist" is a punt. The acquisition plan is now baked into the product: build in public, launch with a single honest Reddit post to r/ShiftWork and r/NewParents ("I built this because every sleep app assumes you sleep at night"). The product itself has a shareable weekly card (your sleep week as a simple graphic) to generate organic reach. Distribution is no longer an afterthought — it's a feature on the roadmap.
- **Replaced the sleep debt number with a 7-day rolling window and added a compassionate mode** — An ever-growing deficit is punishing for the exact users we want (new parents, shift workers who are chronically under-slept by definition). The debt now shows only the rolling 7-day window: "–3.5 hrs this week." A "Realistic Mode" setting lets new parents set a custom sleep goal (e.g. 5.5 hrs) instead of the default 8, so the app meets them where they are rather than shaming them for circumstances they can't change.
- **Locked the date model before touching the data model** — A sleep session is stamped with the date the user *started* sleeping. The log prompt says "When did you start sleeping?" not "What night was this?" This survives midnight crossings, 8am–4pm shift worker schedules, and week boundary math. Decision is made, no open question remaining.
- **Deferred the insight engine to v1.1, replaced with a simpler "streak of sameness" hook** — The insight engine with proper edge-case copy is a week-2 scope trap. Instead, v1 shows one dumb-but-honest metric: "You've logged X nights in a row. Your average this week: 6h 20m." At 14 entries, a single insight unlocks: your most common tag on your 3 worst nights. That's it — no branching copy, no false confidence, no edge-case explosion. The unlock itself becomes the retention mechanic ("log 6 more nights to see your first pattern").
- **Switched from PWA to Expo (React Native) to unlock notifications** — PWA iOS notification support is broken. For shift workers with no fixed routine, the app will be forgotten within a week without a trigger. Expo adds ~2 days of setup overhead but unlocks the one re-engagement mechanic that actually works for irregular schedules: a gentle daily nudge at a user-set time. App Store review is the tradeoff; it's worth it.
- **Changed monetization to free with one-time unlock** — New paid apps get zero organic App Store downloads. Free with a $1.99 one-time "Unlock Insights" IAP changes the funnel: users download for free, log for 2 weeks, hit the insight threshold, and see a "your first pattern is ready — unlock to view" gate. Purchase intent is highest at the moment of payoff, not at download.

## Improved Description
A manual sleep journal for people with irregular schedules — shift workers, new parents, and anyone whose sleep doesn't fit a 9-to-5 mold. Log when you started sleeping, when you woke up, and one optional reason tag (stress, alcohol, late screen, caffeine, travel, exercise, kid). The app shows your rolling 7-day sleep picture in plain numbers: hours logged, consistency score (X/7 nights), and debt for the week (–3.5 hrs this week, not an ever-growing lifetime total).

No wearable. No subscription. Free to log forever.

At 14 entries, one pattern unlocks: your most common reason tag on your three worst nights. That's the "whoa" moment — "Late screen showed up on all 3 of your worst nights" — and the moment to offer the $1.99 insight unlock for ongoing weekly patterns.

The app is built for the rhythm of real irregular sleep: the entry asks "When did you start sleeping?" (not "what night?"), a Realistic Mode lets you set a custom sleep goal if 8 hours is a fantasy, and backfill allows up to 7 days back (marked, excluded from consistency score). A daily notification at a user-chosen time is the only re-engagement mechanic — no streaks, no gamification, just a quiet nudge.

Distribution: launch on Reddit (r/ShiftWork, r/NewParents, r/sleep) with an honest post and a shareable weekly sleep card screenshot. Build in public from day one.

## Why This Is Worth Building (Solo + AI)
The core is a form, local storage, one chart, and a single aggregation query — genuinely 2 weeks of AI-assisted coding. The monetization is proven (free + one-time unlock at the payoff moment), the acquisition channel is specific and free (Reddit launch post + shareable card), and every scope trap from v2 is either cut (insight engine complexity), decided (date model), or deferred correctly (nap tracking). A solo dev ships the free tier in 2 weeks, validates the unlock conversion in week 3.

## Solo MVP Scope
- **What's in v1:** Log entry (start-sleep time, wake time, one reason tag optional), 7-day rolling sleep debt (weekly window only), weekly bar chart, consistency score (X/7), Realistic Mode (custom sleep goal), backfill up to 7 days with backfill marker, daily notification at user-set time, insight lock state ("X more nights to unlock"), one insight at 14 entries (most common tag on 3 worst nights), $1.99 one-time IAP to unlock ongoing weekly insights, shareable weekly card (screenshot-friendly summary)
- **What's out of v1:** Cloud sync (local storage only), multiple insights per week, nap tracking, social features, data export (CSV), onboarding carousel, mood vocabulary, historical insight archive, Android (iOS first via Expo)
- **Estimated build time with AI coding tools:** 2.5 weeks. Week 1: data model (date = sleep-start date), log form, local storage, debt math, weekly chart. Week 2: insight lock + single insight logic, notification setup (Expo), Realistic Mode, App Store build. Half of week 3: IAP integration, shareable card, launch post.

## Open Questions
- Does the shareable weekly card need to be designed before launch, or is a plain-text share (copy to clipboard) enough for the Reddit launch?
- What's the App Store category? Health & Fitness competes with Oura and Sleep Cycle. Productivity might get better placement for a manual journaling app.
- Should Realistic Mode be a prominent onboarding choice ("What's your realistic sleep goal?") or buried in settings? First-time UX decision with retention implications.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Log Entry** — Primary home screen / default open screen. "When did you start sleeping?" label with a scroll-wheel time picker (defaults to last logged start time). "When did you wake up?" with a second scroll-wheel time picker (defaults to last logged wake time). Live duration calculation displayed between the two pickers (e.g. "7h 20m"). Optional reason tag row: single-select chips for Stress / Alcohol / Late screen / Caffeine / Travel / Exercise / Kid / Other. Save button. "Log for a different day" text link below Save (triggers date picker, max 7 days back). New user warm-up state: progress bar "Log X more nights to unlock your first pattern." Already-logged-today state: shows today's entry summary with an Edit button instead of the form.
- **Screen 2: Weekly Dashboard** — Bar chart (7 days, current week shown by default). Bars color-coded: green (at/above goal), yellow (30 min under), red (1 hr+ under). Backfilled entries shown with striped/muted bar. Below chart: consistency score chip ("5/7 nights"), rolling debt number ("–3.5 hrs this week" in red, or "+1.2 hrs" in green). Insight card: locked state ("6 more nights to unlock your first pattern" with a progress bar) or unlocked free state (first insight shown) or paywalled state ("Your pattern is ready — unlock for $1.99"). Week navigation (prev/next arrows). Tap any bar → Entry Detail.
- **Screen 3: Entry Detail** — Single entry view. Shows: date label (e.g. "Sat, Mar 22"), start sleep time, wake time, total duration, delta vs. goal ("+45 min" or "–1h 10m"), reason tag badge (if set), backfill badge (if applicable). Edit button (returns to Log Entry form pre-filled). Delete option with confirmation dialog.
- **Screen 4: History / Calendar** — Monthly calendar grid. Each logged day: filled color dot (green/yellow/red). Days with reason tags: small tag dot below the color dot. Unlogged days: empty circle. Today: outlined. Tap day → Entry Detail. Swipe left/right or arrow buttons for month navigation.
- **Screen 5: Stats** — All-time aggregate view. Cards: Total nights logged, Average sleep duration, Goal hit rate (%), Most common reason tag, Best week (highest consistency), Longest logging streak (secondary). Sparkline: average sleep per week over last 8 weeks. Insight unlock progress bar (persistent until unlocked).
- **Screen 6: Settings** — Sleep goal slider (hours, default 8). Realistic Mode toggle with sub-label "Set a custom goal if 8 hrs isn't realistic right now." Custom goal input (shown when Realistic Mode on). Notification toggle + time picker (default off). Unlock Insights IAP button (shows if not purchased; shows "Unlocked" badge if purchased). Restore purchases link. Data export placeholder (disabled, labeled "coming soon"). Clear all data (destructive, confirm dialog). App version.
- **Screen 7: Shareable Weekly Card** — Full-screen card designed for screenshot sharing. Shows: week label ("Week of Mar 24"), consistency score, average sleep, one-line debt summary, app name/logo. "Share" button (native share sheet). Accessed from Weekly Dashboard via a share icon.
- **Screen 8: Insight Unlock Paywall** — Shown when user taps the locked insight card after 14 entries. Above the fold: blurred/masked first insight preview ("Your worst nights had something in common..."). Below: "$1.99 one-time unlock — see your patterns, forever." One CTA button. Restore purchases link. No subscription language anywhere.

Key interactions:
- **Main logging flow:** Open app → Log Entry is default → scroll time pickers → optionally tap one reason tag → tap Save → brief success state showing updated debt number → auto-navigate to Weekly Dashboard
- **Backfill flow:** Tap "Log for a different day" → date picker (last 7 days only) → same log form → entry saved with backfill marker, excluded from consistency score, striped bar on chart
- **Insight unlock flow:** Reach 14 entries → insight card on Weekly Dashboard shows "Your pattern is ready" → tap → Paywall screen → purchase → insight revealed immediately, weekly insights enabled going forward
- **Realistic Mode flow:** Settings → toggle Realistic Mode → set custom goal (e.g. 5.5 hrs) → all debt calculations and bar chart colors recalibrate to new goal immediately
- **Share flow:** Weekly Dashboard → tap share icon → Shareable Weekly Card screen → tap Share → native share sheet
- **Time picker UX:** Scroll-wheel pickers. Start sleep defaults to last logged start time. Wake defaults to last logged wake time. Duration updates live. If times cross midnight, duration still calculates correctly (no negative duration bug).
- **Empty/warm-up state:** Before 14 entries, insight card shows progress bar. Before any entries, Log Entry shows minimal copy: "Log your sleep. See what hurts it." No carousel, no sign-up wall.

## Version History
- v1: Original idea
- v2.1: Critique — niche pivot good, but acquisition unaddressed, insight engine scope underestimated, sleep debt demoralizing for target users, date model unresolved, PWA/notification conflict, paid app discoverability problem
- v3: Improved — concrete Reddit acquisition plan + shareable card, insight engine deferred to simple one-unlock mechanic, rolling 7-day debt cap + Realistic Mode, date model locked (sleep-start date), switched to Expo for notifications, free + $1.99 IAP monetization
