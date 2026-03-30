# PRD: Design Swipe — Tinder for App Designs

## 1. Overview

Design Swipe is a swipe-to-save inspiration tool for solo developers and vibe coders. Users swipe through full-screen app design concept cards — right to add to their personal BUILD queue, left to pass. The feed is seeded with 133 curated app concepts drawn from an existing 822-screenshot database. Each card shows the hero mockup, app metadata, and (where available) a one-click "Open in Stitch" button that deep-links directly into a working prototype. v1 is a free, web-first tool with no payments, no designer uploads, and no marketplace — just the core swipe-and-save loop with a concrete path to building.

---

## 2. Target User

Solo developers and indie hackers who build apps with AI coding tools and need design inspiration that's actionable, not just pretty. Their pain: Dribbble and Behance show polished work with no path to actually building it — no metadata, no structure, no starting point. They want a fast way to discover app concepts, save the ones they'd build, and jump straight into a working prototype when one exists.

---

## 3. Tech Stack

- **Platform:** Web-first, responsive (mobile browser + desktop)
- **Language + Framework:** TypeScript + Next.js 14 (App Router)
- **Key libraries:**
  - `framer-motion` — swipe gesture handling and card animations (battle-tested on mobile web; avoids iOS Safari scroll conflicts)
  - `next-auth` with magic link (Resend) — email-only auth, no password
  - `Supabase` — Postgres database, row-level security, image storage (design card assets)
  - `zustand` — client-side feed state, swipe index, active filters
  - `shadcn/ui` + Tailwind CSS — component primitives
- **Hosting:** Vercel (zero-config Next.js deploy, edge functions for demand signal logging)
- **Why this stack:** Next.js + Supabase is the fastest solo-dev path to auth + DB + storage with minimal ops overhead. Framer Motion is the only reliable way to get swipe gestures right on mobile web without rolling custom touch handlers. Magic link auth keeps signup friction near zero while capturing an email for v2 re-engagement.

---

## 4. V1 Scope

- **Required v1** — Swipe feed with full-screen design cards (swipe left/right + keyboard arrows)
- **Required v1** — Design detail view with horizontal screen carousel, metadata, and action buttons
- **Required v1** — BUILD queue (persisted per user, requires email to save)
- **Required v1** — "Open in Stitch" deep-link button (shown only when `stitchProjectId` is present)
- **Required v1** — "Request Full Specs" button with persisted "Requested ✓" state per user+card
- **Required v1** — Filter panel (category, platform, sort order)
- **Required v1** — Onboarding (3-slide, shown once, skippable)
- **Required v1** — Empty states for BUILD queue, exhausted feed, and zero filter results
- **Required v1** — Profile / Settings (display name, queue count, built count, sign out, clear queue)
- **Required v1** — "Mark as Built" modal (URL required, screenshot + display name optional; stores submission, does not publish a public wall)
- **Required v1** — Seed content: 133 app concept cards with full metadata (prerequisite: content audit must be complete before code starts)
- **Optional polish** — "New this week" section at top of feed (requires content pipeline; defer until 3+ new cards exist)
- **Optional polish** — "Shuffle / Surprise Me" button when feed is exhausted
- **Optional polish** — Filter result count preview as chips are selected

---

## 5. Out of Scope

- Designer uploads (self-serve or invite-only)
- Payments, Stripe Connect, and designer payouts
- Public "Built With This" showcase wall (form is in v1; public display is not)
- Export to Figma, React code, or image packs
- Team / shared BUILD queues
- Full-text search
- Native iOS / Android app
- Licensing terms and IP enforcement
- Social profiles, follows, or comments

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

> Empty states are conditional states of Screens 1 and 3 — not standalone screens.

---

## 7. State Model

### Screen 1: Swipe Feed

| State | Trigger | Shows | Actions available |
|-------|---------|-------|-------------------|
| **Loading** | Feed first mount | Skeleton card placeholder | None |
| **Active** | Cards available | Current card + peek of next card | Swipe right, swipe left, tap center, open filter |
| **Exhausted** | `currentIndex >= filteredCards.length` | End-of-feed card: queue count summary, "View Your Queue" CTA, "Shuffle" (optional polish) | Navigate to Queue, clear filters |
| **Filter active** | `activeFilters` non-empty | Same as Active; filter icon shows dot indicator | All Active actions + clear filters |
| **Unauthenticated — save attempted** | User swipes right before signing in | Magic link prompt sheet | Enter email, dismiss |

