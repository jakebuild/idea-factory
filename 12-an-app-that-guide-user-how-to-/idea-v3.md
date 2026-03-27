# Idea #12: Boring Phone — A Free Setup Guide + Paid Shortcuts Automation Kit (Improved)

## Version: v3
**Date:** 2026-03-25 20:55
**Status:** improved

## Original Idea
an app that guide user how to make their phone ugly (screen, background, screen time limit, contract, color, font size...) then they will reduce their addictions to their phone and focus on working

## What Changed and Why

- **Killed the paywall on the guide itself.** The critique is correct: paying $2.99 for steps that are freely available on YouTube and r/nosurf is indefensible. Motivated users already know how to Google "enable grayscale iPhone." Making the guide free removes the conversion cliff, removes the "this feels like a scam" moment after seeing step 1, and lets the app compete honestly against free blog posts and videos — because the guide is now the funnel, not the product.

- **Shifted the paid tier to iOS Shortcuts automation files ($4.99 one-time).** This is the structural fix the critique was pointing at: sell automation, not information. A downloadable `.shortcut` file that toggles grayscale on a schedule, hides distracting apps into a hidden folder, and activates a focus mode at 9am is something people genuinely cannot do themselves in 30 seconds. It's the "hard step" users skip. That's what $4.99 buys — not screenshots of Settings menus, but actual one-tap automation. The upsell happens naturally after step 3, when users get frustrated at how many taps it takes to do it manually.

- **Eliminated all iOS screenshots from the guide.** The maintenance treadmill is real: Apple has moved the grayscale toggle, Screen Time, and Notification settings in every major iOS release. Instead, the guide uses evergreen Settings path text ("Settings → Accessibility → Display & Text Size → Color Filters") plus a small icon next to each step. Text paths survive iOS updates; screenshots don't. This removes the biggest solo-dev maintenance burden entirely.

- **Replaced the daily dashboard with a 7-day push notification sequence.** The critique correctly identifies that a dashboard whose success metric (reduced phone use) destroys its engagement metric (daily app opens) is philosophically broken. Instead: after completing setup, the app schedules 7 local push notifications over 7 days using `UNUserNotificationCenter`. No backend. No Screen Time API entitlement. No manual input. Day 1: "Setup complete. Your phone is now boring. That's the point." Day 3: "Most people re-enable color by day 3. Check if yours is still gray." Day 7: "One week in. Your phone should feel like a tool now, not a toy." This is the entire retention strategy — it costs one afternoon to build and requires zero ongoing maintenance.

- **Simplified the share card to a single-image flow.** The before/after flow required 7 user steps and targeted people who are specifically trying to post less on social. The new flow: user takes one screenshot of their ugly home screen, the app puts it in a branded frame with a single line ("I made my phone boring. Day 1."), and they share. Two steps. The ugly home screen screenshot IS the content — it's visually striking, immediately legible, and requires no "before" context.

- **Renamed the concept from "make your phone ugly" to "Boring Phone."** "Ugly" is a hard sell to someone who paid $1,200 for their device. "Boring" is the actual goal — intentional, purposeful, tool-like. It reframes the action as design philosophy rather than self-punishment. Resonates with the digital minimalism and Cal Newport audience without sounding like a bug report.

## Improved Description

**Boring Phone** is a free iPhone app that walks users through 5 research-backed steps to reduce their phone's visual reward system — grayscale mode, a plain gray wallpaper, hiding social apps into a single folder, reducing notification badges, and setting a bedtime focus mode. Each step is text-based with no iOS-version-specific screenshots, so it stays accurate across updates.

The free guide covers the "what" and "why" of each step. The $4.99 Shortcuts Pack — the actual product — delivers 5 downloadable `.shortcut` files that automate the hard steps: a grayscale schedule (on at 9am, off at 9pm), a one-tap "hide distracting apps" automation, a focus mode scheduler, a wallpaper setter (picks a plain gray), and a notification mute toggle. These run from the iOS Shortcuts app and require no ongoing app interaction after setup.

After completing setup, the app schedules 7 motivational push notifications over the next 7 days — no backend, no account, no Screen Time API entitlement needed. At completion, users can generate a simple share card: their ugly home screen in a branded frame. One tap to share or save.

The business model is free download + one-time $4.99 IAP for the Shortcuts Pack. Long-term: the app is a lead-gen funnel for a "Boring Phone" newsletter covering digital minimalism habits, which can monetize with sponsorships or a paid tier later.

## Why This Is Worth Building (Solo + AI)

A solo dev can ship this because the technical surface is genuinely small: SwiftUI onboarding screens, local push notifications, StoreKit 2 for one IAP, and a static Shortcuts file distribution system. AI coding tools handle all the boilerplate. The Shortcuts files themselves are built in Apple's Shortcuts app — no code required — and distributed as URL-scheme deep links or file downloads. The maintenance burden is near-zero because there are no screenshots to update and no backend to keep alive.

This is worth building because it solves a real problem the competition doesn't touch: One Sec and Opal enforce behavior through friction and blocking, but neither helps users redesign their phone's aesthetic environment. Boring Phone is the "mise en place" step — set up your phone so it's less tempting, then the discipline apps work better. That's a different, complementary value proposition, not a head-on competitor.

## Solo MVP Scope

- **What's in v1:**
  - Onboarding (2 screens: value prop + notification permission ask)
  - 5-step setup guide (text-based, evergreen, no screenshots)
  - "Mark as Done" per step (no verification, ship it dumb)
  - 7-day local push notification sequence (scheduled at setup completion, no backend)
  - Shortcuts Pack paywall + StoreKit 2 IAP ($4.99 one-time)
  - 5 pre-built Shortcuts files distributed via URL scheme
  - Completion screen with one-tap share card (ugly home screen + branded frame)
  - iPhone only, iOS 16+

