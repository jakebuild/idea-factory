Verdict: WORTH BUILDING
Score: 8/10

What's Actually Good:
- The pivot from "marketplace" to "free curation tool first" is the right call — it's a classic audience-before-monetization move and eliminates 80% of the legal/technical complexity from v1
- Seeding with 822 existing screenshots solves the cold-start problem that kills most content marketplaces; day-one content is a real advantage
- The "Open in Stitch" deep-link is a concrete, differentiated CTA that no inspiration site offers — it's the actual moat and it's technically achievable without a formal partnership
- Scoping out payments, Stripe Connect, designer uploads, and licensing is mature thinking; the v2 monetization path ($9 unlock) is plausible and clean
- The BUILD queue as a personal curation layer is genuinely useful and different from Dribbble's "like and forget" pattern
- Targeting solo devs / vibe coders specifically is a smart audience narrowing — these people are active on Twitter/X, Product Hunt, and Indie Hackers, making distribution tractable for a solo builder
- The "Request Full Specs" demand-signal button is a clever pre-validation mechanic for v2 — you're selling before you build

Brutal Feedback:
- "133 app concepts from 822 screenshots" needs to actually exist before you write a line of code. If those 822 screenshots are a folder of loose PNGs with no metadata, turning them into structured cards (name, category, platform, screen count, description) is a 2-week manual content job, not a technical task. This is the real risk, not the swipe UI.
- The Stitch deep-link is the whole moat claim — but if Stitch doesn't support a public "open project by ID" URL without auth, this feature vaporizes. You need to verify this exists TODAY before building around it, not after.
- "Fully anonymous swipe with optional account to save BUILD queue" is the right UX call but it means your demand signal data (Request Full Specs clicks) is anonymous noise unless you gate saving behind a lightweight auth. You'll have no way to contact your most engaged users for v2 beta unless you capture emails somewhere.
- The "Built With This" showcase is social proof in theory but requires users to actually build something and come back to report it. That's a high-friction behavior loop that will be empty for months. Don't design it as a primary screen — it's a future feature masquerading as MVP scope.
- Web-first is fine, but the swipe mechanic is deeply mobile-native. On desktop with keyboard shortcuts it'll feel okay. On mobile web, swipe gestures on a browser are notoriously janky (conflicts with scroll, browser back gesture on iOS). If most of your target audience is on mobile, this is a real UX tax.
- "1-2 weeks" estimate is optimistic if the 822-screenshot content work is unsolved. The actual app build is 1 week. The content structuring is another 1-2 weeks on top. Call it 3 weeks total to be honest.
- There's still no answer to: why would solo devs come back after they've swiped through all 133 cards? The feed is finite. Without new content, the retention loop breaks immediately after the first session. You need a plan for content freshness or the DAU curve dies in week 2.

Key Questions:
- Are the 822 screenshots already organized by app concept with metadata, or is this raw unstructured image data that needs manual curation?
- Does Stitch have a publicly accessible "open project by ID" deep-link URL that works without the user being logged into Stitch?
- What's the content refresh plan — how often do new designs get added, and who does the curation work?
- Will you require email/account to save the BUILD queue, or is it localStorage-only? (This changes your ability to re-engage users for v2)
- Where does this live — a standalone web app, or is it part of an existing product/domain?

Suggestions:
- Before writing code, spend 2 days auditing the 822 screenshots: count how many distinct app concepts you actually have, what metadata exists, and what needs to be manually created. This defines your real scope.
- Verify the Stitch deep-link capability with a 10-minute test before building a single line of UI around it.
- Make the BUILD queue require an email to save (just email, no password — magic link or "email me my queue" pattern). This gives you a re-engagement list for v2 without friction.
- Add a "New this week" section once you have a content pipeline — even 3-5 new designs per week gives return visitors a reason to come back.
- Lean into the vibe coder community for launch: post on Indie Hackers and X with a demo GIF of the swipe mechanic. The mechanic is inherently shareable.
- Cut the "Built With This" showcase from v1 entirely — it will be empty and make the app look dead. Save it for when you have 5+ real examples.
- Consider a "shuffle" or "surprise me" button for when users exhaust the feed — keeps them in the loop without requiring new content.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — The swipe UI + BUILD queue is genuinely 1 week with AI coding tools. The real wildcard is content: if the 822 screenshots are already structured, you're golden. If they're raw PNGs, add 1-2 weeks of content work. Total: 2-3 weeks if content is clean, 4+ weeks if it needs manual curation.
- Biggest solo complexity traps:
  - Content structuring: 822 raw screenshots → 133 structured app concept cards with metadata is potentially the most time-consuming part of the project and it's not a coding task
  - Mobile swipe UX on web: getting swipe gestures right on mobile browsers (especially iOS Safari) without conflicts is fiddlier than it looks — use a library like react-spring or framer-motion with tested touch handlers, don't roll your own
  - Stitch integration dependency: the whole differentiator collapses if the deep-link doesn't work; have a fallback (screenshot download? Figma link?) ready
  - Retention without a content pipeline: a finite 133-card feed with no refresh plan turns this into a one-session app; you need a content update plan before launch, not after
  - Auth scope creep: anonymous → optional email → full auth is a slippery slope that can eat a week if you're not strict about v1 scope