### Screen 2: Design Detail View

| State | Trigger | Shows | Actions available |
|-------|---------|-------|-------------------|
| **Default** | Tap card on Feed or Queue row | All screen images, metadata, action buttons | Scroll carousel, add to queue, open Stitch, request specs, back |
| **Already saved** | `queueItems` contains this card's `id` | "Saved ✓" button (disabled) | Open Stitch, request specs, back |
| **Specs requested** | `specRequests` contains user+card record | "Requested ✓" button (disabled) | Open Stitch, back |
| **No Stitch project** | `stitchProjectId` is null | "Open in Stitch" button hidden | Add to queue, request specs |

### Screen 3: BUILD Queue

| State | Trigger | Shows | Actions available |
|-------|---------|-------|-------------------|
| **Populated** | `queueItems.length > 0` | List of saved cards sorted by `savedAt` desc | Tap row → Detail, mark as built |
| **Empty** | `queueItems.length === 0` | Illustration + "Nothing saved yet" + "Go Swipe" CTA | Navigate to Feed |
| **Item: Saved** | Default after swipe-right | Status badge "Saved" | Mark as built |
| **Item: Opened in Stitch** | User clicks "Open in Stitch" from Detail | Status badge "Opened in Stitch" | Mark as built |
| **Item: Built** | User completes Mark as Built modal | Status badge "Built" | View submission |

### Screen 4: Filter Panel

| State | Trigger | Shows | Actions available |
|-------|---------|-------|-------------------|
| **Default** | Tap filter icon | All chips unselected, sort = Newest | Select chips, apply, clear |
| **Chips selected** | User taps category/platform/sort chips | Selected chips highlighted, (optional) result count | Apply, clear all |
| **Zero results** | Selected filters yield 0 cards | "No designs match" message in feed (Screen 1 empty-filter state) | Clear filters |

### Screen 5: Onboarding

| State | Trigger | Shows | Actions available |
|-------|---------|-------|-------------------|
| **Active** | `onboardingComplete` flag not set in localStorage | 3-slide sequence | Next, Skip, Get Started |
| **Done** | "Get Started" or Skip | Redirect to Feed | — |

### Screen 6: Profile / Settings

| State | Trigger | Shows | Actions available |
|-------|---------|-------|-------------------|
| **Authenticated** | User signed in | Display name, queue count, built count, settings | Edit name, toggle notifications, clear queue, sign out |
| **Clear queue confirm** | Tap "Clear BUILD queue" | Destructive confirm modal | Confirm (deletes all `queueItems`), Cancel |

### Screen 7: Mark as Built Modal

| State | Trigger | Shows | Actions available |
|-------|---------|-------|-------------------|
| **Open** | Tap "Mark as Built" on queue item | URL field (required), screenshot upload (optional), display name (optional) | Submit, dismiss |
| **Submitting** | Submit tapped | Loading spinner on submit button | None |
| **Success** | Submission persisted | Success toast, modal closes, queue item status → "Built" | — |
| **Error** | Submission fails | Inline error message | Retry, dismiss |

---

## 8. Data Model

### Entity: `DesignCard`
| Field | Type | Description |
|-------|------|-------------|
| `id` | uuid | Primary key |
| `title` | text | App concept name |
| `category` | enum: `Social \| Productivity \| Finance \| Health \| E-commerce \| Tools \| Other` | Matches filter chip labels exactly |
| `platform` | enum: `iOS \| Android \| Web` | Matches filter chip labels exactly |
| `screenCount` | integer | Number of mockup screens |
| `description` | text | 1–3 sentence description |
| `heroImageUrl` | text | URL to hero mockup (used on swipe card) |
| `screens` | jsonb array of `{ imageUrl: text, order: integer }` | All mockup screens shown in Detail carousel |
| `stitchProjectId` | text \| null | Stitch project ID; null = "Open in Stitch" button hidden |
| `swipeRightCount` | integer | Aggregate right-swipes; used for "Most Swiped" sort |
| `builtCount` | integer | Count of `BuiltSubmission` records for this card |
| `publishedAt` | timestamptz | Used for "Newest" sort and "New this week" filter |
| `isActive` | boolean | False = soft-deleted from feed |

