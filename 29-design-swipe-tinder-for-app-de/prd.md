# PRD: Design Swipe — Tinder for App Designs

## 1. Overview

Design Swipe is a swipe-to-save inspiration tool for solo developers and vibe coders. Users scroll through a card stack of beautifully designed app concepts (full-screen mockups), swipe right to add to their personal BUILD queue, and swipe left to pass. The BUILD queue becomes a curated personal design library — saved ideas the user actually intends to build. The key differentiator over Dribbble/Behance: every card is framed as a buildable app concept, and cards with a linked Stitch project surface a one-click "Open in Stitch" button that drops the user directly into a working prototype. v1 is a free curation tool seeded with 133 app concepts from an existing 822-screenshot database; no payments, no designer uploads, no marketplace.

---

## 2. Target User

Solo developers, indie hackers, and vibe coders who build apps with AI coding tools. Their pain: they have the technical ability to ship but spend hours on Dribbble finding inspiration that never translates into something they can actually build from. Existing inspiration sites (Dribbble, Behance, Mobbin) are great for passive browsing but offer no bridge to actual development — no structured metadata, no buildable specs, no path from "I liked this" to "I started building."

---

## 3. Tech Stack

- **Platform:** Web (responsive, mobile-first)
- **Language + Framework:** TypeScript + Next.js 14 (App Router)
- **Key Libraries:**
  - `framer-motion` — swipe gesture handling and card animation (tested on iOS Safari, avoids roll-your-own touch handler hell)
  - `zustand` — client state for feed position, queue, filter state
  - `next-auth` with magic link (Resend) — email-only auth, no passwords; required to persist BUILD queue server-side
  - `prisma` + PostgreSQL (Supabase free tier) — data persistence, demand signal logging
  - `uploadthing` — screenshot uploads for "Mark as Built" flow
  - `shadcn/ui` + Tailwind CSS — component base, no design system from scratch
- **Hosting / Deployment:** Vercel (free tier sufficient for v1 traffic)
- **Why this fits:** Next.js + Vercel = zero infra config. Framer-motion has battle-tested mobile swipe support. Supabase free tier handles 133 cards + user queue data comfortably. Magic link auth captures emails (critical for v2 re-engagement) without a friction-heavy signup form. The entire stack is AI-codegen-friendly with strong Cursor/Claude support.

---

## 4. Screens & Acceptance Criteria

### Screen 1: Swipe Feed (Home)

**Purpose:** Core swipe loop — full-screen card stack where users swipe right (BUILD) or left (DROP) through app design concepts.

**Key UI elements:**
- Full-bleed hero mockup image covering most of the viewport
- App name overlaid at bottom of card (large, white, semi-transparent bg)
- Category badge (top-left chip)
- Platform tag (iOS / Android / Web — top-right chip)
- Screen count ("8 screens" — below app name)
- One-sentence description below screen count
- Subtle left/right arrow hint indicators on first load (disappears after first swipe)
- Green glow + "BUILD" label appears on right-drag; red glow + "DROP" on left-drag
- Bottom nav: Feed (active) / Queue / Profile
- Filter icon (top-right corner of nav bar) with active indicator dot when filters applied

**Acceptance criteria:**
- [ ] Cards render full-screen with hero image, app name, category badge, platform tag, screen count, and one-sentence description
- [ ] Swipe right: card animates off screen to the right with green BUILD flash, next card slides in, design is silently added to BUILD queue
- [ ] Swipe left: card animates off screen to the left with red DROP flash, next card slides in
- [ ] Keyboard shortcut → (right arrow) triggers BUILD, ← (left arrow) triggers DROP
- [ ] Tapping/clicking the card center navigates to Design Detail View
- [ ] Already-seen card IDs are tracked in session/user state — cards are not shown twice
- [ ] When all cards are exhausted, the feed shows the end-of-feed empty state (Screen 7b)
- [ ] Active filters from Filter Panel are applied to the card stack; filter icon shows dot indicator when active
- [ ] Bottom navigation is always visible; Feed tab is active/highlighted
- [ ] Swipe gesture does not conflict with browser scroll or iOS Safari back gesture

