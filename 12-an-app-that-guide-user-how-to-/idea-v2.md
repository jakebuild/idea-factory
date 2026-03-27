# Idea #12: UglyPhone — The Guided Digital Detox Setup App (Improved)

## Version: v2
**Date:** 2026-03-25 20:52
**Status:** improved

## Original Idea
An app that guides users to make their phone ugly (screen, background, screen time limit, contract, color, font size...) so they reduce phone addiction and focus on working.

## What Changed and Why

- **Cut Android support entirely, go iPhone-only** — The critique identified platform fragmentation as a silent time-killer. iOS 16+ settings are consistent, and the Screen Time API exists (even if limited). Maintaining separate Android instruction sets for every OS version is a maintenance trap for a solo dev. iPhone users also skew toward paying for apps.

- **Replaced the vague "contract" with a shareable ugly home screen card** — "Contract with yourself" is journaling-app territory and needs a backend to be meaningful. Instead, after completing setup, the app generates a shareable before/after card showing the user's new ugly home screen. This is the organic growth loop: users post it, friends ask "what is that?", they download the app. Zero backend, pure local generation.

- **Added a daily Focus Dashboard as the retention hook** — The core flaw was zero reason to return after onboarding. The dashboard shows screen time trend pulled from iOS Screen Time (or manual entry if API access is denied), a streak counter for days under goal, and the user's "ugliness score." This gives users a reason to open the app daily and makes the value visible over time, not just at setup.

- **Picked one hero intervention: grayscale + app icon removal** — The original listed 8 vague features (including font size, which is an accessibility tool, not a compulsion reducer). The single most effective and visually dramatic change is going grayscale + hiding social app icons from the home screen. Everything else is secondary. A clear theory of change: reduce visual reward → reduce compulsion.

- **Set a $2.99 one-time price** — Subscription for a setup-and-track app creates subscription guilt. One-time purchase matches user psychology: "I'm paying to solve a problem once." It also filters for high-intent users who are serious about changing behavior.

## Improved Description

UglyPhone is an iPhone-only app that walks users through a proven 5-step setup to make their phone deliberately less appealing — grayscale display, plain dark wallpaper, hiding social apps from the home screen, enabling Screen Time limits, and reducing notification badges. The setup takes 10 minutes. After that, the app becomes a daily focus tracker: it shows your screen time trend, your streak of staying under your daily goal, and your "ugliness score" (how many anti-addiction settings are still active).

The key insight is that most people know they should do this but never get around to it. UglyPhone is the push that actually gets it done, with a step-by-step guide, OS-specific screenshots, and a satisfying checklist. After completing setup, the app generates a shareable before/after home screen card — the natural growth loop for an audience that already talks about digital minimalism online.

No backend. No subscription. No OS-level permission nightmares. Just a wizard, a tracker, and a share button.

## Why This Is Worth Building (Solo + AI)

A solo dev with AI coding tools can ship this in 2-3 weeks because the core is static content (screenshots + instructions) wrapped in a multi-step wizard — there's no novel engineering, just good UX execution. The daily dashboard either uses a simple manual input flow (no API approval needed) or the Screen Time API as an enhancement, so the app ships regardless of Apple's API approval timeline. The $2.99 one-time model means even modest downloads (500-1000 in year one) cover the cost of building it, and the before/after share card gives it an organic growth vector that costs nothing to maintain.

## Solo MVP Scope

- **What's in v1:**
  - Onboarding (2 screens: welcome + quick addiction self-rating)
  - 5-step guided setup wizard with iOS-specific screenshots and "Mark as done" per step
  - Progress checklist screen showing completion and ugliness score
  - Goal-setting screen (daily screen time target in minutes)
  - Daily dashboard with screen time trend (manual input) and streak counter
  - Before/after home screen share card generator (static template, user screenshots pasted in)
  - $2.99 one-time paywall after the assessment screen (or after step 1 of wizard)
  - Settings screen (reset progress, notification time for daily check-in reminder)

- **What's out of v1:**
  - Android support — cut entirely
  - Accountability email / social commitment feature — needs backend
  - Grayscale automation / Shortcuts integration — scope creep, guide-only for v1
  - Screen Time API integration — pursue after ship; manual input is the fallback
  - Community / social feed — not needed to validate core value
  - In-app before/after photo comparison slider — nice to have, cut for time
  - Multiple "profiles" or presets — one path only

- **Estimated build time with AI coding tools:** 12-16 days (2.5 weeks)

## Open Questions

- Will Apple approve a $2.99 app in the "Health & Fitness" or "Productivity" category that doesn't use the Screen Time API? (Likely yes — it's a guide app, not an enforcement app.)
- Does the manual screen time input flow feel too friction-heavy on day 2+? Consider a simpler "did you hit your goal today? Yes/No" tap instead of entering a number.
- Is the before/after share card actually shareable without the user manually screenshotting their home screen first? Need to define the exact flow — probably: "Screenshot your home screen now, then screenshot it after step 5, and we'll put them side by side." Simple, no photo library permission needed if user just screenshots the card.
- What's the right paywall placement? After assessment (captures intent) vs. after step 1 (shows value first)?

