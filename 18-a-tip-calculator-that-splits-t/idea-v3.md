# Idea #18: (Improved)

## Version: v3
**Date:** 2026-03-27 11:02
**Status:** improved

## Original Idea
A tip calculator that splits the bill and lets each person mark which dishes they ordered

## What Changed and Why

- **Replaced real-time WebSocket sync with simple polling** — The critique correctly called out that "lightweight backend" for Supabase Realtime or Partykit is wishful thinking. WebSockets require handling concurrent writes, connection drops, and reconnection on flaky restaurant WiFi — that's a distributed systems problem, not a UI problem. v3 switches to 2-second HTTP polling: guests POST their claims, the host's view polls for updates. Same perceived UX, zero WebSocket complexity, works on any connection. This eliminates the #1 build risk from the critique.
- **Fast item entry shortcuts to kill the bottleneck** — The critique flagged that manually typing 12 items while everyone's waiting is the moment the app dies. v3 adds a quick-entry mode: a scrollable grid of common restaurant items with auto-set prices (beer, wine, burger, salad, pizza, fries, etc.) that the host can tap to add instantly. Custom items still work with name + price. This isn't OCR — it's a curated shortcut list that covers ~60% of real bill items. The host entering 10 items should take under 90 seconds.
- **4-digit code always built-in, QR is secondary** — The critique noted QR fails silently in dark restaurants and at least one person at every table can't scan. v3 flips the mental model: the primary share mechanism is a 4-digit code shown large on screen (guests type it at splittab.app/join). The QR code is still there but secondary. This eliminates the "QR won't scan" failure mode entirely and removes a whole class of edge case debugging.

## Improved Description

**SplitTab** is a no-install web app for splitting restaurant bills collaboratively. The host opens the app, builds the bill using a quick-tap shortlist of common items (or manual entry), and gets a 4-digit session code. Everyone at the table types the code at splittab.app/join from their own phone — no QR, no download. They tap the items they ordered; shared dishes show "N people splitting" and divide automatically. The host sees a live view (polling every 2 seconds) of claimed vs. unclaimed items, manually assigns anything left over, adjusts tax and tip, then finalizes. Every person's screen shows their total in large text with a one-tap plain-text copy. Session lives for 24 hours then auto-deletes. No accounts, no payment processing, no persistence.

The entry shortlist is the key UX unlock: a scrollable grid of 40 common items (beer, wine, cocktail, burger, pizza, salad, fries, appetizer, dessert, etc.) with typical price ranges the host can tap and adjust. For unusual dishes, there's an "add custom" escape hatch. Most bills at casual restaurants are buildable in under 2 minutes.

Polling-based sync means the backend is a simple key-value store with a 24-hour TTL — no persistent connections, no reconnect logic, no race condition infrastructure. Two people claiming the same item simultaneously is resolved by server-side "last write wins" + a visible counter ("3 people splitting this") that updates on the next poll. No conflicts need explicit resolution because splitting more ways is always recoverable.

## Why This Is Worth Building (Solo + AI)

Polling + KV store is genuinely buildable by one person in 10 days — there's no WebSocket infrastructure, no conflict resolution logic, and no connection state management. The item entry shortlist is a JSON file. The entire backend is "store a session object, return it on GET, update on POST" — AI tools write this in an afternoon. The hard part is the UI polish, which is exactly where vibe coding with Cursor/v0 excels.

## Solo MVP Scope

- **What's in v1:**
  - Host creates session, builds bill via quick-tap shortlist + custom items
  - 4-digit session code (+ QR as bonus) generated instantly
  - Guest join via 4-digit code at /join
  - Per-item claim: tap to claim, tap again to unclaim, shared items split automatically
  - Host live view: color-coded claimed/shared/unclaimed, manual assign on any item
  - Unclaimed item warning before finalize ("$38 unclaimed — split equally or assign?")
  - Tax entry (flat amount or %) with "tax included" toggle
  - Tip presets (15/18/20%) + custom, pre/post-tax toggle
  - Per-person summary with large total, copy-as-text CTA