**Edge cases:**
- Feed empty due to aggressive filters → show Screen 7c (no results state)
- Feed exhausted (all cards seen) → show Screen 7b (end of feed state)
- User not logged in swipes right → add to localStorage queue; prompt email capture after 3 saves ("Save your queue — enter your email")
- Image fails to load → show placeholder with app name and category color fill
- Slow connection → show card skeleton loader while next card preloads

---

### Screen 2: Design Detail View

**Purpose:** Shows all mockup screens for a single app concept and exposes the primary action CTAs.

**Key UI elements:**
- Back arrow (top-left) returning to feed at the same position
- Horizontal scrollable carousel of all mockup screens (full-width, paginated dots below)
- App name (large heading) below carousel
- Category chip + Platform chip in a row
- Screen count label ("8 screens")
- 2–3 sentence description
- Action row (bottom, always visible):
  - "Add to BUILD Queue" — primary filled button (changes to "Saved ✓" if already in queue)
  - "Open in Stitch" — secondary outlined button (only rendered if `stitchProjectId` is non-null)
  - "Request Full Specs" — tertiary text button (changes to "Requested ✓" after click, persisted)

**Acceptance criteria:**
- [ ] All screens in the design's `screens[]` array are shown in a horizontally scrollable carousel with pagination dots
- [ ] App name, category, platform, screen count, and description are displayed below the carousel
- [ ] "Add to BUILD Queue" button adds the design to the user's queue and changes state to "Saved ✓"
- [ ] If design is already in queue, button renders as "Saved ✓" on arrival
- [ ] "Open in Stitch" button is only rendered when `stitchProjectId` is non-null; clicking opens Stitch project in a new tab and logs the event to DB
- [ ] "Request Full Specs" button changes to "Requested ✓" on click and state persists for the session (and per user if logged in); click is logged to DB
- [ ] Back arrow returns to feed at the card the user tapped (not back to card 0)
- [ ] Transition from feed to detail is a smooth push (not a jarring modal pop)
- [ ] Carousel swipe does not conflict with page scroll

**Edge cases:**
- Design has only 1 screen → carousel shows single image, no dots
- `stitchProjectId` is null → "Open in Stitch" button is completely absent (not disabled)
- "Open in Stitch" link target is unreachable → button opens new tab anyway; no error handling needed (external dependency)
- User not logged in clicks "Add to BUILD Queue" → same flow as swipe-right: localStorage + email prompt after 3 saves

---

### Screen 3: BUILD Queue

**Purpose:** Personal library of all right-swiped or manually queued designs, with status tracking.

**Key UI elements:**
- Header: "Your BUILD Queue (N)" where N is live count
- List of saved designs, sorted by date saved descending
- Each row: small thumbnail (60×80px), app name, category, date saved, status badge
- Status badge variants: "Saved" (neutral) / "Opened in Stitch" (blue) / "Built" (green)
- Tap row → opens Design Detail View
- "Mark as Built" action per row (icon button or swipe action on mobile)
- Empty state when queue is empty (Screen 7a)

**Acceptance criteria:**
- [ ] All designs the user has right-swiped or added via Detail View appear in the list
- [ ] List is sorted by date saved, newest first
- [ ] Each row shows thumbnail, app name, category chip, date saved, and status badge
- [ ] Status badge reflects current state: "Saved" by default, "Opened in Stitch" after tapping that button, "Built" after submitting the Mark as Built modal
- [ ] Tapping a row navigates to Design Detail View for that design
- [ ] "Mark as Built" action per row opens the Mark as Built Modal (Screen 8)
- [ ] Queue count in header updates in real-time when items are added from feed
- [ ] Queue persists across sessions (server-side for logged-in users, localStorage for anonymous)
- [ ] Empty state shown when queue has 0 items

**Edge cases:**
- Anonymous user with localStorage queue visits on new device → queue is empty; no sync across devices (acceptable in v1)
- Design in queue has been removed from the database → show greyed-out row with "Design unavailable" label; do not crash
- Very long queue (100+ items) → list must not lag; use virtualized list if >50 items

---

### Screen 4: Filter / Browse

**Purpose:** Overlay panel to filter the swipe feed by category, platform, and sort order before entering the swipe loop.

