Verdict: WORTH BUILDING
Score: 6/10

What's Actually Good:
- Shake-to-interact is genuinely delightful for young kids (ages 3–7) — it's physical, immediate, and requires zero reading ability
- Zero learning curve for the target user; if they can wave a phone they can play
- Motion detection (DeviceMotion API or accelerometer) is well-supported and trivially implementable — real weekend MVP territory
- Star + sparkle animations are easily achievable with CSS/Canvas and look great with minimal code
- Works perfectly as a web app or React Native app — no complex backend, no auth, no infra bill
- The idea is narrow enough that scope creep is easy to resist if you're disciplined

Brutal Feedback:
- The idea as described is a screensaver, not a game. "Shake → star appears" is a tech demo. A 5-year-old will do it twice and put the phone down.
- There is no goal, no win state, no progression, no reason to keep shaking. Without a retention loop (fill the jar, unlock a character, reach a milestone), this dies in 90 seconds.
- "Collect stars" — collect toward what? A number going up is meaningless to a toddler. You need a visual container that fills up (jar, bucket, galaxy) with a satisfying "FULL!" moment or this is vapor.
- Parents are the real gatekeepers and they will read one App Store description before deciding. "Shake your phone" as the entire pitch earns a 1-star review that says "my kid did it twice and uninstalled it."
- Motion sensitivity is a UX minefield for kids. Too sensitive = stars fly everywhere on a bumpy car ride. Not sensitive enough = kid shakes harder and throws the phone across the room. Getting this calibration right requires physical testing with real children, which most solo devs skip.
- No monetization path described. Ads are inappropriate for kids' apps under COPPA. IAP on a toddler distraction app is dark-pattern territory. You're building a free hobby app — be honest about that going in.
- Zero differentiation. "Shake for stars" has been done in every accelerometer tutorial since 2009. What makes yours the one worth downloading?
- Sound design is 50% of the experience for a kids' app. Silent stars = dead app. But autoplay audio is blocked by browsers and requires a user gesture to unlock — annoying to wire up correctly and easy to forget until you test on a real device.

Key Questions:
- What happens when the kid collects enough stars? Is there a reward moment, or does the counter tick up forever toward nothing?
- Who holds the phone — the 3-year-old or the supervising parent? Matters enormously for UI scale, grip safety, and shake threshold.
- Is this a 30-second airplane-mode distraction or a recurring daily play session? Very different design targets.
- Are stars permanent across sessions, or do they reset? If they reset, why? If they don't, what's the long-term loop once you have 10,000 stars?
- Native app or PWA? The platform decision changes shake detection quality, App Store review burden, and time-to-ship by weeks.

Suggestions:
- Add a visual goal container immediately — a jar, a rocket, a treasure chest that fills with stars as the kid shakes. Make the fill state the central visual, not a counter.
- Build a "prize moment" at 100% fill: explosion of stars, confetti, a happy animal appears, celebratory sound. Then reset for the next round. This is the entire product.
- Make shaking intensity matter — harder shakes = bigger stars or bonus stars. Gives kids a reason to go wild.
- Sound is non-negotiable. Twinkle per star, escalating music as the jar fills, triumphant fanfare at completion. This is the emotional experience.
- Ship as a PWA first. Skip the App Store entirely, test with real kids fast, iterate. Native-ify later if it has legs.
- Add a parent "set goal" screen — parent picks the jar size (10 stars for a quick win, 50 for longer engagement). Costs one screen, doubles the use case.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? YES — the core mechanic is 1–2 days of actual coding. Shake detection + CSS/canvas animation + fill-progress visual + one sound set. A disciplined solo builder ships this in a long weekend. The 2–4 week budget is more than enough if you don't scope-creep into themes, characters, and parent dashboards.
- Biggest solo complexity traps:
  - iOS DeviceMotion requires explicit permission request (iOS 13+) — easy to forget until you test on a real device and everything is silently broken
  - Audio on mobile: browsers block autoplay; you must trigger sound on the first user gesture (the shake counts, but wiring this correctly has gotchas)
  - Scope creep: "just add one character skin" → "just add three" → "just add a settings screen" → you've tripled the project. Lock the MVP feature list before writing a line of code.
  - Performance on low-end Android: canvas particle effects drop frames badly on cheap tablets kids actually use. Test on a $100 Android early, not at the end.
  - If going React Native: one platform first. Cross-platform doubles QA time for a solo builder.

Design Handoff:
- Screens needed:
  - Launch/Home screen — full-screen, single tap to start (required to unblock audio on mobile), oversized friendly "SHAKE!" prompt with animated star
  - Play screen — main game view: animated star field background, visual goal container (jar/chest/rocket) with real-time fill indicator, shake-triggered star burst animation, large star count display
  - Prize/Celebration screen — full-screen explosion of stars + confetti, celebratory character or message, oversized "AGAIN!" button to restart
  - (Optional) Parent gate screen — simple "tap the big number" parent check before accessing settings
  - (Optional) Settings screen — parent-controlled: goal size preset (easy/medium/hard), sound on/off
- Key UI interactions:
  - Shake gesture → spawns animated stars that fly into the goal container
  - Goal container fill animates in real-time as stars accumulate
  - At 100% fill → auto-transition to Celebration screen, no tap required
  - Celebration screen "AGAIN!" button → resets counter, returns to Play screen
  - All tap targets must be oversized (kids have imprecise motor control) — minimum 80px touch targets, bright high-contrast colors
- Data each screen needs:
  - Play screen: currentStarCount (int), goalStarCount (int), shakeThreshold (float), isSoundEnabled (bool)
  - Celebration screen: totalStarsCollectedThisRound (for display), which celebratory animation to show
  - Settings screen: goalPreset (easy=20/medium=50/hard=100), soundEnabled (bool)
