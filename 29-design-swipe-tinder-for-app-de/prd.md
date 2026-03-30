# PRD: Design Swipe — Tinder for App Designs

## 1. Overview

Design Swipe is a swipe-to-save inspiration tool for solo developers and vibe coders. Users browse full-screen app design cards, swipe right to add concepts to a personal BUILD queue, and swipe left to pass. The feed is seeded with 133 curated app concepts drawn from an existing 822-screenshot database. The core differentiator over Dribbble/Behance is that each card is framed as a buildable app concept — and cards with a linked Stitch project surface a one-click "Open in Stitch" button that deep-links directly into a working prototype. v1 is a free personal curation tool; monetization ($9 Stitch export unlock, designer revenue share) is deferred to v2.

---

## 2. Target User

Solo developers, indie hackers, and vibe coders who want fast design inspiration for apps they can actually build. Their pain: Dribbble and Behance surface beautiful work with no path from "I like this" to "I can build this." Design Swipe closes that gap by framing every card as a buildable concept and providing a concrete handoff via Stitch deep-link, turning passive browsing into an actionable BUILD queue.

---

## 3. Tech Stack

- **Platform:** Web (responsive, mobile-optimized)
- **Language + Framework:** TypeScript + Next.js 14 (App Router)
- **Key libraries:**
  - `framer-motion` — swipe gesture handling and card animation (tested touch handlers for iOS Safari; do not roll custom touch logic)
  - `@supabase/supabase-js` — auth (magic link / email-only), Postgres storage, row-level security
  - `zustand` — client-side feed state, filter state, BUILD queue optimistic updates
  - `@tanstack/react-query` — server data fetching, cache invalidation
  - `shadcn/ui` + Tailwind CSS — UI components
- **Hosting / Deployment:** Vercel (zero-config Next.js deploy, edge functions for demand-signal logging)
- **Why this fits:** Next.js + Supabase is the fastest credible full-stack path for a solo dev — magic-link auth eliminates password flows, Supabase Postgres handles relational data with no backend boilerplate, Vercel deploys in seconds. Framer Motion's drag gesture API handles swipe conflicts on mobile browsers out of the box. Total setup to first swipe: under a day.

---

## 4. V1 Scope

- **Required v1** — Swipe feed with full-screen design cards (swipe left/right, keyboard arrow shortcuts)
- **Required v1** — Design detail view with horizontal screen carousel, app metadata, and action buttons
- **Required v1** — BUILD queue: persistent list of right-swiped designs per user
- **Required v1** — "Open in Stitch" deep-link button (shown only when `stitchProjectId` is present)
- **Required v1** — "Request Full Specs" button with `Requested ✓` persisted state per user per design
- **Required v1** — Filter panel: filter by category, platform; sort by Most Swiped / Newest / Most Built
- **Required v1** — Onboarding flow (3 slides, shown once on first visit, skippable)
- **Required v1** — Magic-link email auth (required to persist BUILD queue; anonymous browsing allowed up to queue save attempt)
- **Required v1** — "Mark as Built" modal: URL + optional display name; submission stored in DB
- **Required v1** — Seed content: 133 app concept cards in DB with all required fields populated
- **Required v1** — Empty states for BUILD queue, exhausted feed, and zero filter results
- **Required v1** — Profile / Settings screen: display name, queue stats, clear queue, sign out
- **Optional polish** — "New this week" label on feed cards added within last 7 days
- **Optional polish** — Feed end card with BUILD queue summary and "View Queue" CTA
- **Optional polish** — Shuffle / "Surprise me" button when feed is exhausted

---

## 5. Out of Scope

- Designer self-serve uploads (invite-only DB seeding only)
- Payments, Stripe Connect, designer payouts, revenue share
- Licensing terms and IP enforcement
- Export to Figma, React code, or image packs
- Full-text search
- Team / shared queues
- Public "Built With This" showcase wall (submissions stored in DB, wall not surfaced in v1)
- Native iOS / Android app
- Social profiles, follows, or activity feeds
- Push / email notifications
- Content moderation tooling

---

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Swipe Feed (Home) | Required v1 |
| 2 | Design Detail View | Required v1 |
| 3 | BUILD Queue | Required v1 |
| 4 | Filter Panel | Required v1 |
| 5 | Onboarding | Required v1 |
| 6 | Profile / Settings | Required v1 |
| 7 | Mark as Built Modal | Required v1 |