- **What's out of v1:**
  - Receipt OCR or photo scanning
  - Real-time WebSocket sync (polling only)
  - Payment integration (Venmo, Zelle links)
  - Session history or saved receipts
  - User accounts or login of any kind
  - Currency selection (USD only)
  - Itemized tax per dish

- **Estimated build time with AI coding tools:** 10–12 days

## Open Questions

- What KV/storage backend? Upstash Redis (simple, cheap, 24h TTL built-in) vs. Supabase (more familiar but overkill). Pick before building anything.
- What's the right polling interval? 2 seconds feels fast enough; 3–4 seconds reduces load but may feel laggy. Test this early.
- Should unclaimed items default to "split equally among everyone" or "assigned to host"? Needs a clear default with an override — probably "split equally" is safer.
- What happens at 24h TTL — does the session silently vanish or does the app show a clear "session expired" state? The latter requires a check on every load.
- Is a "tip the developer" optional micro-donation (e.g., $1 via Stripe) worth adding on the summary screen? Minimal code, covers hosting costs, unobtrusive.

## Design Handoff

- **Screen 1: Home / New Session** — Single large "Start Splitting" CTA, optional group name field. Minimal. No signup noise. "Join a session" secondary link for guests who were sent a direct URL.
- **Screen 2: Item Entry (Host)** — Two-tab layout: "Quick Add" grid (40 common items as tappable cards with default price, adjustable) and "Custom" tab (name + price fields). Running item list below with edit/delete per row. Running subtotal always visible. Prominent "Share with table →" CTA fixed at bottom.
- **Screen 3: Share Screen** — Large 4-digit code centered and bold (the primary affordance). QR code below it, smaller. Shareable link. Short instruction: "Everyone goes to splittab.app and enters this code." Live "X people joined" counter. Host proceeds without waiting.
- **Screen 4: Guest Join** — Simple landing at /join. Single large numeric input for 4-digit code. Optional name field ("What should we call you?"). "Join" button. Dead simple — this is the highest-friction moment and must feel effortless.
- **Screen 5: Guest Claim View** — Scrollable list of all items (name + price). Tap to claim (highlights green). Tap shared item: shows "2 others also claimed this — you'll split 3 ways" inline. No modals. Header shows guest's name + running "your total so far" in small text. No submit button — claims save automatically on each tap.
- **Screen 6: Host Live View** — Same item list with status indicators: green (claimed solo), amber (shared — shows N people), grey (unclaimed). Tap any item to manually assign to a person. Unassigned total shown at top as a warning chip. "Finalize →" button, disabled while items are unassigned (unless host explicitly chooses "split equally" override).
- **Screen 7: Tip & Tax** — Tax: flat amount input with "already included" toggle. Tip: 15/18/20% preset buttons + custom percentage input. Pre/post-tax tip toggle (labeled clearly). Live per-person total breakdown updates as tip changes. Large, thumb-friendly — used at a table in low light.
- **Screen 8: Summary / Results** — Each person: name, itemized list, tax share, tip share, **bold large total**. Bill total at bottom. Primary CTA: "Copy summary as text" (formats as clean plain text). Secondary: "Share screenshot." Optional: "Tip the developer $1" link below the fold.

Key interactions:
- Host flow: Home → Item Entry → Share Screen → Live View → Tip & Tax → Summary
- Guest flow: /join (enter 4-digit code + name) → Claim View (tap items, auto-saves) → see updated total live
- Quick Add: tap item card → added to list at default price → tap price to edit inline
- Shared item claim: tap item already claimed by others → inline confirmation "N others splitting — add me?" → confirm
- Host finalizes with unclaimed items: modal "3 items ($38.50) unclaimed. Split equally among everyone, or go back to assign?" — two buttons
- Copy summary: formats as "Alice: $24.50\nBob: $31.00\n..." — paste-ready for group chat

## Version History

- v1: Original idea
- v2.1: Critique — real-time sync underestimated, item entry bottleneck unresolved, QR-only share fragile, no 4-digit fallback
- v3: Improved — polling replaces WebSockets, quick-add shortlist kills entry bottleneck, 4-digit code is primary share mechanism