**Key UI elements:**
- Sheet/drawer that slides up from bottom (mobile) or dropdown panel (desktop)
- Section: Category — multi-select chips: Social, Productivity, Finance, Health, E-commerce, Tools, Other
- Section: Platform — single-select: All / iOS / Android / Web
- Section: Sort — single-select: Most Swiped / Newest / Most Built
- Live result count preview: "Showing 47 designs" updates as chips are toggled
- "Apply Filters" primary button (closes panel, resets feed to filtered stack)
- "Clear All" text link (resets all filters to defaults)

**Acceptance criteria:**
- [ ] Filter panel opens from the filter icon in the feed top bar
- [ ] Category filter is multi-select; multiple categories can be active simultaneously
- [ ] Platform filter is single-select; selecting one deselects others (All is default)
- [ ] Sort filter is single-select; Most Swiped is default
- [ ] Result count updates live as filter chips are toggled (no Apply needed to see count)
- [ ] "Apply Filters" closes the panel and resets the feed card stack to the filtered result set
- [ ] Feed filter icon shows an active dot indicator when any non-default filter is applied
- [ ] "Clear All" resets all three filter sections to defaults and updates count
- [ ] Filtered feed does not re-show cards the user has already seen (already-seen IDs excluded from filtered set)

**Edge cases:**
- Filter combination returns 0 results → show count "0 designs" before applying; after applying, show Screen 7c
- All cards in filtered set already seen → show Screen 7b (feed exhausted) immediately after applying

---

### Screen 5: Onboarding (First Launch)

**Purpose:** Three-slide intro shown once on first app load; teaches the swipe mechanic, BUILD queue concept, and Stitch integration.

**Key UI elements:**
- Slide 1: Animated swipe gesture illustration, tagline "Swipe through app designs. Build what you love."
- Slide 2: BUILD queue mockup, copy "Save ideas to your BUILD queue."
- Slide 3: Stitch integration illustration, copy "Jump straight into building."
- Progress dots (3) at bottom of each slide
- Skip button (top-right, visible on all slides)
- "Get Started" button on slide 3 only (navigates to feed)
- Swipe between slides is supported

**Acceptance criteria:**
- [ ] Onboarding is shown exactly once — completion is stored in localStorage and never shown again
- [ ] Skip button on any slide navigates directly to the feed
- [ ] "Get Started" on slide 3 navigates to the feed
- [ ] Slide transitions are animated (slide or fade)
- [ ] Progress dots reflect the current slide

**Edge cases:**
- User force-refreshes mid-onboarding → show onboarding from slide 1 again (localStorage flag only written on completion or skip)
- Returning user with flag set → onboarding never shown; navigate directly to feed

---

### Screen 6: Empty States

**Purpose:** Three distinct empty states covering BUILD queue empty, feed exhausted, and filter with no results.

**Variant A — BUILD Queue Empty:**
- Illustration (placeholder graphic)
- Heading: "Nothing saved yet"
- Subtext: "Swipe right on designs you want to build"
- CTA button: "Go Swipe" → navigates to Feed tab

**Variant B — Feed Exhausted:**
- Heading: "You've seen everything!"
- Subtext: "Check back soon for new designs"
- Stat: "You've saved N designs to your BUILD queue"
- CTA button: "View Your Queue" → navigates to Queue tab
- Secondary CTA: "Shuffle" → re-presents previously seen cards in random order

**Variant C — Filter Returns No Results:**
- Heading: "No designs match this filter"
- Subtext: "Try a different combination"
- CTA button: "Clear Filters" → resets all filters and refreshes feed

**Acceptance criteria:**
- [ ] Variant A is shown when BUILD Queue has 0 items
- [ ] Variant B is shown when the user has seen all cards in the current (filtered or unfiltered) feed
- [ ] Variant C is shown when applied filters return 0 cards
- [ ] All CTA buttons navigate to the correct destination
- [ ] Variant B's queue count stat is accurate
- [ ] "Shuffle" in Variant B re-queues all previously seen cards in random order and returns to swipe feed

**Edge cases:**
- User is on Variant B but new cards are added to the DB → Shuffle or returning to feed should pick them up (no manual refresh required if using SWR/React Query with revalidation)

---

### Screen 7: Profile / Settings

**Purpose:** Minimal user profile and app settings; not a social profile.

