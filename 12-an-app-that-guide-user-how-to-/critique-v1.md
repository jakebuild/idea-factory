Verdict: WORTH BUILDING
Score: 6/10

What's Actually Good:
- The core insight is solid and research-backed — grayscale mode, removing icon colors, and ugly wallpapers genuinely reduce dopamine hooks and phone compulsion. This isn't pseudoscience.
- "Guided setup wizard" is a clever framing. Instead of building enforcement features (which require deep OS permissions), you're just teaching people what to change. Low-tech, low-risk.
- Clear target audience: people who already know they're addicted and are actively seeking friction. High intent users = easier to market.
- No backend needed. This can be a fully offline guide app — zero infra, zero ops, zero server costs.
- It's a content + UX problem, not an engineering problem. A solo dev with AI can absolutely nail this.
- There's a growing anti-smartphone movement (dumbphone trend, digital minimalism, Cal Newport crowd). The timing is reasonable.

Brutal Feedback:
- This is basically a YouTube tutorial or a Notion doc dressed up as an app. "Guide the user to change settings" = a checklist. You're one blog post away from being irrelevant.
- The problem with a guide app: it has zero replayability, zero retention hook. User opens it once, follows the steps, closes it, never returns. How do you monetize a one-time-use app?
- "Contract" is vague nonsense. A contract with who? Themselves? That's a journaling app feature, not a standalone concept. What does this actually mean in product terms?
- The value prop cannibalizes itself. If the app works, the user will also stop using YOUR app. You're building the product that kills your DAU.
- Android vs iOS split is a real problem. The steps to enable grayscale, screen time limits, and accessibility settings are completely different across Android versions and iOS versions. You're not building one guide — you're building a matrix of guides. That's a maintenance nightmare.
- Every major competitor already does this for free: Screen Time (iOS built-in), Digital Wellbeing (Android built-in), One Sec, BeReal's anti-doom-scroll features. You need a strong "why this app vs. just Googling it" answer.
- "Font size" is listed as an ugliness tool — that's an accessibility feature for elderly users, not a compulsion reducer. The feature list feels padded and unfocused. What's your actual theory of change?
- No engagement loop means no word-of-mouth. The only viral vector is "my friend told me about this app that makes your phone ugly." That's a weak referral surface.
- If you add tracking (did the user actually make the changes?), you now need OS-level integrations or you're on the honor system. Honor system apps don't retain users.

Key Questions:
- What's the single strongest ugliness intervention? Pick one hero feature instead of listing 8 things.
- How do you verify the user actually completed the steps? Without verification, the checklist is just a to-do list.
- What's the business model? Free download with no monetization = hobby project. Paid upfront? Subscription to a one-time-use app? Neither works well.
- Who is your user the week after they set it up? Do they ever come back? What for?
- iOS vs Android: which do you build for first and why?
- What's the "before/after" moment that makes this shareable? Can users post their ugly home screen?

Suggestions:
- Flip the retention problem: add a "daily check-in" that shows users their screen time going down over weeks. Now the app has a reason to exist after onboarding.
- Lean into the social angle hard — ugly home screen challenges, before/after screenshots, a community of digital minimalists. That's your growth loop.
- Make it iPhone-first only. iOS Screen Time API gives you more to work with and the audience skews toward people who will pay.
- Consider a paid one-time-purchase model ($2.99) — matches the "one-time setup" nature of the product. No subscription guilt.
- The "contract" idea could actually be interesting if you frame it as accountability: send a commitment email to a friend who gets notified if your screen time goes up. That's a novel feature worth building.
- Add a "grayscale timer" — the phone goes grayscale after 9pm automatically. That's a specific, differentiated, automatable feature that a guide app can't do.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? YES — if scoped to a guided setup checklist + screen time tracking display. The core is just a multi-step wizard with static content. No backend required. React Native or a simple native app with 8-10 screens is very doable.
- Biggest solo complexity traps:
  - Platform fragmentation: maintaining separate instruction sets for iOS 17, iOS 18, Android 13, Android 14 will silently eat your time. Pick one OS and ship.
  - Trying to automate the phone changes programmatically — iOS sandboxing will block you. Stay in "guide" mode, don't try to apply settings for the user.
  - Scope creep on the "contract" feature — accountability features require notifications, reminders, possibly backend. Cut it for v1.
  - Screen time data access on iOS requires Screen Time API with parental controls entitlement — getting Apple approval for this takes weeks and may be rejected.
  - Adding tracking/verification of whether the user followed steps = rabbit hole. Keep it honor-system for v1.

Design Handoff:
- Screens needed:
  1. Onboarding / Welcome screen — "Take back your focus" value prop, single CTA to start
  2. Assessment screen — "How bad is your addiction?" quick 3-question self-rating (sets urgency)
  3. Setup Wizard screen (multi-step) — step-by-step guide with screenshots for each ugliness setting (grayscale, wallpaper, remove colors, screen time limits, remove social apps from home screen)
  4. Step detail screen — zoomed-in instruction for each individual setting with OS-specific screenshots and "Mark as done" checkbox
  5. Progress / Checklist screen — overview of all steps, how many completed, visual "ugliness score"
  6. Commitment screen — user sets a screen time goal and optionally enters an accountability email
  7. Dashboard / Home screen (post-setup) — shows daily screen time trend, streak of staying under goal, ugliness score
  8. Settings screen — OS selection (iOS/Android), notification preferences, reset progress
- Key UI interactions:
  - Swipe-through wizard for setup steps
  - "Mark as done" tap per step with satisfying checkmark animation
  - Before/after comparison slider for home screen ugliness
  - Goal-setting number picker for daily screen time limit
  - Share button to post ugly home screen to social
- Data each screen needs:
  - Assessment: 3 self-reported answers (no persistence needed)
  - Setup Wizard: list of steps with title, description, screenshot asset, OS variant, completion boolean (local storage)
  - Dashboard: screen time data (from iOS Screen Time API or manual user input), completion count, streak counter
  - Commitment: goal value (minutes/day), accountability email (optional, local only for v1)
