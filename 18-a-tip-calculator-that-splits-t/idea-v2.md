# Idea #18: (Improved)

## Version: v2
**Date:** 2026-03-27 10:59
**Status:** improved

## Original Idea
A tip calculator that splits the bill and lets each person mark which dishes they ordered

## What Changed and Why

- **QR-code shared session instead of "one person drives"** — The critique nailed the core UX ambiguity: is this one-phone or many-phones? The answer is many-phones, via a QR code the host generates. Everyone scans, claims their own items, and the host sees the live totals. This is the actual differentiating feature that no basic tip calculator app ships; it makes splitting feel collaborative instead of delegated.
- **Shared dish splitting added as a first-class feature** — The critique called this "the real UX nightmare you haven't solved." Every group dinner has shared apps, wine, or bread. The improved idea treats multi-person item assignment (split N ways among a subset) as a core interaction, not an afterthought — with a simple "split between" multi-select on each item.
- **Ruthlessly trimmed to a fast web app, no install required** — The critique flagged that requiring an app install mid-dinner kills conversion to zero. This is a PWA/web app that loads instantly from a link or QR scan. No accounts, no backend persistence beyond the session, no friction.

## Improved Description

**SplitTab** is a fast, no-install web app for splitting restaurant bills at the table. The host opens the app, enters the bill items (or snaps a photo of the receipt — stretch goal), and shares a QR code. Everyone at the table scans it from their own phone and taps which items they ordered. Shared dishes (apps, wine, sides) get split evenly among whoever selects them. The app handles tax and tip automatically — tip on subtotal only, with a clear percentage slider and a pre/post-tax toggle. The final screen shows each person's total in large text with a one-tap copy of the full breakdown. No signup. No download. Gone when the session ends.

The core loop is under 60 seconds per person: scan QR → tap your dishes → done. The host sees a live view as people claim items, and can manually assign anything unclaimed before finalizing.

## Why This Is Worth Building (Solo + AI)

The math is trivial — this is entirely a UI/UX problem, which is exactly where AI coding tools (v0, Cursor, Claude) shine. The QR-code shared session is the only technical complexity, and it can be done with a simple shared state via a lightweight backend (Cloudflare Workers KV or Supabase realtime) without building anything bespoke. One person can ship a polished, fast v1 in 2 weeks.

## Solo MVP Scope

- **What's in v1:**
  - Host creates a session, enters items (name + price)
  - QR code / shareable link generated instantly
  - Each person on their phone claims items (tap to assign)
  - Shared items split among selected people automatically
  - Tax entry + tip percentage slider (15/18/20/custom)
  - Pre/post-tax tip toggle
  - Final summary screen — each person's total, clean text to copy or screenshot
  - Host can manually reassign unclaimed items before finalizing

- **What's out of v1:**
  - Receipt photo scanning / OCR
  - Venmo / payment links
  - Session history / saved receipts
  - Itemized tax per dish (just split tax proportionally)
  - Currency conversion
  - User accounts

- **Estimated build time with AI coding tools:** 10–14 days

## Open Questions

- What's the right backend for the shared session state — Supabase Realtime, Cloudflare Workers KV with polling, or Partykit? Affects complexity significantly.
- Should the host be able to lock items after someone claims them, or can anyone re-assign freely until the host finalizes?
- Is there a graceful fallback for the QR/multi-device flow when someone's phone won't scan (e.g., type-in code)?
- How do you handle a bill where tax is already included (UK, Australia, many countries)? A "tax included" toggle is probably sufficient.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Home / New Session** — Large "Start Splitting" CTA, minimal chrome. Option to name the table/group. Transitions immediately to item entry. No signup, no noise.
- **Screen 2: Item Entry (Host view)** — Running list of items added so far (name + price). Inline "Add item" row always visible at bottom (name field + price field + add button). Running subtotal visible. Button: "Share with table" → generates QR code.
- **Screen 3: QR Code / Share Screen** — Large QR code centered. Shareable link below it. Brief instruction: "Everyone scans to claim their items." Live counter: "3/5 people joined." Host can proceed without waiting for everyone.
- **Screen 4: Guest Claim View (each person's phone)** — Shows all items as a scrollable list. Each item has a checkbox. Shared items show a "split with others" label once multiple people select them. Name entry at top (optional but helps host view). Simple, fast — no friction.
- **Screen 5: Host Live View** — Same item list as guest view but with assignment status per item. Color-coded: claimed (green), shared (yellow), unclaimed (grey). Host can tap any item to manually assign/reassign. "Finalize" button once everything is claimed.
- **Screen 6: Tip & Tax** — Enter tax amount (or %) with toggle for "tax already included." Tip slider with preset buttons: 15% / 18% / 20% / Custom. Live preview of each person's updated total as tip changes. Pre/post-tax tip toggle shown clearly.
- **Screen 7: Summary / Results** — Each person's name, their items listed, their share of tax, their share of tip, and their **total in large bold text**. Overall bill total at bottom. Two CTAs: "Copy summary as text" and "Screenshot this." Optional: Venmo deep link per person (v2).

**Key interactions:**
- Host flow: Home → Item Entry → Share QR → Live View → Tip & Tax → Summary
- Guest flow: Scan QR → Claim View (tap items) → done (host sees update live)
- Any item can be tapped on the item entry screen to edit name/price before sharing
- Multi-select on shared items: tapping an already-claimed item shows who claimed it; guest can "add me too" to split it
- Tip slider updates all person totals in real time on the Tip & Tax screen
- Summary screen is the terminal state — no editing, just share/copy

## Version History

- v1: Original idea
- v1.1: Critique — too generic, missing shared dish splitting, UX paradigm undefined, crowded market
- v2: Improved — QR-code multi-phone session, shared dish splitting first-class, web app no install, scope trimmed to 2-week build