### Entity: `User`
| Field | Type | Description |
|-------|------|-------------|
| `id` | uuid | Primary key (matches Supabase auth UID) |
| `email` | text | Required for account creation; used for magic link |
| `displayName` | text \| null | Editable in Profile |
| `notificationsEnabled` | boolean | Notification toggle in Settings |
| `onboardingComplete` | boolean | Also mirrored in localStorage; DB is authoritative |
| `createdAt` | timestamptz | — |

### Entity: `QueueItem`
| Field | Type | Description |
|-------|------|-------------|
| `id` | uuid | Primary key |
| `userId` | uuid | FK → User |
| `cardId` | uuid | FK → DesignCard |
| `status` | enum: `Saved \| OpenedInStitch \| Built` | Maps exactly to queue status badge labels |
| `savedAt` | timestamptz | Used for default sort (desc) |

**Relationships:** belongs to User, belongs to DesignCard. Unique constraint on `(userId, cardId)`.

### Entity: `SeenCard`
| Field | Type | Description |
|-------|------|-------------|
| `id` | uuid | Primary key |
| `userId` | uuid | FK → User |
| `cardId` | uuid | FK → DesignCard |
| `seenAt` | timestamptz | — |
| `action` | enum: `swiped_right \| swiped_left` | For analytics only |

**Purpose:** prevents already-seen cards from reappearing in feed.

### Entity: `SpecRequest`
| Field | Type | Description |
|-------|------|-------------|
| `id` | uuid | Primary key |
| `userId` | uuid | FK → User |
| `cardId` | uuid | FK → DesignCard |
| `requestedAt` | timestamptz | — |

**Unique constraint:** `(userId, cardId)` — one request per user per card. When this record exists, "Request Full Specs" button shows "Requested ✓" (disabled).

### Entity: `BuiltSubmission`
| Field | Type | Description |
|-------|------|-------------|
| `id` | uuid | Primary key |
| `userId` | uuid | FK → User |
| `cardId` | uuid | FK → DesignCard |
| `queueItemId` | uuid | FK → QueueItem |
| `url` | text | Required. URL of what was built |
| `screenshotUrl` | text \| null | Optional uploaded screenshot |
| `displayName` | text \| null | Optional credit name |
| `submittedAt` | timestamptz | — |
| `isPublished` | boolean | Default false; reserved for future Showcase wall toggle |

---

## 9. Screens & Acceptance Criteria

### Screen 1: Swipe Feed (Home) [Required v1]

**Purpose:** The primary interaction loop — users swipe through design concept cards to build their queue.

**Visual elements:**
- Full-bleed hero mockup image (fills card)
- Bottom overlay: app title (large), category badge, platform tag, screen count, one-sentence description
- Top-right: Filter icon (dot indicator when `activeFilters` non-empty)
- Bottom navigation bar: Feed / Queue / Profile tabs
- Swipe right = green glow on card edge + "BUILD" label; swipe left = red glow + "DROP" label (during drag only)

**User actions:**
- Swipe right → save to queue, advance card
- Swipe left → pass, advance card
- Keyboard → / ← → same as swipe right / left
- Tap card center → navigate to Screen 2 (Design Detail)
- Tap filter icon → open Screen 4 (Filter Panel)
- Tap Queue tab → navigate to Screen 3
- Tap Profile tab → navigate to Screen 6

**State changes:**
- Swipe right: creates `QueueItem` (status = `Saved`), creates `SeenCard` (action = `swiped_right`), increments `DesignCard.swipeRightCount`, advances `currentIndex`
- Swipe left: creates `SeenCard` (action = `swiped_left`), advances `currentIndex`
- Unauthenticated swipe right: shows magic link prompt sheet before persisting; after auth, retroactively saves the card
- Filter applied: resets `currentIndex` to 0 on filtered subset (excludes already-seen cards)

