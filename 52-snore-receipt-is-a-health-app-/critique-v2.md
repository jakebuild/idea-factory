Verdict: NEEDS MAJOR WORK
Score: 4/10
What's Actually Good:
- You cut the most bullshit part. Removing posture detection was the right move because bedside-phone posture attribution was fantasy dressed up as insight.
- The target user is finally specific: people whose partner told them they snore. That is much better than vague "sleep health" positioning.
- Manual context logging is more honest than pretending sensors know things they absolutely do not know.
- The morning "receipt" with a loudest clip is a clear payoff. Users understand that instantly.
Brutal Feedback:
- The whole product still stands on one giant UNVERIFIED assumption: reliable overnight recording on mobile, especially iOS. If that breaks, the app is dead. Not degraded. Dead.
- "Recording app + log form + charting layer" is a laughably soft estimate. Overnight recording, background behavior, interruption handling, battery drain, storage management, crash recovery, permission edge cases, and audio post-processing are the product.
- Your differentiator is weaker than you think. "Your worst nights had alcohol" is not magical insight. It is the kind of thing users can guess before installing, and competitors can copy in a weekend.
- The correlation layer risks being fake-science theater. Seven nights with five binary inputs is tiny, noisy, confounded data. You are one bad week away from surfacing nonsense with a smug chart.
- The app says "not medical" while still asking users to trust a proprietary snore score. That is the oldest health-adjacent dodge in the book. If the score is flaky, the disclaimer will not save trust.
- Partner noise filtering is cut, but the problem is not cut. If the room has another snorer, a pet, a fan, rain sounds, or a white noise machine, your "receipt" becomes a random-number generator with a waveform.
- Manual pre-sleep logging is retention poison. People are tired at bedtime. They do not want to fill out a tiny ritual so your analytics screen can maybe feel useful in a week.
- Your week-2 retention story is fragile. Why does the user come back after day 1? Because maybe, after seven compliant nights, the app might tell them alcohol is bad for snoring? That is not a hook. That is delayed obviousness.
- The 3-night experiment feature sounds neat in a doc and weak in reality. Three nights is not enough to overcome normal sleep variance, so you are building a confidence theater machine.
- There is still no moat. SnoreLab already owns the mental category. Voice memos are free. Apple and Android already capture sleep-adjacent data. You are trapped between "too flimsy to trust" and "too small to matter."
- There is no monetization in scope, which is fine for MVP, but it exposes another problem: if the thing works, users may only need it briefly. That means weak long-term retention and awkward business economics.
- App Store review risk is not gone just because you removed medical claims. Background recording and health-adjacent messaging still invite scrutiny, and a solo dev does not want that fight as the core platform risk.
- The build estimate is fantasy. A solo dev doing AI-assisted mobile work can mock the UI in weeks. Shipping reliable overnight audio capture across real devices is the part that eats the calendar alive.
Key Questions:
- Can you prove, on a real device, that overnight recording works reliably for 6-8 hours without the app getting suspended or wrecking battery life?
- What is the minimum honest scoring method that does not collapse in normal bedroom noise?
- What happens when the user forgets the log for two nights in a row? Does the core value proposition just stall out?
- Why does the user come back after day 1 if the first morning already answers the curiosity hook?
- Why would someone use this instead of SnoreLab, a sleep recording app, or a free voice recorder plus common sense?
- Are you building iOS-first, Android-first, or pretending both are equally feasible? They are not.
Suggestions:
- Do not build "the app" first. Build a brutal technical prototype that records one full night on target devices and outputs a rough event timeline. If that fails, kill the idea immediately.
- Narrow the MVP further: Android-first, single-device support path, no experiment mode, no fake correlations, just nightly recording, basic score, clip, and history.
- Replace "correlation intelligence" with brutally honest summaries until you have enough data. Example: "2 of your 3 highest-score nights had alcohol logged." That is weaker marketing, but less fraudulent.
- Consider making the app a short-term diagnostic habit tool, not a daily habit tracker. Position it as a 7-night self-audit instead of pretending it will become a durable routine.
- If you want a sharper wedge, solve one painful problem better than anyone else: easiest overnight capture, clearest morning clip, or fastest partner-proof evidence. Right now you are mediocre at all three on paper.
- Put a kill metric in week 1: if overnight recording reliability is not solid, stop. Do not rationalize your way into months of tuning bedroom audio garbage.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — only if you slash scope to Android-first and treat the first week as a feasibility test. Cross-platform MVP in that timeline is fantasy.
- Biggest solo complexity traps: background audio restrictions on iOS, battery/storage drain from overnight recording, false positives from ambient noise, unreliable score extraction, bedtime logging compliance, and spending weeks polishing charts before proving the recording pipeline works.