> **Empty states** are conditional states of Screens 1 and 3, not standalone screens. See Section 7.

---

## 7. State Model

### Screen 1: Swipe Feed

| State | Trigger | UI | Actions available |
|---|---|---|---|
| **Loading** | Initial page load | Skeleton card placeholder | None |
| **Active feed** | Cards remain in filtered set | Full-screen card stack; top card interactive | Swipe right, swipe left, tap to expand, tap filter icon |
| **Feed exhausted** | `currentIndex` === total filtered cards | End-of-feed card: "You've seen everything!", BUILD queue count, "View Your Queue" CTA | Navigate to Queue, tap shuffle (optional polish) |
| **Feed exhausted (no queue)** | Feed exhausted AND `queueCount === 0` | End-of-feed card with "Start building — your queue is empty" | Navigate to Queue |
| **Filter active** | Any filter chip selected | Filter icon shows active dot; feed reset to filtered subset | Clear filters, swipe, tap |
| **Filter zero results** | Applied filters return 0 cards | Inline message: "No designs match this filter" + "Clear Filters" CTA | Clear filters |
| **Unauthenticated swipe-right** | User swipes right but has no session | Auth prompt sheet: "Save your queue — enter your email" | Submit email (magic link), dismiss (loses save) |

### Screen 2: Design Detail View

| State | Trigger | UI | Actions available |
|---|---|---|---|
| **Loading** | Transition from feed before data resolves | Skeleton carousel + skeleton metadata | Back |
| **Default** | Design loaded, user has not saved | "Add to BUILD Queue" (primary), "Open in Stitch" (if `stitchProjectId` present), "Request Full Specs" (text button) | Add to queue, open Stitch, request specs, scroll carousel, back |
| **Saved** | `QueueItem` row exists for this design + user | "Add to BUILD Queue" → "Saved ✓" (disabled) | Open Stitch, request specs, scroll carousel, back |
| **Specs requested** | `SpecRequest` row exists for this design + user | "Request Full Specs" → "Requested ✓" (disabled) | Other actions still available |
| **No Stitch project** | `stitchProjectId` is null | "Open in Stitch" button hidden | Add to queue, request specs |

### Screen 3: BUILD Queue

| State | Trigger | UI | Actions available |
|---|---|---|---|
| **Loading** | Queue page load | Skeleton list rows | None |
| **Populated** | `QueueItem[]` for user, count > 0 | Header "Your BUILD Queue (N)", list of items with status badge | Tap item → Detail View, tap "Mark as Built" |
| **Empty** | `QueueItem[]` count === 0 | Illustration + "You haven't saved anything yet" + "Go Swipe" CTA button | Navigate to Feed |
| **Item status: Saved** | Default after swipe-right | Badge: `Saved` | Mark as Built |
| **Item status: Opened in Stitch** | User clicked "Open in Stitch" for this item | Badge: `Opened in Stitch` | Mark as Built |
| **Item status: Built** | `builtSubmission` exists for item | Badge: `Built` | View submission |

### Screen 4: Filter Panel

| State | Trigger | UI | Actions available |
|---|---|---|---|
| **Closed** | Default | Hidden | Tap filter icon in Screen 1 |
| **Open (no selection)** | Filter icon tapped | Sheet with all chips unselected, sort = "Most Swiped", result count = total | Select chips, apply, clear |
| **Open (selection active)** | Any chip selected | Selected chips highlighted, result count updates live | Apply, clear all |

### Screen 5: Onboarding

| State | Trigger | UI | Actions available |
|---|---|---|---|
| **Slide 1** | First app launch (`onboardingComplete` not in localStorage) | Hero swipe animation + tagline | Next, Skip |
| **Slide 2** | After slide 1 | BUILD queue concept illustration | Next, Skip |
| **Slide 3** | After slide 2 | Stitch integration illustration | Get Started |
| **Complete** | "Get Started" or "Skip" tapped | Onboarding dismissed, feed shown; `dsw_onboarding_complete = 'true'` written to localStorage | — |

### Screen 6: Profile / Settings