**Acceptance criteria:**
- [ ] Swiping right on a card creates a `QueueItem` record with `status = Saved` and a `SeenCard` record with `action = swiped_right`
- [ ] Swiping left creates a `SeenCard` record with `action = swiped_left` and no `QueueItem`
- [ ] Pressing → on keyboard triggers the same state changes as swipe right; ← triggers swipe left
- [ ] After a swipe, the next card in the filtered stack is displayed; the swiped card is not shown again in the same session
- [ ] When `currentIndex >= filteredCards.length`, the end-of-feed state is shown with the user's queue count and "View Your Queue" CTA
- [ ] Filter icon shows a visible dot indicator when any filter in `activeFilters` is non-default
- [ ] Unauthenticated user who swipes right sees magic link prompt; completing auth saves the card to queue
- [ ] Cards already in `SeenCard` for the authenticated user are excluded from the feed on load
- [ ] `DesignCard.swipeRightCount` increments by 1 on each right-swipe

**Conditional states:**
- **Loading:** Skeleton card shown while initial card batch loads from Supabase
- **Exhausted:** Triggered when `currentIndex >= filteredCards.length`; shows end-of-feed card (not a separate screen)
- **Zero filter results:** Feed shows "No designs match this filter" with "Clear Filters" CTA inline

---

### Screen 2: Design Detail View [Required v1]

**Purpose:** Shows all mockup screens and full metadata for a design concept; primary action point for queue saves and Stitch deep-links.

**Visual elements:**
- Back arrow (top-left)
- Horizontally scrollable carousel of all `DesignCard.screens` (swipe through mockup images)
- Below carousel: app title (large), category chip, platform chip, screen count, 2–3 sentence description
- Action row (bottom): "Add to BUILD Queue" (primary, filled) | "Open in Stitch" (secondary, shown only if `stitchProjectId` is non-null) | "Request Full Specs" (tertiary, text button)

**User actions:**
- Scroll carousel left/right → view each mockup screen
- "Add to BUILD Queue" → create `QueueItem`, button state → "Saved ✓" (disabled)
- "Open in Stitch" → open Stitch project in new tab, update `QueueItem.status` to `OpenedInStitch`, log event
- "Request Full Specs" → create `SpecRequest`, button state → "Requested ✓" (disabled)
- Back arrow → return to the card index position in Screen 1 (not top of feed)

**State changes:**
- "Add to BUILD Queue": creates `QueueItem { userId, cardId, status: Saved, savedAt: now() }`; button becomes "Saved ✓" disabled
- "Open in Stitch": if `QueueItem` exists, updates `status` to `OpenedInStitch`; if not, creates `QueueItem { status: OpenedInStitch }` first; logs to analytics
- "Request Full Specs": creates `SpecRequest { userId, cardId }`; button becomes "Requested ✓" disabled

**Acceptance criteria:**
- [ ] All `DesignCard.screens` images are displayed in the carousel in `order` sequence
- [ ] "Add to BUILD Queue" button shows "Saved ✓" (disabled) when a `QueueItem` record exists for the authenticated user + this card
- [ ] "Open in Stitch" button is not rendered when `DesignCard.stitchProjectId` is null
- [ ] Clicking "Open in Stitch" opens a new browser tab and updates `QueueItem.status` to `OpenedInStitch` (or creates the item with that status if not yet queued)
- [ ] "Request Full Specs" button shows "Requested ✓" (disabled) when a `SpecRequest` record exists for the authenticated user + this card; this state persists across sessions
- [ ] Pressing the back arrow returns the user to Screen 1 at the same card index they tapped from (not the top of the stack)
- [ ] The "Requested ✓" state is loaded from the `SpecRequest` table on mount, not stored only in component state

---

### Screen 3: BUILD Queue [Required v1]

**Purpose:** The user's personal library of saved design concepts, with status tracking.

**Visual elements:**
- Header: "Your BUILD Queue (N)" where N = `queueItems.length`
- List rows: small thumbnail, app name, category, `savedAt` formatted date, status badge (`Saved` / `Opened in Stitch` / `Built`)
- Per-row action: "Mark as Built" button (opens Screen 7 modal)
- Bottom navigation bar