- **What's out of v1:**
  - Any dashboard or screen time tracking
  - Before/after comparison flow
  - "Ugliness score" or gamification
  - Android
  - Screen Time API / FamilyControls entitlement
  - Accounts, sync, backend of any kind
  - Streak tracking
  - Notification settings step-by-step screenshots
  - In-app Shortcuts builder

- **Estimated build time with AI coding tools:** 10–14 days. Week 1: onboarding + guide screens + notifications. Week 2: StoreKit IAP + Shortcuts distribution + share card. Buffer: 2–3 days for App Store submission and TestFlight.

## Open Questions

- Can `.shortcut` files be reliably distributed via URL scheme deep-link from inside an App Store app, or does Apple's review team flag this? (Test this before building the paywall around it.)
- What's the actual conversion rate on a $4.99 IAP for a free utility app in the productivity category? 10–20% is the optimistic range — run the math honestly: 500 downloads × 15% = 75 × $3.50 net = $262. Is that enough to justify 2 weeks of build time, or is this a portfolio/lead-gen play from day one?
- Is the r/nosurf and r/digitalminimalism community large enough to get organic traction, or does this need paid UA from launch?
- Does the "Boring Phone" name test well with the target demographic (25–40, knowledge workers, productivity-focused) or does it read as self-deprecating in a bad way?
- Should the 7 notification messages be customizable, or is a fixed script fine for v1?

## Design Handoff

List every screen/view the app needs:

- **Screen 1: Welcome / Value Prop** — Single screen, hero image of a plain gray iPhone home screen vs. a colorful cluttered one (illustrated, not screenshot). Headline: "Your phone is designed to distract you. Take it back." Two bullets on the research backing. Single CTA button: "Start Setup (Free)".

- **Screen 2: Notification Permission Ask** — Shown immediately after Welcome tap. Explains: "We'll send you 7 check-ins over the next week to help you stay boring. That's it — no daily nagging." Single CTA: "Allow Notifications". Skip link below. This is a custom pre-prompt screen before the iOS system dialog.

- **Screen 3: Guide Overview** — A vertical list of 5 steps with titles, each row showing: step number, step name, estimated time (e.g. "2 min"), and a checkmark (unchecked). A progress bar at the top showing 0/5. Tapping any row opens the detail screen. A sticky footer CTA: "Get the Shortcuts Pack — $4.99" (always visible).

- **Screen 4: Step Detail (template, reused for all 5 steps)** — Full-screen scroll. Step name + "Why this works" paragraph (2-3 sentences of research-backed reasoning). Then: "How to do it" section with numbered text steps using Settings path notation (no screenshots). A large "Mark as Done" button at the bottom. No verification prompt. Steps: (1) Enable Grayscale, (2) Set a Plain Wallpaper, (3) Move Social Apps to One Folder, (4) Turn Off Notification Badges, (5) Set a Bedtime Focus Mode.

- **Screen 5: Shortcuts Pack Paywall** — Shown when user taps "Get the Shortcuts Pack" from Guide Overview, OR after completing step 3 (natural friction point). Lists 5 included Shortcuts with name + one-line description each. Price: $4.99 one-time. "What you get" section emphasizes automation over information. CTA: "Unlock Shortcuts Pack". Restore Purchase link below. No free trial.

- **Screen 6: Shortcuts Hub (post-purchase)** — Replaces paywall after IAP. Same list of 5 Shortcuts, each with a "Add to Shortcuts" button that deep-links into the iOS Shortcuts app to install the file. Installation instruction: one line of text per Shortcut. No in-app experience beyond this — after tapping "Add to Shortcuts", users are in Apple's app.

- **Screen 7: Completion / Share Card** — Triggered when all 5 steps are marked done. Headline: "Your phone is now boring. Good." Subtext: "Share your setup with someone who needs to hear this." A preview of the share card (user's current home screen screenshot in a plain gray frame with the line "I made my phone boring." + app name in small text below). Two CTAs: "Share" (iOS share sheet) and "Save to Photos". Below: "We'll check in with you over the next 7 days." (Notification sequence is already scheduled from Screen 2.)

- **Screen 8: Settings / About** — Accessible from a gear icon in the top-right of Guide Overview. Options: Restore Purchase, About (one paragraph on digital minimalism philosophy + link to newsletter), Rate the App. No account, no data, no toggles.

**Key interactions:**

- **Main flow:** Welcome → Notification Ask → Guide Overview → each Step Detail (mark done) → Completion/Share Card. Linear, no branching except the notification permission skip.
- **Upsell flow:** Guide Overview footer CTA (or auto-prompt after step 3) → Paywall → StoreKit purchase → Shortcuts Hub → individual "Add to Shortcuts" deep-links (each opens iOS Shortcuts app).
- **Share flow:** Completion screen → user is prompted to take a screenshot of their home screen (app provides instruction: "Press side button + volume up") → returns to app → taps "Share" → iOS share sheet with pre-composed message.
- **Notification flow:** Silent background scheduling at notification permission grant (Screen 2). 7 notifications over 7 days. No UI to manage them in v1 — users use iOS notification settings if they want to turn them off.

## Version History
- v1: Original idea — guide users to make phone ugly to reduce addiction
- v2.1: Critique — charging for blog-post content, broken economics, iOS screenshot maintenance treadmill, retention paradox, no defensible moat vs. free competitors
- v3: Improved — free guide + $4.99 Shortcuts automation pack; evergreen text instructions (no screenshots); 7-day local notification sequence instead of dashboard; simplified single-image share card; renamed to "Boring Phone"