| State | Trigger | UI | Actions available |
|---|---|---|---|
| **Authenticated** | Valid session | Display name (editable), queue count, built count, settings section | Edit name, clear queue (confirm modal), sign out |
| **Clear queue confirm** | "Clear BUILD queue" tapped | Confirmation modal: "This will remove all N saved designs" | Confirm (deletes all `QueueItem` rows for user), Cancel |
| **Unauthenticated** | No session | Prompt to sign in with email | Submit email |

### Screen 7: Mark as Built Modal

| State | Trigger | UI | Actions available |
|---|---|---|---|
| **Open** | "Mark as Built" tapped on queue item | URL field (required), display name field (optional), Submit | Submit, Dismiss |
| **Submitting** | Submit tapped | Button shows loading state | None (debounced) |
| **Success** | Submission saved | Success toast, modal closes, queue item status → `Built` | — |
| **Validation error** | URL field empty or invalid | Inline error under URL field | Fix and resubmit |

---

## 8. Data Model

### Entity: `DesignCard`
| Field | Type | Description |
|---|---|---|
| `id` | `uuid` | Primary key |
| `title` | `text` | App concept name |
| `category` | `enum` | One of: `Social`, `Productivity`, `Finance`, `Health`, `E-commerce`, `Tools`, `Other` |
| `platform` | `enum` | One of: `iOS`, `Android`, `Web` |
| `screenCount` | `integer` | Number of mockup screens in this concept |
| `description` | `text` | 2–3 sentence description |
| `heroImageUrl` | `text` | URL of primary card thumbnail |
| `screens` | `jsonb` | Array of `{ order: integer, imageUrl: text }` |
| `stitchProjectId` | `text \| null` | Stitch project ID; null if no linked project |
| `swipeRightCount` | `integer` | Total right-swipes across all users (for "Most Swiped" sort) |
| `builtCount` | `integer` | Total built submissions linked to this card |
| `publishedAt` | `timestamptz` | When added to DB (for "Newest" sort and "New this week" label) |
| `isActive` | `boolean` | Soft-delete / curation flag |

### Entity: `User`
| Field | Type | Description |
|---|---|---|
| `id` | `uuid` | Primary key (matches Supabase auth UID) |
| `email` | `text` | Auth identifier |
| `displayName` | `text \| null` | User-set display name |
| `createdAt` | `timestamptz` | Account creation timestamp |

> `onboardingComplete` is `localStorage`-only (key: `dsw_onboarding_complete`). It does not require a session and is not stored in the DB.

### Entity: `QueueItem`
| Field | Type | Description |
|---|---|---|
| `id` | `uuid` | Primary key |
| `userId` | `uuid` | FK → `User.id` |
| `designCardId` | `uuid` | FK → `DesignCard.id` |
| `status` | `enum` | One of: `Saved`, `OpenedInStitch`, `Built` |
| `savedAt` | `timestamptz` | When item was added to queue |

- **Unique constraint:** `(userId, designCardId)` — one entry per user per design
- **Relationships:** belongs to `User`, belongs to `DesignCard`

### Entity: `SwipeEvent`
| Field | Type | Description |
|---|---|---|
| `id` | `uuid` | Primary key |
| `userId` | `uuid \| null` | FK → `User.id`; null if anonymous |
| `designCardId` | `uuid` | FK → `DesignCard.id` |
| `direction` | `enum` | `right` or `left` |
| `createdAt` | `timestamptz` | Event timestamp |

> Used to compute `DesignCard.swipeRightCount` (via DB trigger or scheduled aggregation).

### Entity: `SpecRequest`
| Field | Type | Description |
|---|---|---|
| `id` | `uuid` | Primary key |
| `userId` | `uuid \| null` | FK → `User.id`; null if anonymous |
| `designCardId` | `uuid` | FK → `DesignCard.id` |
| `createdAt` | `timestamptz` | Request timestamp |

- **Unique constraint:** `(userId, designCardId)` — enforced for authenticated users only; anonymous requests always insert
- **Purpose:** demand signal for v2 paid tier; drives "Request Full Specs" → "Requested ✓" persisted state per authenticated user

### Entity: `StitchOpenEvent`
| Field | Type | Description |
|---|---|---|
| `id` | `uuid` | Primary key |
| `userId` | `uuid \| null` | FK → `User.id` |
| `designCardId` | `uuid` | FK → `DesignCard.id` |
| `createdAt` | `timestamptz` | Event timestamp |