**Key UI elements:**
- Display name (editable inline or via input field)
- Stat: BUILD Queue count
- Stat: Built count (designs marked as Built)
- Settings section:
  - Notification toggle (email digest — off by default)
  - "Clear BUILD Queue" — destructive action with confirm modal
  - "Sign Out"

**Acceptance criteria:**
- [ ] Display name is editable and saved on blur/Enter
- [ ] BUILD queue count and Built count are accurate and live
- [ ] Notification toggle saves preference to DB
- [ ] "Clear BUILD Queue" shows a confirm modal before deleting; on confirm, all queue items are deleted and count resets to 0
- [ ] "Sign Out" ends the session and redirects to the feed (anonymous mode)
- [ ] Profile tab in bottom nav is active/highlighted on this screen

**Edge cases:**
- Anonymous user navigates to Profile tab → prompt to enter email to save their profile; show a soft gate, not a hard block
- Display name left blank → show placeholder "Anonymous Builder"

---

### Screen 8: Mark as Built Modal

**Purpose:** Triggered from BUILD Queue; lets users submit what they built and records it for the future Showcase wall.

**Key UI elements:**
- Modal overlay (dismissible by tapping outside or X button)
- Design card reference at top (thumbnail + app name)
- Required field: URL of what they built
- Optional field: Screenshot upload (drag-and-drop or file picker)
- Optional field: Display name to credit
- "Submit" primary button
- Cancel / X to dismiss

**Acceptance criteria:**
- [ ] Modal is triggered from the "Mark as Built" action on a BUILD Queue row
- [ ] URL field is required; Submit is disabled until a valid URL is entered
- [ ] Screenshot upload is optional; accepted formats: JPG, PNG, WebP; max 5MB
- [ ] Display name field is optional; defaults to the user's profile display name if set
- [ ] On Submit: success toast shown, modal closes, item status in queue updates to "Built"
- [ ] Submission is written to DB (url, screenshotUrl, displayName, designId, userId, createdAt)
- [ ] Cancel/dismiss closes modal without saving; queue status unchanged

**Edge cases:**
- Invalid URL entered → show inline validation error before allowing submit
- Upload fails (network error) → show error toast, keep modal open, allow retry
- User submits without a display name → store as anonymous, display as "Anonymous Builder" in future Showcase

---

## 5. Data Model

### Entity: DesignCard
| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Primary key |
| `title` | string | App concept name |
| `category` | enum | Social \| Productivity \| Finance \| Health \| E-commerce \| Tools \| Other |
| `platform` | enum | iOS \| Android \| Web \| Cross-platform |
| `screenCount` | int | Number of mockup screens in this concept |
| `description` | string | 1–3 sentence description shown on card and detail view |
| `heroImageUrl` | string | URL of the primary/first screen image (shown on swipe card) |
| `stitchProjectId` | string? | Stitch project ID — null if no Stitch project linked |
| `swipeCount` | int | Total right-swipes across all users (for "Most Swiped" sort) |
| `builtCount` | int | Total "Mark as Built" submissions for this card |
| `createdAt` | DateTime | When added to the DB |
| **screens** | Screen[] | Ordered array of screen images |

### Entity: Screen
| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Primary key |
| `designCardId` | string | FK → DesignCard |
| `imageUrl` | string | URL of the mockup image |
| `order` | int | Display order in carousel (0-indexed) |

### Entity: User
| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Primary key |
| `email` | string | Unique — used for magic link auth |
| `displayName` | string? | Optional display name |
| `notificationsEnabled` | boolean | Email digest opt-in, default false |
| `createdAt` | DateTime | Account creation timestamp |

### Entity: QueueItem
| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Primary key |
| `userId` | string | FK → User |
| `designCardId` | string | FK → DesignCard |
| `status` | enum | Saved \| OpenedInStitch \| Built |
| `savedAt` | DateTime | When right-swiped or manually added |

### Entity: SeenCard
| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Primary key |
| `userId` | string | FK → User (null for anonymous — use localStorage) |
| `designCardId` | string | FK → DesignCard |
| `seenAt` | DateTime | When card was shown |
| `action` | enum | Swiped_Right \| Swiped_Left \| Skipped |