**User actions:**
- Tap row → navigate to Screen 2 (Design Detail) for that card
- Tap "Mark as Built" → open Screen 7 modal

**State changes:**
- None on Screen 3 itself; state changes happen in Detail and Modal

**Acceptance criteria:**
- [ ] Queue displays all `QueueItem` records for the authenticated user, sorted by `savedAt` descending
- [ ] Header count N matches `queueItems.length`
- [ ] Status badge text matches `QueueItem.status` exactly: "Saved", "Opened in Stitch", or "Built"
- [ ] Tapping a row navigates to Screen 2 for that `cardId`
- [ ] After completing Screen 7 (Mark as Built), the row's status badge updates to "Built" without requiring a page reload
- [ ] Empty state (no `QueueItem` records): shows illustration, "Nothing saved yet", and "Go Swipe" CTA that navigates to Screen 1

---

### Screen 4: Filter Panel [Required v1]

**Purpose:** Allows users to filter and sort the swipe feed before entering the card stack.

**Visual elements:**
- Sheet/overlay panel (slides up from bottom)
- Category filter chips: Social, Productivity, Finance, Health, E-commerce, Tools, Other (multi-select)
- Platform filter chips: iOS, Android, Web, All (single-select; default = All)
- Sort chips: Most Swiped, Newest, Most Built (single-select; default = Newest)
- "Apply Filters" button (primary)
- "Clear All" text link

**User actions:**
- Tap chips → toggle selection
- "Apply Filters" → close panel, update `activeFilters` in zustand store, reset feed to index 0
- "Clear All" → reset all selections to defaults
- Dismiss (swipe down or tap outside) → close without applying

**State changes:**
- "Apply Filters": updates `activeFilters` store object `{ categories: string[], platform: string, sortBy: string }`, resets `currentIndex` to 0 on filtered+unseen card set

**Acceptance criteria:**
- [ ] Category chips support multi-select; Platform and Sort chips are single-select
- [ ] "Apply Filters" resets `currentIndex` to 0 and applies the filter to the feed card stack
- [ ] Filter icon on Screen 1 shows dot indicator after Apply when any filter deviates from defaults (All platforms, Newest sort, no category filter)
- [ ] "Clear All" resets all chips to default values; requires a separate "Apply" to take effect
- [ ] Dismissing the panel without tapping "Apply" does not change the active filters
- [ ] Enum values for category chips match `DesignCard.category` enum values exactly: Social, Productivity, Finance, Health, E-commerce, Tools, Other

---

### Screen 5: Onboarding [Required v1]

**Purpose:** Explains the app to first-time users with a skippable 3-slide intro.

**Visual elements:**
- Slide 1: Swipe gesture animation + tagline "Swipe through app designs. Build what you love."
- Slide 2: BUILD queue illustration + "Save ideas to your BUILD queue."
- Slide 3: Stitch integration illustration + "Jump straight into building."
- Skip button (prominent, all slides)
- "Get Started" button (Slide 3 only)
- Progress dots (3)

**User actions:**
- Next → advance slide
- Skip or "Get Started" → mark onboarding complete, navigate to Screen 1

**State changes:**
- On completion/skip: sets `localStorage.onboardingComplete = true`; if user is authenticated, also sets `User.onboardingComplete = true`

**Acceptance criteria:**
- [ ] Onboarding is shown on first app load when `localStorage.onboardingComplete` is not set
- [ ] Onboarding is NOT shown on subsequent visits when `localStorage.onboardingComplete = true`
- [ ] Skip button on any slide completes onboarding and navigates to Screen 1
- [ ] "Get Started" on Slide 3 completes onboarding and navigates to Screen 1

---

### Screen 6: Profile / Settings [Required v1]

**Purpose:** Minimal account management and queue stats; no social features.

**Visual elements:**
- Display name (editable inline or via edit button)
- Stats: BUILD queue count (from `queueItems.length`), Built count (from `QueueItem` records with `status = Built`)
- Settings section: Notifications toggle, "Clear BUILD queue" (destructive), Sign out