> Inserting a `StitchOpenEvent` also updates `QueueItem.status` → `OpenedInStitch` if a `QueueItem` exists for this user + design.

### Entity: `BuiltSubmission`
| Field | Type | Description |
|---|---|---|
| `id` | `uuid` | Primary key |
| `userId` | `uuid \| null` | FK → `User.id` |
| `designCardId` | `uuid` | FK → `DesignCard.id` |
| `builtUrl` | `text` | URL of what the user built (required) |
| `displayName` | `text \| null` | Optional credit name |
| `createdAt` | `timestamptz` | Submission timestamp |

- Inserting a `BuiltSubmission` updates `QueueItem.status` → `Built` (if queue item exists) and increments `DesignCard.builtCount`

---

## 9. Screens & Acceptance Criteria

### Screen 1: Swipe Feed (Home) [Required v1]

**Purpose:** Core swipe loop — users browse full-screen design cards and add to BUILD queue or pass.

**Visual elements:**
- Full-screen card (fills viewport): hero mockup image (full bleed), gradient overlay at bottom, app title, category badge, platform chip, screen count, one-sentence description
- Bottom navigation bar: Feed / Queue / Profile tabs
- Filter icon (top-right corner); shows active indicator dot when any filter is applied
- Active card is interactive; next card visible behind it at reduced scale

**User actions:**
- Swipe card right → BUILD
- Swipe card left → DROP
- Press `→` arrow key → BUILD (keyboard shortcut)
- Press `←` arrow key → DROP (keyboard shortcut)
- Tap card center → navigate to Screen 2 (Design Detail)
- Tap filter icon → open Screen 4 (Filter Panel)
- Tap bottom nav tabs → navigate to Screen 3 or Screen 6

**State changes:**
- Swipe right: `SwipeEvent { direction: right }` inserted; if authenticated, `QueueItem` upserted with `status: Saved`; `DesignCard.swipeRightCount` incremented; card animates off-right with green BUILD flash; next card becomes active
- Swipe right (unauthenticated): auth prompt sheet shown; if user completes magic link, `QueueItem` saved retroactively for the pending design
- Swipe left: `SwipeEvent { direction: left }` inserted; card animates off-left with red DROP flash; next card becomes active
- Feed exhausted: end-of-feed card rendered when `currentIndex === filteredCards.length`

**Acceptance criteria:**
- [ ] Swiping right on a card inserts a `SwipeEvent` with `direction: right` and a `QueueItem` row for the authenticated user
- [ ] Swiping left on a card inserts a `SwipeEvent` with `direction: left` and does NOT create a `QueueItem`
- [ ] Pressing `→` arrow key has identical state changes to swipe right
- [ ] Pressing `←` arrow key has identical state changes to swipe left
- [ ] Cards already present in the user's `QueueItem` table are excluded from the feed (not reshown)
- [ ] Filter icon shows an active dot indicator when any filter chip has a non-default value
- [ ] Applying a filter resets `currentIndex` to 0 on the filtered subset
- [ ] An unauthenticated user who swipes right is shown an auth prompt; completing magic link saves the pending `QueueItem`
- [ ] `DesignCard.swipeRightCount` increments by 1 on each right-swipe event
- [ ] When `currentIndex === filteredCards.length`, the end-of-feed card is shown (not a blank screen or error)

**Conditional states:**
- **Loading:** skeleton card placeholder shown until first card data resolves
- **Feed exhausted:** end-of-feed card with BUILD queue count and "View Your Queue" CTA; if `queueCount === 0`, CTA copy is "Start building — your queue is empty"
- **Filter zero results:** inline message "No designs match this filter" with "Clear Filters" CTA; swipe UI not rendered

---

### Screen 2: Design Detail View [Required v1]

**Purpose:** Show all screens in the app concept and provide the three action CTAs.

**Visual elements:**
- Back arrow (top-left) returning to Screen 1
- Horizontal scrollable carousel showing all `DesignCard.screens` ordered by `screens[].order`
- Below carousel: app title (large), category chip, platform chip, screen count, 2–3 sentence description
- Action row: "Add to BUILD Queue" (primary filled button) | "Open in Stitch" (secondary, hidden if `stitchProjectId` is null) | "Request Full Specs" (tertiary text button)