### Entity: DemandSignal
| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Primary key |
| `designCardId` | string | FK → DesignCard |
| `userId` | string? | FK → User (null for anonymous) |
| `type` | enum | RequestFullSpecs \| OpenedInStitch |
| `createdAt` | DateTime | Timestamp |

### Entity: BuiltSubmission
| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Primary key |
| `designCardId` | string | FK → DesignCard |
| `userId` | string? | FK → User |
| `url` | string | URL of the built app |
| `screenshotUrl` | string? | Uploaded screenshot URL |
| `displayName` | string? | Credit name |
| `createdAt` | DateTime | Submission timestamp |

**Relationships:**
- `DesignCard` has many `Screen` (one-to-many)
- `User` has many `QueueItem` (one-to-many)
- `User` has many `SeenCard` (one-to-many)
- `QueueItem` references one `DesignCard`
- `SeenCard` references one `DesignCard`
- `DemandSignal` references one `DesignCard`, optionally one `User`
- `BuiltSubmission` references one `DesignCard`, optionally one `User`

---

## 6. External Integrations

**Service:** Resend
**Purpose:** Magic link email auth (passwordless sign-in) and optional email digest notifications
**Specifics:** Use `next-auth` with the Email provider configured to use Resend's SMTP or API. Resend free tier (100 emails/day) is sufficient for v1. Store `RESEND_API_KEY` in environment. No gotchas — next-auth handles token generation and expiry.

---

**Service:** Supabase (PostgreSQL)
**Purpose:** Primary database for all entities; storage bucket for screen images and BuiltSubmission screenshots
**Specifics:** Use Prisma ORM with `DATABASE_URL` pointing to Supabase connection pooler (port 6543 for Prisma). Use Supabase Storage for images — store all 133 design concept images and user-uploaded screenshots in a public bucket. Gotcha: use the connection pooler URL for Prisma in production (Vercel serverless), not the direct connection URL — direct connections exhaust the connection limit under load.

---

**Service:** Stitch (deep-link only)
**Purpose:** "Open in Stitch" button deep-links users from a design card directly into the corresponding Stitch project
**Specifics:** Construct the deep-link URL as `https://stitch.withgoogle.com/project/<stitchProjectId>` (or equivalent — **verify the exact URL format before building**; this is the highest-risk integration). No auth, no API key — this is a plain URL open in a new tab. Log the click to `DemandSignal` table with `type: OpenedInStitch`. If the deep-link format turns out to require auth or isn't publicly accessible, fallback: hide the button entirely and treat all `stitchProjectId`-linked cards as having `null` until confirmed.

---

**Service:** UploadThing
**Purpose:** Screenshot uploads in the "Mark as Built" modal
**Specifics:** Use `uploadthing` SDK for Next.js. Configure a `imageUploader` route with `maxFileSize: "5MB"`, accepted types: `["image/jpeg", "image/png", "image/webp"]`. Store the returned URL in `BuiltSubmission.screenshotUrl`. Free tier (2GB storage) is sufficient for v1.

---

## 7. Out of Scope (v1)

- Designer uploads (self-serve or invite-only) — zero upload UI in v1
- Payments, Stripe Connect, designer payouts, or any monetization features
- $9 "unlock full Stitch project" export flow
- Licensing terms, IP enforcement, or DMCA workflow
- Export to Figma, React code generation, or image pack downloads
- Team / shared BUILD queues
- Full-text search across design cards
- Native iOS or Android app (web-only in v1)
- Public "Built With This" Showcase wall — the submission form (Modal) is built, but the public display wall is not rendered anywhere
- Social profiles, follows, or any social graph features
- Admin CMS for content management (seed data is loaded via DB migration script)
- "New this week" feed section (requires content pipeline)
- Notifications (toggle is in Settings but sending is not implemented in v1)

---

## 8. Known Risks

**Risk:** The 822 screenshots may be unstructured raw images with no metadata — structuring them into 133 card objects (title, category, platform, description, screen groupings) is manual content work estimated at 1–2 weeks. **Mitigation:** Before writing a line of code, audit the screenshot database for 1–2 days. Count distinct app concepts, assess what metadata already exists, and identify what must be manually created. Gate the build start on having at least 30 structured cards ready.