## Design Handoff

List every screen/view the app needs:

- **Screen 1: Welcome** — Hero value prop ("Stop scrolling. Start working."), single CTA button "Start the Detox", app name/logo. Full-bleed dark background, minimal text.

- **Screen 2: Addiction Assessment** — 3 quick self-rating questions (e.g., "How many hours/day on your phone?", "Do you pick up your phone without thinking?", "Has your phone hurt your focus at work?"). Tap-to-select options (not a form). Shows urgency score at the end ("You're moderately hooked — let's fix that in 10 minutes").

- **Screen 3: Paywall** — $2.99 one-time purchase screen. Short list of what they get (5-step guide, daily tracker, ugliness score, share card). Single "Get UglyPhone — $2.99" CTA. Restore purchase link below.

- **Screen 4: Setup Wizard — Step Overview** — Shows all 5 steps as a vertical list with icons, completion checkmarks, and a progress bar at top. Tapping a step opens its detail screen. "All done" CTA activates when all 5 are checked.

- **Screen 5: Step Detail Screen** — Reused for all 5 steps. Shows: step title, 1-sentence explanation of why this reduces addiction, iOS screenshot(s) showing exactly where to tap, "Mark as Done" button at bottom. Navigation: back/next arrows.
  - Step 1: Enable Grayscale (Accessibility → Display → Color Filters)
  - Step 2: Set plain black wallpaper (provided as downloadable asset in-app)
  - Step 3: Move social apps off home screen to App Library
  - Step 4: Set Screen Time daily limit for social apps
  - Step 5: Turn off all non-essential notification badges

- **Screen 6: Setup Complete / Ugliness Score** — Celebratory screen. Shows "Ugliness Score: 5/5" with a visual meter. Displays the before/after prompt: "Now screenshot your home screen for your share card." CTA: "Set My Daily Goal →"

- **Screen 7: Goal Setting** — User picks a daily screen time goal (number picker: 30 min, 1 hr, 1.5 hr, 2 hr, custom). Secondary: set a daily check-in reminder time. CTA: "Start Tracking →"

- **Screen 8: Daily Dashboard (Home)** — Shown on every subsequent open. Top: streak counter ("Day 4 under goal"). Middle: 7-day screen time bar chart (manual input or Screen Time API). Bottom: ugliness score with quick re-check ("All 5 settings still active?"). FAB or top-right: share card button.

- **Screen 9: Daily Check-In Modal** — Triggered by reminder or dashboard tap. "How much screen time today?" number input (hours + minutes) or simplified "Under goal / Over goal" toggle. Updates dashboard chart. Can be dismissed.

- **Screen 10: Share Card Generator** — Shows a two-panel card: left = "Before" placeholder (user taps to paste/screenshot their old home screen), right = "After" (current ugly home screen). App branding at bottom. "Share" button exports as image to camera roll / share sheet.

- **Screen 11: Settings** — Reset progress, change daily goal, change reminder time, restore purchase, contact/feedback link.

**Key interactions:**

- **Onboarding flow:** Welcome → Assessment → Paywall → Setup Wizard (Steps 1–5) → Setup Complete → Goal Setting → Dashboard
- **Daily return flow:** Open app → Daily Check-In Modal (if triggered) → Dashboard → optionally tap a step to re-verify it's still active
- **Share flow:** Dashboard → Share Card → paste screenshots → export to camera roll → native iOS share sheet
- **Step wizard navigation:** Overview list → tap any step → detail screen → "Mark as Done" → returns to overview with that step checked
- **Paywall restore:** Settings → Restore Purchase → StoreKit restore flow

### Critique

**Score: 5/10 — NEEDS MAJOR WORK**

The v2 improvements are real: iPhone-only, one-time pricing, no backend, clear scope. But the fundamental business model hasn't been solved. The core product is a $2.99 guide to settings that are freely documented everywhere online. The daily dashboard retention is philosophically broken (app success = users stop opening the app). The before/after share card has too much UX friction to drive real virality. iOS screenshot maintenance is a solo dev treadmill. Competition from free tools like One Sec and Opal is severe. The economics are brutal: even 1,000 downloads generates ~$2k gross before Apple's cut and taxes — barely worth the ongoing maintenance burden. The path forward is either kill the paywall and monetize something harder (Shortcuts automation, coaching), or kill the idea and post a free Shortcuts file to Reddit instead.

## Version History

- v1: Original idea — guided ugliness setup wizard for phone addiction
- v1.1: Critique — one-time-use app with no retention, platform fragmentation, vague contract feature, no business model
- v2: Improved — iPhone-only, daily dashboard as retention hook, before/after share card as growth loop, $2.99 one-time paywall, cut Android and backend features