**User actions:**
- Scroll carousel horizontally → view additional screens
- Tap "Add to BUILD Queue" → save to queue
- Tap "Open in Stitch" → open Stitch project in new tab
- Tap "Request Full Specs" → log demand signal
- Tap back arrow → return to Screen 1

**State changes:**
- "Add to BUILD Queue" tapped: `QueueItem` upserted with `status: Saved`; button label changes to "Saved ✓" (disabled); no navigation change
- "Open in Stitch" tapped: `StitchOpenEvent` inserted; `QueueItem.status` updated to `OpenedInStitch` if row exists; Stitch URL opened in new tab; button briefly shows loading spinner
- "Request Full Specs" tapped: `SpecRequest` row inserted; button label changes to "Requested ✓" (disabled); state sourced from DB for authenticated users on next load

**Acceptance criteria:**
- [ ] Navigating back from Screen 2 returns to the previously viewed card index in Screen 1 (not card index 0)
- [ ] "Open in Stitch" button is not rendered when `DesignCard.stitchProjectId` is null
- [ ] "Open in Stitch" button opens the verified Stitch URL (format confirmed before build per Section 11) in a new tab when `stitchProjectId` is present
- [ ] Tapping "Add to BUILD Queue" upserts a `QueueItem` row and changes button label to "Saved ✓" without page reload
- [ ] "Saved ✓" state is rendered on load if a `QueueItem` row exists for this user + design
- [ ] Tapping "Request Full Specs" inserts a `SpecRequest` row and changes button label to "Requested ✓" without page reload
- [ ] "Requested ✓" state is rendered on load for authenticated users if a `SpecRequest` row exists for this user + design (sourced from DB, not session state only)
- [ ] Carousel renders all `screens` entries ordered by `screens[].order`

**Conditional states:**
- **Loading:** skeleton carousel and skeleton metadata shown while data fetches
- **Saved:** "Add to BUILD Queue" renders as "Saved ✓" (disabled) on initial load if `QueueItem` exists
- **Specs requested:** "Request Full Specs" renders as "Requested ✓" (disabled) on initial load if `SpecRequest` exists

---

### Screen 3: BUILD Queue [Required v1]

**Purpose:** Persistent list of right-swiped designs with per-item status tracking.

**Visual elements:**
- Header: "Your BUILD Queue (N)" where N = `QueueItem` count for authenticated user
- List of items sorted by `savedAt` descending: small thumbnail, app name, category, formatted date saved, status badge
- Status badge values: `Saved` | `Opened in Stitch` | `Built`
- Per-item "Mark as Built" action (available on items with status `Saved` or `Opened in Stitch`)

**User actions:**
- Tap list item → navigate to Screen 2 (Design Detail View)
- Tap "Mark as Built" → open Screen 7 (Mark as Built Modal)

**State changes:**
- Navigating to Screen 2 from queue: back arrow in Screen 2 returns to Screen 3 (not Screen 1)
- "Mark as Built" submitted via Screen 7: `QueueItem.status` → `Built`; status badge updates to `Built`

**Acceptance criteria:**
- [ ] Queue displays all `QueueItem` rows for the authenticated user, sorted by `savedAt` descending
- [ ] Header count N reflects current `QueueItem` count and updates immediately after a new item is added (no reload required)
- [ ] Status badge reflects current `QueueItem.status` value (`Saved`, `OpenedInStitch` displayed as "Opened in Stitch", `Built`)
- [ ] Tapping a queue item opens Screen 2 with that design; back arrow from Screen 2 returns to Screen 3
- [ ] "Mark as Built" action is available on items with `status: Saved` or `status: OpenedInStitch`
- [ ] "Mark as Built" action is not available on items with `status: Built`

**Conditional states:**
- **Empty:** shown when `QueueItem` count === 0; renders illustration, "You haven't saved anything yet", "Go Swipe" CTA navigating to Screen 1
- **Loading:** skeleton list rows shown while data fetches

---

### Screen 4: Filter Panel [Required v1]

**Purpose:** Allow users to filter the feed by category, platform, and sort order before swiping.