**User actions:**
- Edit display name → updates `User.displayName`
- Toggle notifications → updates `User.notificationsEnabled`
- "Clear BUILD queue" → shows confirm modal; on confirm, deletes all `QueueItem` records for this user
- Sign out → ends Supabase auth session, redirects to Feed (unauthenticated state)

**Acceptance criteria:**
- [ ] Display name updates persist to `User.displayName` in Supabase
- [ ] Queue count stat matches `queueItems.length` for the authenticated user
- [ ] Built count stat matches count of `QueueItem` records where `status = Built` for the authenticated user
- [ ] "Clear BUILD queue" shows a confirmation modal before deleting; canceling leaves queue intact
- [ ] Confirming "Clear BUILD queue" deletes all `QueueItem` records for the user and resets queue count to 0
- [ ] Sign out terminates the session and returns the user to Screen 1 in unauthenticated state

---

### Screen 7: Mark as Built Modal [Required v1]

**Purpose:** Captures a user's build submission tied to a queue item. Stores data for future Showcase; does not publish publicly in v1.

**Visual elements:**
- Modal overlay (dismissible)
- URL field (required, text input, labeled "Link to what you built")
- Screenshot upload (optional, file input)
- Display name field (optional, text input, labeled "Your name (optional)")
- Submit button
- Dismiss / Cancel button

**User actions:**
- Fill URL (required) → enables Submit
- Submit → create `BuiltSubmission`, update `QueueItem.status` to `Built`
- Dismiss → close modal, no state change

**State changes:**
- On submit: creates `BuiltSubmission { userId, cardId, queueItemId, url, screenshotUrl?, displayName?, isPublished: false }`; updates `QueueItem.status` to `Built`; increments `DesignCard.builtCount` by 1; shows success toast; closes modal

**Acceptance criteria:**
- [ ] Submit button is disabled when URL field is empty
- [ ] Submitting creates a `BuiltSubmission` record with `isPublished = false`
- [ ] `QueueItem.status` updates to `Built` after successful submission
- [ ] `DesignCard.builtCount` increments by 1 after successful submission
- [ ] Success toast is shown and modal closes after successful submission
- [ ] Dismissing the modal without submitting makes no data changes
- [ ] The public showcase wall is not present in v1; `BuiltSubmission.isPublished` remains false for all v1 records

---

## 10. External Integrations

### Stitch Deep-Link
- **Service:** Stitch (Google)
- **Purpose:** "Open in Stitch" button drops the user directly into the linked prototype project
- **Specifics:** URL format assumed: `https://stitch.withgoogle.com/project/<stitchProjectId>`. No auth required on the Stitch side — the link must be publicly accessible without the user being logged into Stitch. This assumption **must be verified before building** (see Section 11). Button opens in new tab. On click, log event to Supabase `analytics_events` table `{ userId, cardId, eventType: 'open_stitch', timestamp }` and update `QueueItem.status` to `OpenedInStitch`.
- **Fallback:** If Stitch deep-link is not publicly accessible, hide the "Open in Stitch" button entirely for v1 and replace with no CTA. Do not substitute a different export format without an explicit decision.

### Supabase
- **Service:** Supabase (Postgres + Storage + Auth)
- **Purpose:** Database for all entities, file storage for `BuiltSubmission.screenshotUrl` and `DesignCard` assets, row-level security to scope queue/spec data per user
- **Specifics:** Use Supabase Auth with magic link (email OTP via Resend). RLS policies: users can read all `DesignCard` rows; users can only read/write their own `QueueItem`, `SeenCard`, `SpecRequest`, and `BuiltSubmission` rows.

### Resend (via Supabase Auth)
- **Service:** Resend
- **Purpose:** Sends magic link emails for passwordless auth
- **Specifics:** Triggered by Supabase Auth's email provider. Configure Supabase to use Resend SMTP. One template: "Here's your Design Swipe login link." Verify delivery time < 10s on a real device before launch.

---

## 11. Open Risks & Assumptions to Verify Before Build

- **Risk: Stitch deep-link requires authentication** — If the Stitch project URL redirects to a login wall for users not signed into Stitch, the primary differentiator is nonfunctional. **What to verify:** Open a Stitch project link in an incognito browser with no Stitch session. If it requires login, remove "Open in Stitch" from v1 scope entirely before writing any UI around it.

