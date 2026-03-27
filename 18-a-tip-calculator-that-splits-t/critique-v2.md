# Critique v2 — Idea #18: SplitTab

**Verdict:** WORTH BUILDING
**Score:** 7/10

---

## What's Actually Good

- **The QR-code multi-phone flow is the right call.** This is the differentiating feature that makes the idea actually interesting. It solves the v1 ambiguity (one phone vs. many phones) decisively and correctly. Nobody wants to pass a single phone around a table of six.
- **Shared dish splitting as first-class, not afterthought.** The "split between" multi-select model is exactly right. This is what every other quick-build in this space punts on. Doing it well is the whole moat.
- **No-install web app is mandatory, not optional.** Getting this right in v2 is huge. The conversion rate for "download this app to split your bill" is zero. A QR-scannable PWA is the correct mental model.
- **Scope is ruthlessly trimmed.** No OCR, no Venmo, no accounts, no persistence. The feature list is realistic for a 2-week build. The builder actually understands what to cut.
- **The design handoff in the idea itself is unusually thorough.** Seven screens, key interactions, data model per screen — this is rare for an ideation doc and signals the builder has already thought through the UX seriously.

---

## Brutal Feedback

- **Real-time sync is not "lightweight."** Calling Supabase Realtime or Partykit a lightweight backend is cope. You are now building a distributed system that needs to handle concurrent writes (two people claim the same item simultaneously), connection drops (someone's phone goes to sleep mid-dinner), and reconnection logic. At a restaurant with spotty WiFi and mixed cellular, this will fail in ways that are embarrassing and hard to debug. This is not a weekend task — it's the hardest part of the build and the idea breezes past it.
- **The host's item entry bottleneck is still there.** OCR is punted to "stretch goal," which means the host is manually typing every item off a restaurant receipt. A typical bill has 8–15 line items with slightly wrong names and decimal prices. At a table where everyone is talking and waiting, this is the moment the app dies. Without fast item entry, the QR share flow never even starts.
- **Tab (the app), Dineout, and Divvy already do this.** The QR-based collaborative claim flow is not new. Tab (iOS) launched this exact interaction years ago and built a following before pivoting. You're not inventing the category — you're entering an already-attempted space where others failed to find sustainable traction. The v2 improvement is real, but the differentiation story is still thin vs. existing players.
- **Zero monetization plan.** The idea doesn't mention how this makes money. Ad-supported? Tip on the tip (charge a 1% fee)? "No accounts, gone when session ends" is a great UX choice but a terrible business choice — there is no retention hook, no email capture, no way to reach users again. This is fine for a portfolio piece or side project but not for anything you'd call a product.
- **"Gone when the session ends" kills the referral loop.** The only growth mechanism is the QR code itself — the host shows it to the table, people scan it, they never have to find the app themselves. That's a strong in-person distribution path. But there's no way for guests to share the app later, no history to come back to, no reason to bookmark it. Organic growth is entirely word-of-mouth from the host.
- **The 60-seconds-per-person claim is fantasy at a real dinner table.** Scan QR, wait for page to load, enter a name, figure out the UI, identify your items from a list of 12 things, handle the shared appetizer... this is a 3–5 minute process minimum, with at least one person at every table who can't get the QR to scan or doesn't understand what they're being asked to do. The UX needs to be almost idiot-proof, which is harder than it sounds.
- **Race conditions on shared item claims need explicit UI design.** If two people tap "add me" on the wine bottle at the same moment, what happens? Optimistic updates + a clear "N people splitting this" counter need to be designed explicitly, not assumed to work. The v2 says "shared items split among whoever selects them" but doesn't resolve the concurrent mutation problem.

---

## Key Questions

- What's the minimum viable backend? Have you actually prototyped Supabase Realtime at a table of 6 with everyone on cellular? The latency and reliability story matters.
- What happens when the host accidentally closes the tab before finalizing? Session recovery with no accounts is a hard UX problem.
- Who is the repeat user — the host? If so, what's the hook that makes them remember this app exists next time they're at a restaurant?
- What's the business model? If this is a portfolio/fun project, fine. If you want it to become something real, the "no accounts, no persistence" design needs to be reconciled with sustainable growth.
- Have you looked at why Tab (the app) pivoted away from this exact flow? Understanding where they failed is table stakes research.

---

## Suggestions

- **Prototype the real-time sync before building anything else.** Seriously. Spin up a Supabase Realtime instance and test it on 4 phones at a coffee shop. If the latency and reconnection story isn't good, you need to know before you've built 7 screens around it.
- **Give the host a "fast item entry" shortcut.** Even without OCR, a "common items" suggestion list (beer, wine, burger, salad, etc.) or quick-add with just a price and auto-name ("Item 1 — $14.00") would reduce the entry bottleneck dramatically.
- **Design for the one person at the table who won't scan.** A 4-digit table code as fallback (type it into the URL or a landing page) eliminates the "QR won't scan" failure mode entirely.
- **Add a "host sees unclaimed total" warning.** If 3 items totaling $40 are still unclaimed when the host hits Finalize, show a prominent warning. This prevents the host from eating costs accidentally.
- **Consider a simple link-based share instead of (or in addition to) QR.** "Text this link to your table" is sometimes faster than QR in practice, especially in low-light restaurant environments.

---

## Solo Dev Reality Check

- **Can one person ship this in 2–4 weeks with AI coding tools?** MAYBE — the UI is well-scoped and AI tools handle React/Tailwind UI fast. The real-time sync layer is the wild card. If Supabase Realtime works as advertised and the builder has used it before, 2 weeks is plausible. If the builder is learning real-time sync from scratch while building 7 screens, this stretches to 4–6 weeks easily.
- **Biggest solo complexity traps:**
  - Real-time sync reliability on flaky restaurant networks — this is the #1 risk and it's underestimated in the idea doc
  - Concurrent claim mutations (two people claiming the same item simultaneously) — needs explicit conflict resolution design
  - Session recovery with no accounts — what happens if the host's phone dies, browser refreshes, or session expires
  - Getting item entry fast enough that people don't give up before sharing the QR — without OCR, the bottleneck is real
  - Cross-device UI consistency — designing a layout that works on both the host's iPad-sized screen and a guest's small Android phone in a dark restaurant

---

## Design Handoff

### Screens needed
1. **Home / New Session** — Single large CTA "Start Splitting," optional table/group name field, no signup noise. Loads instantly.
2. **Item Entry (Host)** — Running list of items (name + price), inline add-row always pinned at bottom (name field + price field + "+" button), running subtotal visible, prominent "Share with table →" CTA at bottom.
3. **QR Code / Share Screen** — Large centered QR code, shareable link + 4-digit code below, live "X people joined" counter, brief instruction text. Host can proceed before everyone joins.
4. **Guest Claim View** — Full-screen scrollable list of all items. Tap to claim. Selected items highlight. Shared items show "X others also claimed this — splitting N ways." Name field at top (optional). Minimal chrome — this needs to be scannable in 30 seconds.
5. **Host Live View** — Same item list with per-item assignment status. Color-coded: claimed solo (green), shared (yellow/amber), unclaimed (grey/red). Tap any item to manually assign. "Finalize" button with unclaimed-items warning if applicable.
6. **Tip & Tax** — Tax amount input with "tax already included" toggle. Tip preset buttons (15/18/20%) + custom slider. Live per-person total preview updates as tip changes. Pre/post-tax toggle clearly labeled. Big and thumb-friendly — this is used at a table.
7. **Summary / Results** — Each person's name, itemized list, tax share, tip share, **large bold total**. Overall bill at bottom. "Copy summary as text" CTA primary, "Share screenshot" secondary. Terminal state — no back nav.

### Key UI interactions
- Host flow: Home → Item Entry → Share QR → Live View → Tip & Tax → Summary
- Guest flow: Scan QR (or enter 4-digit code) → Claim View (tap items) → done automatically when host finalizes
- Tap shared item as a guest: if already claimed by others, shows "N people splitting — add me?" confirmation
- Unclaimed item warning modal before host can finalize: "3 items ($38.50) unclaimed — assign them or they'll be split equally"
- Tip slider: all person totals update in real time with no lag
- Summary screen: copy-to-clipboard formats as clean plain text (name + total, one per line)

### Data each screen needs
- **Item Entry:** items[] (id, name, price, claimedBy[]), sessionId, hostName, runningSubtotal
- **QR/Share:** sessionId, shareURL, 4-digit code, participantCount
- **Guest Claim:** items[] with claimStatus per item, guestName, sessionId
- **Host Live View:** items[] with claimedBy[] per item, participantList, unclaimedTotal
- **Tip & Tax:** perPersonSubtotal[], taxAmount, taxIncluded bool, tipPercent, tipOnPreTax bool
- **Summary:** perPerson[] (name, items[], taxShare, tipShare, total), billTotal, sessionId (for sharing)
