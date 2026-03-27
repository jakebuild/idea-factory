# Critique v1 — Idea #19: Sleep Journal App

**Verdict:** WORTH BUILDING
**Score:** 7/10

## What's Actually Good
- Intentionally low friction: bedtime + wake time + one word is the minimum viable logging experience — no wearable, no sensors, no subscription hardware required
- "One word" feeling log is a clever constraint that removes the blank-page problem of journaling; it's memorable and differentiated
- Sleep tracking without a wearable is a real market — not everyone has an Apple Watch or Oura Ring, and many people distrust passive tracking
- Weekly patterns + streaks is proven sticky retention mechanics (Duolingo effect)
- Scope is ruthlessly simple — this is genuinely a 2–4 week build
- No real-time data processing, no ML, no complex infra — just a form, a calendar, and a chart

## Brutal Feedback
- This is a solved problem. Sleep Cycle, Pillow, Bearable, Finch, and a dozen others do manual sleep logging. The "one word" angle is cute but not a moat. What keeps someone here after week 2?
- The word-of-the-day input sounds fun until users realize they want to say "groggy" and "tired" and "okay" every single day — novelty wears thin fast
- Weekly patterns are only interesting if there's enough variance to learn from. Most people have the same sleep pattern every day. After 2 weeks of "yep I sleep 7 hours, yep I feel okay," the app becomes useless
- Streaks are a double-edged sword. Miss one day → streak broken → user abandons the app. It's a retention mechanic that can actively drive churn
- No clear monetization path. Paid app? Subscription? Free with ads? This is a features-not-a-product problem unless there's a hook
- "No wearable needed" is a positioning statement, not a feature. People who don't have a wearable aren't necessarily looking for a sleep app — they just don't care that much about sleep tracking
- Zero network effects. This is a solo-use app with nothing that makes it more valuable with more users. Hard to grow organically
- Retention cliff: journaling apps have notoriously terrible 30-day retention. Without push notifications at the right time, users forget within a week

## Key Questions
- Who is the specific person who would use this daily for 6 months? Describe them
- What insight does the app surface that surprises the user? "You sleep less on Sundays" is obvious — what's the "whoa" moment?
- Is the one-word log actually enough emotional granularity to be useful, or is it a gimmick?
- What happens when a user misses 3 days? Can they backfill? If yes, streaks are meaningless. If no, the app punishes real life
- How does this differ from just using the Notes app or a physical journal?
- What's the revenue model — and at what price point would someone pay for this?

## Suggestions
- Add the "why" layer: let users optionally tag one reason (alcohol, stress, travel, late screen) — now the app can actually tell you what's hurting your sleep
- Replace pure streaks with a forgiving "weekly consistency score" — 5 out of 7 days is still a win, don't punish users for having a life
- The one-word input could become a mood vocabulary builder over time — "this week your most common word was 'groggy' — 3 nights followed late bedtimes"
- Weekly digest notification: push a Sunday summary card to recapture dropped users
- Consider a "sleep debt" running tally — simple math (your target vs. actual) makes the data immediately actionable
- Lock in a clear niche: teen sleep tracking for parents, shift workers, new parents with newborns — one specific audience beats generic

## Solo Dev Reality Check
- **Can one person ship this in 2-4 weeks with AI coding tools?** YES — the core is a form + local storage/simple DB + a chart library. Nothing technically hard here. React Native or a PWA. Week 1: logging + storage. Week 2: patterns view + streaks. Week 3: polish + notifications. Shippable.
- **Biggest solo complexity traps:**
  - Push notifications across iOS/Android are always trickier than expected — scheduling bedtime reminders requires platform permissions and edge case handling
  - Timezone handling for logging bedtime past midnight (e.g., logging "I went to bed at 1am" — is that Friday night or Saturday?) is a silent data corruption bug waiting to happen
  - If building mobile (React Native/Expo), App Store review adds time. A PWA sidesteps this but has worse notification support
  - Chart library selection matters — don't roll your own, use Victory Native or Recharts and accept its constraints
  - Backfill logic for missed days needs a decision upfront: allow it (kills streak integrity) or forbid it (frustrates users)

## Design Handoff

### Screens needed
1. **Onboarding / Setup** — ask for wake goal time, sleep goal time, name (optional), notification preference
2. **Home / Log Today** — primary CTA: log last night's sleep. Shows: bedtime input, wake time input, one-word mood input. Also shows current streak prominently
3. **Log Entry Form** — focused input screen: time pickers for bed/wake, word input with autocomplete of past words, optional note (stretch)
4. **Weekly View / Patterns** — bar or line chart of sleep duration by day, mood word cloud or list for the week, streak counter, average sleep duration
5. **History / Calendar View** — monthly calendar with color-coded nights (green = hit goal, yellow = close, red = missed), tap a day to see entry detail
6. **Entry Detail** — single night breakdown: hours slept, mood word, delta vs. goal, any tags
7. **Streak / Stats Screen** — all-time stats: longest streak, average sleep, most common mood word, best sleep night
8. **Settings** — sleep goal, notification time, data export, clear data

### Key UI interactions
- Time pickers should be scroll-wheel style (native feel), defaulting to last logged time
- One-word input: free text with chips showing previously used words for quick re-tap
- Streak counter should animate on log completion (dopamine hit)
- Weekly chart bars should be tappable to show that day's entry
- Swipe left on calendar to go back in time
- "Log last night" should be dismissible with a "I'll log later" snooze option
- Empty state on first open should feel welcoming, not clinical

### Data each screen needs
- **Home:** today's log status (logged/not), current streak count, last night's entry preview
- **Log Form:** previous entry (to pre-fill times as defaults), list of previously used mood words
- **Weekly View:** 7 entries (bed time, wake time, mood word) for current week, sleep goal setting
- **Calendar:** all entries indexed by date, sleep goal for color coding
- **Entry Detail:** single entry object: { date, bedtime, waketime, duration, moodWord, note? }
- **Stats Screen:** aggregated stats computed from all entries (streak logic, averages, word frequency)
- **Settings:** user preferences object (goal hours, notification time, notification enabled)