Design Handoff:
- Screens needed:
  1. Swipe Feed (Home) — Full-screen card stack, one card visible at a time. Card shows: hero mockup image (full bleed), app name overlay at bottom, category badge, platform tag (iOS/Android/Web), screen count, one-sentence description. Swipe right = BUILD (green glow), swipe left = DROP (red glow). Bottom nav: Feed / Queue / Profile. "Filter" icon in top-right corner.
  2. Design Detail View — Full-screen modal/page triggered by tapping card center. Top: back arrow. Hero area: horizontal scrollable carousel of all mockup screens (swipe through screens). Below carousel: app name (large), category chip, platform chip, screen count, 2-3 sentence description. Action row: "Add to BUILD Queue" (primary CTA, filled button) + "Open in Stitch" (secondary, only shown if Stitch ID exists) + "Request Full Specs" (tertiary, text button with checkmark state).
  3. BUILD Queue — List view of saved designs. Header: "Your BUILD Queue (N)". Each row: small thumbnail, app name, category, date saved, status badge (Saved / Opened in Stitch / Built). Tap row → opens Detail View. "Mark as Built" action available per item. Empty state: illustration + "You haven't saved anything yet — go swipe."
  4. Filter / Browse — Sheet or overlay panel. Filter chips: Category (Social, Productivity, Finance, Health, E-commerce, Tools, Other), Platform (iOS / Android / Web / All), Sort (Most Swiped / Newest / Most Built). "Apply Filters" button. "Clear All" link. Shows result count as filters are selected.
  5. Onboarding (first launch, 3 slides) — Slide 1: Hero animation of swipe gesture + tagline "Swipe through app designs. Build what you love." Slide 2: Show the BUILD queue concept — "Save ideas to your BUILD queue." Slide 3: Show Stitch integration — "Jump straight into building." Skip button prominent throughout. "Get Started" on final slide.
  6. Empty States — Three variants: (a) BUILD queue empty: illustration, "Nothing saved yet", "Go Swipe" CTA button. (b) Feed exhausted: "You've seen everything!", show BUILD queue count, "View Your Queue" CTA. (c) Filter returns zero results: "No designs match this filter", "Clear Filters" CTA.
  7. Profile / Settings — Display name (editable), BUILD queue count stat, Built count stat. Settings section: notification toggle, "Clear BUILD queue" (destructive, confirm modal), Sign out. Minimal — no social features.
  8. "Mark as Built" Modal — Triggered from BUILD queue item. Fields: URL of what you built (required), optional screenshot upload, optional display name to credit. Submit button. This posts to the future Showcase — but the submission form is in v1 even if the public showcase wall is not.

- Key UI interactions:
  - Swipe right on card → card flies off right with green BUILD flash, next card slides up, item silently added to queue
  - Swipe left on card → card flies off left with red DROP flash, next card slides up
  - Tap card center → expands to Detail View (smooth push transition, not modal)
  - "Add to BUILD Queue" in Detail → button state changes to "Saved ✓", item added to queue
  - "Open in Stitch" → opens in new tab, button briefly shows loading state, logs event
  - "Request Full Specs" → button changes to "Requested ✓" (persisted per session/user), logs demand signal
  - Filter applied → feed resets to filtered card stack, filter icon shows active indicator (dot)
  - Feed exhausted → special end-of-feed card with queue summary and CTA
  - "Mark as Built" submit → success toast, item status updates to "Built" in queue

- Data each screen needs:
  - Swipe Feed: array of DesignCard objects (id, title, category, platform, screenCount, description, heroImageUrl, stitchProjectId?, screens[]), current index, user's already-seen IDs (to skip), active filters
  - Design Detail: single DesignCard with full screens array (imageUrl, order), user's queue status for this card (saved/not saved)
  - BUILD Queue: user's saved designs array with status per item (saved/opened/built), sorted by date saved desc
  - Filter Panel: current filter state (category[], platform, sortBy), result count preview per filter combination
  - Onboarding: completion flag (localStorage — don't show again after first time)
  - Profile: user object (displayName, email), aggregate stats (queueCount, builtCount)
  - Mark as Built Modal: design card reference, form state (url, screenshot, displayName)