**Visual elements:**
- Bottom sheet overlay
- Category filter chips (multi-select): `Social`, `Productivity`, `Finance`, `Health`, `E-commerce`, `Tools`, `Other`
- Platform filter chips (single-select): `iOS`, `Android`, `Web`, `All` (default: `All`)
- Sort chips (single-select): `Most Swiped`, `Newest`, `Most Built` (default: `Most Swiped`)
- Live result count: "N designs match" updates as chips are toggled
- "Apply Filters" primary button; "Clear All" text link

**User actions:**
- Select / deselect category chips → update live count
- Select platform chip → update live count
- Select sort chip → update live count
- Tap "Apply Filters" → close panel, reset feed to filtered + sorted card stack
- Tap "Clear All" → reset all chips to defaults

**State changes:**
- "Apply Filters" tapped: filter state persisted in zustand store; feed `currentIndex` reset to 0; filter icon in Screen 1 shows active dot if any non-default filter is active
- Panel dismissed without applying (tap outside / swipe down): no change to active feed filters

**Acceptance criteria:**
- [ ] Category chip labels and values match exactly the `DesignCard.category` enum values: `Social`, `Productivity`, `Finance`, `Health`, `E-commerce`, `Tools`, `Other`
- [ ] Platform chip values match exactly the `DesignCard.platform` enum values: `iOS`, `Android`, `Web` (plus `All` meaning no platform filter)
- [ ] Sort options map to: `Most Swiped` → `swipeRightCount` desc, `Newest` → `publishedAt` desc, `Most Built` → `builtCount` desc
- [ ] Live result count updates without full page reload as chips are toggled
- [ ] "Apply Filters" closes the panel and resets feed `currentIndex` to 0
- [ ] "Clear All" resets: category → none selected, platform → `All`, sort → `Most Swiped`
- [ ] Dismissing without applying (tap outside or swipe down) makes no change to the active feed filter state

---

### Screen 5: Onboarding [Required v1]

**Purpose:** First-launch orientation shown once; introduces swipe mechanic, BUILD queue, and Stitch integration.

**Visual elements:**
- Slide 1: Hero swipe gesture animation + tagline "Swipe through app designs. Build what you love."
- Slide 2: BUILD queue illustration + "Save ideas to your BUILD queue."
- Slide 3: Stitch integration illustration + "Jump straight into building."
- Skip button prominent on Slides 1 and 2
- "Get Started" button on Slide 3 only
- Progress dots indicating current slide

**User actions:**
- Tap "Next" / swipe horizontally → advance to next slide
- Tap "Skip" (Slides 1–2) → dismiss onboarding
- Tap "Get Started" (Slide 3) → dismiss onboarding

**State changes:**
- Any dismissal: `localStorage.setItem('dsw_onboarding_complete', 'true')`; navigate to Screen 1

**Acceptance criteria:**
- [ ] Onboarding is shown on first app visit when `localStorage.getItem('dsw_onboarding_complete')` is null or absent
- [ ] Onboarding is NOT shown on subsequent visits when `dsw_onboarding_complete` is `'true'` in localStorage
- [ ] "Skip" on Slide 1 or Slide 2 sets `dsw_onboarding_complete` and navigates to Screen 1
- [ ] "Get Started" on Slide 3 sets `dsw_onboarding_complete` and navigates to Screen 1

---

### Screen 6: Profile / Settings [Required v1]

**Purpose:** Minimal account and queue management; not a social profile.

**Visual elements:**
- Display name field (editable inline or via edit icon)
- Stats: "BUILD queue: N" (count of `QueueItem` rows) and "Built: M" (count of `QueueItem` rows with `status: Built`)
- Settings section: "Clear BUILD queue" (destructive), "Sign out"
- Notification toggle: UI rendered, no notification infra behind it in v1 (toggle state stored in localStorage only)

**User actions:**
- Edit display name → save to `User.displayName`
- Tap "Clear BUILD queue" → show confirmation modal
- Confirm clear → delete all `QueueItem` rows for this user
- Tap "Sign out" → end Supabase session, redirect to Screen 1 as anonymous

**State changes:**
- Display name saved: `User.displayName` updated in DB
- Clear confirmed: all `QueueItem` rows for `userId` hard-deleted; queue count → 0
- Sign out: session cleared; user returned to Screen 1 in unauthenticated state