- **Risk: 822 screenshots are unstructured raw images** — The 133 app concept cards require `title`, `category`, `platform`, `screenCount`, `description`, `heroImageUrl`, and an ordered `screens` array. If the source is a flat folder of PNGs with no metadata, this is a 1–2 week manual content task that must complete before any code starts. **What to verify:** Audit the 822-screenshot source in the first 2 days. Count distinct app concepts, assess existing metadata, and produce a concrete content scope before sprint planning.

- **Risk: Swipe gestures conflict with iOS Safari native navigation** — iOS Safari's swipe-left triggers browser back, which overrides card swipe. **What to verify:** Build a minimal gesture prototype using `framer-motion` with `dragConstraints` and `touch-action: none` on the card container, test on a physical iPhone in Safari before wiring up state.

- **Assumption: Retroactive save across magic link redirect** — The spec assumes that a card swiped right by an unauthenticated user can be saved after the magic link auth flow completes. Next.js magic link auth involves a full-page redirect that clears component state. **What to verify:** Map the auth callback URL flow before implementation; determine whether the pending card `id` must be serialized to a query param or `sessionStorage` to survive the redirect.

- **Risk: Magic link delivery latency breaks first-swipe moment** — The auth prompt fires at peak engagement (first right-swipe). A slow or spam-filtered email kills the conversion. **What to verify:** Test Resend delivery time end-to-end on a real device and real email domain before launch; include "Check spam" messaging in the prompt UI.

---

## 12. Visual Reference

- **Live prototype (static mock — use for visual layout reference only):** https://jakebuild.github.io/idea-factory/29-design-swipe-tinder-for-app-de/
- **Source idea:** `/Users/giangnguyen/workspace/idea-factory/29-design-swipe-tinder-for-app-de/idea-v2.md`
- **Note on prototype vs PRD:** The prototype is a design reference only. Where the prototype shows a "Built With This" public showcase as a navigable screen, this PRD explicitly marks it as **out of scope for v1**. The Mark as Built modal (Screen 7) stores submissions with `isPublished = false` but does not display a public wall. Implement per this PRD, not the prototype layout.

---

## 13. Suggested Folder Structure

```
design-swipe/
├── app/
│   ├── layout.tsx                  # Root layout, bottom nav
│   ├── page.tsx                    # → /feed
│   ├── feed/
│   │   └── page.tsx                # Screen 1: Swipe Feed
│   ├── design/
│   │   └── [id]/
│   │       └── page.tsx            # Screen 2: Design Detail
│   ├── queue/
│   │   └── page.tsx                # Screen 3: BUILD Queue
│   ├── profile/
│   │   └── page.tsx                # Screen 6: Profile / Settings
│   └── auth/
│       └── callback/
│           └── route.ts            # Magic link callback handler
├── components/
│   ├── feed/
│   │   ├── SwipeCard.tsx           # Framer Motion card with gesture handler
│   │   ├── CardStack.tsx           # Manages currentIndex and filtered array
│   │   └── EndOfFeedCard.tsx       # Exhausted feed state
│   ├── detail/
│   │   ├── ScreenCarousel.tsx      # Horizontal scroll carousel
│   │   └── ActionRow.tsx           # Queue/Stitch/RequestSpecs buttons
│   ├── queue/
│   │   ├── QueueList.tsx
│   │   └── QueueRow.tsx
│   ├── filter/
│   │   └── FilterPanel.tsx         # Bottom sheet with chips
│   ├── onboarding/
│   │   └── OnboardingSlides.tsx
│   ├── modals/
│   │   └── MarkAsBuiltModal.tsx
│   └── ui/                         # shadcn/ui primitives
├── lib/
│   ├── supabase/
│   │   ├── client.ts               # Browser Supabase client
│   │   ├── server.ts               # Server Supabase client (RSC)
│   │   └── types.ts                # Generated DB types
│   └── store/
│       └── feedStore.ts            # Zustand: currentIndex, activeFilters
├── supabase/
│   ├── migrations/                 # SQL migration files
│   └── seed.sql                    # 133 design card seed data
└── public/
    └── designs/                    # Static hero images (or Supabase Storage URLs)
```
