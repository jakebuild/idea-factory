# Critique v1 — Idea #18: Tip Calculator with Per-Person Dish Splitting

**Verdict:** WORTH BUILDING
**Score:** 6/10

---

## What's Actually Good

- **Real pain point.** Splitting bills at restaurants is genuinely annoying. Everyone has been in the "wait who had the salmon?" conversation. This solves a real, recurring moment of friction.
- **Zero infrastructure needed.** No backend, no accounts, no database. Pure client-side math. This is a weekend build, not a sprint.
- **High usage frequency.** Every group dinner is a potential use case. If the UX is fast enough, people will reach for it habitually.
- **Already proven demand.** Splitwise, Venmo, and a dozen other apps do adjacent things — meaning people want this. The question is whether you can do it better for the specific "at the table" moment.

---

## Brutal Feedback

- **This market is brutally crowded.** Tip calculators are one of the most-built demo apps on earth. Every bootcamp student has made one. "With dish splitting" is a minor variation, not a moat. Why would anyone switch from the app they already use?
- **The core interaction is slower than just eyeballing it.** Entering every dish, assigning it to a person, and then calculating — by that point, most tables have already Venmoed someone $20 and moved on. The speed of the UX is everything and most implementations of this are clunky.
- **Shared dishes are the real UX nightmare you haven't solved.** You said "each person marks which dishes they ordered" — fine for individual items. But the appetizer, the bottle of wine, the bread — everyone touched those. How do you split a shared item between a subset of people? This is the hardest part of bill-splitting and the idea doesn't acknowledge it at all.
- **No retention mechanism.** Use it at dinner, close the tab, forget it exists. There's no reason to come back unless you actively remember it when the bill arrives. Discovery and habit formation are both hard problems.
- **"Marks which dishes they ordered" UX is ambiguous.** Does each person get their own phone screen? Does one person drive? Does the table pass the phone around? Session management for a shared table context is surprisingly tricky to design well.
- **Tip split logic has edge cases.** Do you tip on pre-tax or post-tax? Does everyone tip the same percentage or can individuals choose? What about service charges that are already included? These aren't hard to code but they're easy to get wrong and users will notice.

---

## Key Questions

- Is this a "one person enters everything" app or a "everyone touches their own phone" app? That's two completely different UX paradigms.
- How do shared/split dishes work? That's the feature that actually differentiates bill-splitters.
- Is the target output just "here's what each person owes" or does it also facilitate payment (Venmo links, etc.)?
- Web app or native? If it requires installing an app, the conversion rate at a restaurant is basically zero — nobody does it mid-dinner.
- What's the hook that makes someone use THIS over just opening Notes.app and doing the math?

---

## Suggestions

- **Make it a web app, shareable via QR code or link.** One person opens it, generates a link, everyone at the table scans the QR code and claims their items from their own phone. That's the actual differentiating UX.
- **Add a shared dish split feature immediately.** It's table stakes (pun intended). "Split this item N ways" is a must-have.
- **Tip on subtotal only, with a clear toggle.** Show the math. People care.
- **Output a clean summary people can screenshot and share.** "Alex owes $23.40, Brianna owes $31.10" — clean, copy-able, shareable.
- **Keep the input fast.** Keyboard shortcut to add a dish, autofocus fields, no modals. Speed is the feature.
- **Consider a tax-inclusive mode** for countries/states where tax is included in the price.

---

## Solo Dev Reality Check

- **Can one person ship this in 2–4 weeks with AI coding tools?** YES — the math is trivial, the UI is the whole challenge. A polished, fast single-page app is very achievable. If you add the QR-code/multi-phone session feature, add another week but still doable.
- **Biggest solo complexity traps:**
  - Real-time sync across multiple phones (if you go the shared-session route) — websockets or polling, easy to underestimate
  - Shared dish splitting UX — designing this intuitively is harder than coding it
  - Edge cases in the math (rounding, tax, included gratuity) — small bugs that make users distrust the app entirely
  - Getting people to actually use it mid-dinner — the app needs to load instantly and be self-explanatory with zero onboarding

---

## Design Handoff

### Screens needed
1. **Home / Setup screen** — enter number of people (or add names), option to add dishes manually or enter total bill
2. **Dish entry screen** — add dish name + price, assign to one or more people (multi-select for shared dishes)
3. **Per-person assignment view** — each person can review/claim their items (could be same screen as dish entry, toggled by person)
4. **Tip & tax screen** — enter tax amount or percentage, choose tip percentage, toggle tip on pre/post-tax subtotal
5. **Summary / Results screen** — each person's subtotal, their share of tax and tip, total owed; shareable screenshot or link
6. **Shared session view (optional but recommended)** — QR code or link for others to join and claim their own items

### Key UI interactions
- Add a dish (name + price) quickly — inline form, not a modal
- Assign a dish to one person or multiple (for shared items)
- Swipe or tap to switch between "view by dish" and "view by person"
- Adjust tip percentage with a slider or preset buttons (15%, 18%, 20%, custom)
- Share results screen as image or copy text summary
- QR code generation for multi-device session (if built)

### Data each screen needs
- **Setup:** number of people, names (optional)
- **Dish entry:** list of dishes (name, price, assigned people[]), running subtotal per person
- **Tip & tax:** subtotal per person, tax amount, tip percentage, tip base (pre/post-tax toggle)
- **Summary:** per-person breakdown (items, tax share, tip share, total), overall bill total, payment handles (optional Venmo deep links)