**Acceptance criteria:**
- [ ] Display name edit saves to `User.displayName` and reflects on reload without requiring sign out
- [ ] Confirmation modal displays current queue count: "This will remove all N saved designs"
- [ ] Confirming clear hard-deletes all `QueueItem` rows for the authenticated user; Screen 3 queue count updates to 0
- [ ] Cancelling the confirm modal makes no DB changes
- [ ] "Sign out" clears the Supabase session and Screen 1 renders in unauthenticated state

**Conditional states:**
- **Unauthenticated:** sign-in prompt shown instead of profile content; "Clear BUILD queue" and "Sign out" not rendered

---

### Screen 7: Mark as Built Modal [Required v1]

**Purpose:** Collect built-app submission; data stored in DB for future showcase wall (out of scope v1).

**Visual elements:**
- Modal overlay triggered from Screen 3 queue item
- URL field labeled "Link to what you built" (required)
- Display name field labeled "Your name (optional)"
- "Submit" primary button; "Cancel" / dismiss (X)

**User actions:**
- Enter URL
- Enter display name (optional)
- Tap "Submit" → validate and save
- Tap "Cancel" / dismiss → close with no state change

**State changes:**
- Submit success: `BuiltSubmission` row created with `builtUrl`, `designCardId`, `userId`; `QueueItem.status` → `Built`; `DesignCard.builtCount` incremented; success toast shown; modal closed
- Submit error: inline error under URL field; modal stays open

**Acceptance criteria:**
- [ ] Submit is blocked and an inline error shown if URL field is empty or not a valid URL (must start with `http://` or `https://`)
- [ ] Successful submission creates a `BuiltSubmission` row with `builtUrl`, `designCardId`, and `userId` (if authenticated)
- [ ] After successful submission, the corresponding `QueueItem.status` updates to `Built` and the badge in Screen 3 reflects `Built` without page reload
- [ ] `DesignCard.builtCount` increments by 1 on successful submission
- [ ] Dismissing the modal without submitting makes no DB changes

---

## 10. External Integrations

**Service:** Supabase
- **Purpose:** PostgreSQL database, magic-link auth, row-level security
- **Specifics:** Magic link via `supabase.auth.signInWithOtp({ email })`. Row-level security policies must ensure users can only read/write their own `QueueItem`, `SpecRequest`, `SwipeEvent`, `StitchOpenEvent`, and `BuiltSubmission` rows. `DesignCard` is publicly readable (no auth required). Note: magic link emails sometimes land in spam; display a "Check your spam folder" note after sending.

**Service:** Stitch (Google)
- **Purpose:** Deep-link from design card to working prototype
- **Specifics:** URL format must be verified before build (see Section 11). Do not hardcode a URL format until confirmed. Known example from design metadata: `https://stitch.withgoogle.com/projects/15776618951775017939` — but whether this URL is publicly accessible without Stitch auth is unverified. No SDK or API key assumed — this is URL navigation only. `StitchOpenEvent` is logged via Supabase insert after click. If the deep-link requires Stitch auth, display a tooltip: "You may need to sign in to Stitch to open this project."

**Service:** Vercel
- **Purpose:** Hosting and CI/CD
- **Specifics:** Standard Next.js App Router deploy. Env vars required: `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY` (server-only for admin seed scripts).

---

## 11. Open Risks & Assumptions to Verify Before Build

- **Risk: 822 screenshots are unstructured raw images.** The entire seed content depends on 133 app concepts with `title`, `category`, `platform`, `screenCount`, `description`, `heroImageUrl`, and ordered `screens` array being populated in the DB. If the screenshots are a flat folder of PNGs with no metadata, this is a 1–2 week manual curation task before any UI ships. **What to verify:** Open the screenshots source. Count distinct app concepts, check what metadata exists, and estimate how much manual grouping and labeling is needed. Do this before writing a line of code.

- **Risk: Stitch deep-link format is unconfirmed.** "Open in Stitch" is the primary product differentiator. If Stitch does not support a public `open project by ID` URL without requiring the viewer to be a collaborator and authenticated, this feature fails silently. **What to verify:** Open a Stitch project, copy the URL, open it incognito without signing in, and confirm the behavior. Identify the exact URL format. Do this before building any Stitch-related UI. Fallback if it requires auth: show a tooltip warning rather than hiding the button.