**Risk:** The Stitch deep-link ("Open in Stitch") is the entire product moat — if Stitch doesn't support a public project-by-ID URL without requiring the viewer to be authenticated, this feature doesn't work. **Mitigation:** Spend 10 minutes verifying the deep-link URL format before building any UI around it. If it requires auth, remove the feature from v1 entirely and don't show the button.

**Risk:** Anonymous swipe data (Request Full Specs clicks, demand signals) is noise if you can't contact those users for v2 beta. **Mitigation:** Gate BUILD queue persistence behind email capture after 3 saves. Use the pattern "Email me my queue" (magic link flow) — one field, no password. This converts your most engaged anonymous users into a re-engagement list.

**Risk:** The swipe feed is finite (133 cards) with no content refresh plan, which breaks retention after the first session for users who swipe quickly. **Mitigation:** Implement the "Shuffle" button on the Feed Exhausted empty state as a stop-gap. Before launch, define a content cadence (even 3–5 new cards per week) and build a simple internal admin route to add cards via a form, so you don't need to touch the DB directly.

**Risk:** Mobile swipe gestures on web (especially iOS Safari) conflict with browser-native scroll and the back swipe gesture. **Mitigation:** Use `framer-motion`'s `drag` with `dragConstraints` and `dragElastic`, and set `overscroll-behavior: none` on the card container. Do not implement touch handling from scratch. Test on a real iOS Safari device before launch — simulator behavior differs.

---

## 9. Visual Reference

- **Live prototype:** https://jakebuild.github.io/idea-factory/29-design-swipe-tinder-for-app-de/
- **Source idea:** `/Users/giangnguyen/workspace/idea-factory/29-design-swipe-tinder-for-app-de/idea-v2.md`
- **Latest critique:** `/Users/giangnguyen/workspace/idea-factory/29-design-swipe-tinder-for-app-de/critique-v2.md`

---

## 10. Suggested Folder Structure

```
design-swipe/
├── prisma/
│   ├── schema.prisma          # All entity definitions
│   └── seed.ts                # Seed script for 133 design cards
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx           # Feed (Screen 1)
│   │   ├── design/
│   │   │   └── [id]/
│   │   │       └── page.tsx   # Design Detail View (Screen 2)
│   │   ├── queue/
│   │   │   └── page.tsx       # BUILD Queue (Screen 3)
│   │   ├── profile/
│   │   │   └── page.tsx       # Profile / Settings (Screen 7)
│   │   └── api/
│   │       ├── auth/
│   │       │   └── [...nextauth]/route.ts
│   │       ├── queue/
│   │       │   └── route.ts   # GET/POST queue items
│   │       ├── seen/
│   │       │   └── route.ts   # POST seen card
│   │       ├── demand/
│   │       │   └── route.ts   # POST demand signal
│   │       ├── built/
│   │       │   └── route.ts   # POST built submission
│   │       └── uploadthing/
│   │           └── route.ts
│   ├── components/
│   │   ├── feed/
│   │   │   ├── CardStack.tsx  # Swipe card stack + gesture logic
│   │   │   ├── DesignCard.tsx # Individual card UI
│   │   │   └── FilterPanel.tsx
│   │   ├── detail/
│   │   │   ├── ScreenCarousel.tsx
│   │   │   └── ActionRow.tsx
│   │   ├── queue/
│   │   │   ├── QueueList.tsx
│   │   │   └── MarkAsBuiltModal.tsx
│   │   ├── onboarding/
│   │   │   └── OnboardingSlides.tsx
│   │   ├── empty/
│   │   │   └── EmptyState.tsx # Variants: queue | exhausted | no-results
│   │   └── ui/                # shadcn/ui generated components
│   ├── lib/
│   │   ├── prisma.ts          # Prisma client singleton
│   │   ├── auth.ts            # next-auth config
│   │   └── store.ts           # Zustand store (feed position, filters, local queue)
│   └── types/
│       └── index.ts           # Shared TypeScript types
├── public/
│   └── images/                # Local fallback images (if not using Supabase Storage)
├── .env.local                 # DATABASE_URL, NEXTAUTH_SECRET, RESEND_API_KEY, etc.
├── next.config.ts
├── tailwind.config.ts
└── package.json
```