- **Risk: Anonymous `SpecRequest` data has no re-engagement value.** If "Request Full Specs" is available to anonymous users, demand signals are uncontactable noise for v2. **What to verify:** Decide before build whether to gate "Request Full Specs" behind magic-link auth. The current data model allows anonymous inserts (`userId` nullable). Change `userId` to required on `SpecRequest` if email capture matters for v2 beta outreach.

- **Risk: Swipe gestures on iOS Safari conflict with browser navigation.** iOS Safari's edge-swipe for back navigation and pull-to-refresh will interfere with the card drag handler. **What to verify:** Prototype `framer-motion` drag gesture on a real iPhone in Safari before committing to the full card stack. Confirm `dragConstraints`, `dragElastic`, and `touchAction: 'none'` CSS settings eliminate the conflict.

- **Risk: No content refresh plan makes this a one-session product.** 133 cards is finite. A user who exhausts the feed has no reason to return. **What to verify:** Before launch, define who adds new cards, at what cadence, and how (admin DB script, simple CMS, or manual SQL). The "New this week" label (optional polish) is only meaningful if a content pipeline exists.

---

## 12. Visual Reference

- **Live prototype (static mock — visual layout reference only):** https://jakebuild.github.io/idea-factory/29-design-swipe-tinder-for-app-de/
- **Screen files on GitHub:** https://github.com/jakebuild/idea-factory/tree/gh-pages/29-design-swipe-tinder-for-app-de
- **Source idea:** https://github.com/jakebuild/idea-factory/blob/main/29-design-swipe-tinder-for-app-de/idea-v2.md
- **Source critique:** https://github.com/jakebuild/idea-factory/blob/main/29-design-swipe-tinder-for-app-de/critique-v2.md

> The prototype is a static visual mock. Treat it as layout reference only. All interaction behavior, state transitions, and data requirements are defined by this PRD. Where the prototype conflicts with this PRD, this PRD governs.

---

## 13. Suggested Folder Structure

```
design-swipe/
├── app/
│   ├── layout.tsx                  # Root layout with bottom nav
│   ├── page.tsx                    # Redirect to /feed
│   ├── feed/
│   │   └── page.tsx                # Screen 1: Swipe Feed
│   ├── design/
│   │   └── [id]/
│   │       └── page.tsx            # Screen 2: Design Detail View
│   ├── queue/
│   │   └── page.tsx                # Screen 3: BUILD Queue
│   └── profile/
│       └── page.tsx                # Screen 6: Profile / Settings
├── components/
│   ├── feed/
│   │   ├── SwipeCard.tsx           # Framer Motion drag card
│   │   ├── CardStack.tsx           # Card stack orchestrator
│   │   ├── SwipeFlash.tsx          # Green/red overlay flash
│   │   └── EndOfFeedCard.tsx       # Feed exhausted state
│   ├── detail/
│   │   ├── ScreenCarousel.tsx      # Horizontal screen scroll
│   │   └── ActionRow.tsx           # BUILD / Stitch / Request CTAs
│   ├── queue/
│   │   ├── QueueList.tsx
│   │   ├── QueueItem.tsx
│   │   └── MarkAsBuiltModal.tsx    # Screen 7
│   ├── filter/
│   │   └── FilterPanel.tsx         # Screen 4: bottom sheet
│   ├── onboarding/
│   │   └── OnboardingFlow.tsx      # Screen 5: 3-slide intro
│   └── ui/                         # shadcn/ui primitives
├── lib/
│   ├── supabase/
│   │   ├── client.ts               # Browser Supabase client
│   │   ├── server.ts               # Server Supabase client
│   │   └── schema.sql              # DB schema + enums + RLS policies
│   ├── store/
│   │   ├── feedStore.ts            # Zustand: cards, currentIndex, filters
│   │   └── queueStore.ts           # Zustand: optimistic queue updates
│   └── hooks/
│       ├── useSwipe.ts             # Keyboard + gesture unified hook
│       ├── useBuildQueue.ts        # Queue read/write
│       └── useOnboarding.ts        # localStorage flag
├── types/
│   └── index.ts                    # DesignCard, QueueItem, User, etc.
├── public/
│   └── onboarding/                 # Static onboarding illustration assets
├── .env.local                      # NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY
├── next.config.ts
├── tailwind.config.ts
└── package.json
```
