# PRD: AI Dating Photo Technical Audit

## 1. Overview

A web-based technical photo audit tool for people preparing their dating profile photos. Users upload 3–6 solo clear-face photos; the app scores each independently on five measurable technical criteria (lighting quality, face visibility, focus/sharpness, framing/crop, background clarity) using a vision model. The free output shows a 1–5 score per photo and identifies the technical best and worst. A one-time $5 payment unlocks full per-criterion explanations, one specific fix per weak photo, and a priority fix order. No accounts. No attractiveness claims. No stored photos. The product earns on first use or not at all.

---

## 2. Target User

People actively building or refreshing a dating profile (Tinder, Hinge, Bumble) who have multiple candidate photos and cannot decide which to use. Their pain: no objective signal beyond asking friends who give vague or kind answers. Existing alternatives — Reddit feedback threads, general AI chat — require manual effort and produce inconsistent framing. This tool gives a structured, criteria-based reading in under a minute.

---

## 3. Tech Stack

**Platform:** Web (desktop + mobile browser). No native app in v1.

**Language + Framework:** TypeScript + Next.js 14 (App Router). API routes handle moderation, scoring, and Stripe webhooks server-side. React components render all UI. Keeps front-end and back-end in one repo, minimal infra, fast iteration.

**Key Libraries:**
- `stripe` + `@stripe/stripe-js` — one-time payment intent, client-side card element
- `openai` — GPT-4o Vision API for single-pass scoring; OpenAI moderation endpoint for NSFW pre-check
- `aws-sdk` (Rekognition) — group-shot and face-count detection (fallback: OpenAI Vision prompt with face-count check if Rekognition cost is prohibitive)
- `uuid` — session ID generation
- `iron-session` or `next-auth` (session only, no auth) — lightweight cookie-based session to carry session state across page navigations without a database
- `react-dropzone` — upload drag-and-drop
- `sharp` — server-side image resize/thumbnail before sending to vision model (cost control)

**Storage:** No persistent database. Session state held in a signed cookie (session_id + payment_status + result payload ≤ 4 KB limit). Photo bytes processed in-memory on the API route and never written to disk or object storage. Thumbnails for the results UI are generated server-side, base64-encoded, and returned in the score response — not stored.

**Hosting:** Vercel. Next.js API routes run as serverless functions. No additional infrastructure required for v1.

**Why this fits:** One repo, one deploy command, no DB to provision. Vercel's serverless timeout (60s on Pro) is sufficient for 6 sequential vision-model calls. The cookie-based session avoids Redis/Upstash setup. Stripe one-time payment intent needs no webhook persistence — payment confirmation is polled client-side via `stripe.retrievePaymentIntent`.

---

## 4. V1 Scope

- **Required v1** — Upload flow: drag-and-drop or file picker, 3–6 photo limit, file format/size validation
- **Required v1** — Client-side pre-validation: JPEG/PNG/WEBP only, max 10 MB per file, 3 min / 6 max count
- **Required v1** — Server-side moderation: NSFW check (OpenAI moderation) + group/obscured-face check (AWS Rekognition or Vision prompt) before scoring
- **Required v1** — Inline rejection handling: rejected photo identified with plain-language reason, user can remove and replace
- **Required v1** — Single-pass scoring: one GPT-4o Vision API call per photo, 5 criteria (lighting quality, face visibility, focus/sharpness, framing/crop, background clarity), score 1–5 + one-line raw signal per criterion
- **Required v1** — Free results: per-photo aggregate score (average of 5 criteria, displayed as X.X / 5), best photo badge, worst photo badge, checkmark-only criterion summary (no text)
- **Required v1** — Payment gate: Stripe one-time charge of $5 to unlock full explanations
- **Required v1** — Paid results: per-criterion explanations, one specific fix per photo scoring below 3/5 on any criterion, priority fix callout ("Start here")
- **Required v1** — Disagree / raw breakdown modal: tap any result card (free or paid) to see raw criterion scores + exact model signal text
- **Required v1** — Session-only lifecycle: photos processed in-memory, thumbnails returned inline, session expires after 2 hours, re-audit CTA starts a new session
- **Required v1** — Privacy copy: on upload screen and loading screen; one-sentence policy statement
- **Optional polish** — Animated scan effect on processing screen
- **Optional polish** — Re-audit CTA on paid results screen with "Retook your photos?" framing
- **Optional polish** — "Tell us what you think" free-text field in the disagree modal (stored anonymously, no account required)

---

## 5. Out of Scope

- Pairwise photo comparisons (cut entirely — will not return under any name)
- LinkedIn / Instagram / platform-specific modes
- Opener or caption generator
- Attractiveness scoring or attractiveness language anywhere in UI copy
- "Will perform better on Hinge" or any match outcome prediction
- Account creation, login, or saved history
- Social sharing of results
- Match feedback loop or analytics dashboard
- Subscription or credits model (one-time charge only in v1)
- Native mobile app (iOS or Android)
- PDF or shareable report export
- A/B price testing UI (handle manually by deploying config change if needed)
- Refund flow automation (handle manually via Stripe dashboard in v1)

---

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Landing / Value Prop | Required v1 |
| 2 | Upload | Required v1 |
| 3 | Processing / Loading | Required v1 |
| 4 | Free Results — Score Reveal | Required v1 |
| 5 | Payment Gate | Required v1 |
| 6 | Paid Results — Full Audit | Required v1 |

**Modals (not standalone screens):**
- Rejection Modal — conditional state of Screen 2
- Disagree / Raw Breakdown Modal — triggered from Screen 4 and Screen 6

---

## 7. State Model

### Screen 1: Landing

| State | Trigger | Shows | Actions |
|-------|---------|-------|---------|
| Default | Page load | Headline, 3-bullet explainer, sample score card (static), CTA | Tap "Audit my photos" → navigate to Screen 2 |

### Screen 2: Upload

| State | Trigger | Shows | Actions |
|-------|---------|-------|---------|
| Empty | Arrive from Screen 1 | Drop zone, count indicator (0/6), disabled Analyze CTA | Add photos |
| Partial (< 3) | 1–2 photos added | Thumbnails + remove buttons, count indicator, disabled Analyze CTA, inline constraint checklist | Add more, remove photo |
| Ready (3–6) | 3–6 valid photos added | Thumbnails, enabled Analyze CTA | Remove photo, tap Analyze |
| File error | Invalid file type / size | Inline error under thumbnail: "File must be JPEG, PNG, or WEBP under 10 MB" | Dismiss, add new file |
| Max reached | 6 photos present | Add zone hidden, count indicator "6/6" | Remove to add different photo |
| Rejection modal | Server returns rejected photo | Modal: photo thumbnail, plain-language rejection reason, "Remove" or "Continue without it" (if count ≥ 3) | Remove photo, continue |
| Submitting | Analyze tapped | Analyze CTA shows spinner, upload disabled | — (auto-advance to Screen 3) |

### Screen 3: Processing / Loading

| State | Trigger | Shows | Actions |
|-------|---------|-------|---------|
| Moderating | Upload complete | "Checking photos…" message, indeterminate progress | None (auto-advance or rejection) |
| Analyzing | Moderation passed | "Analyzing photo N of M…", progress bar, privacy reminder | None (auto-advance) |
| Error (moderation API failure) | Moderation API returns error | "Something went wrong — please try again" with retry CTA | Retry → back to Screen 2 |
| Error (scoring API failure) | Vision API returns error | "Analysis failed — please try again" with retry CTA | Retry → back to Screen 2 |

### Screen 4: Free Results — Score Reveal

| State | Trigger | Shows | Actions |
|-------|---------|-------|---------|
| Default | Scoring complete | Score cards (photo thumbnail + aggregate score), best badge, worst badge, criterion checkmark row, unlock CTA | Tap card → Disagree modal; tap Unlock CTA → Screen 5 |
| Disagree modal open | Tap any score card | Modal overlay: photo, 5 criteria with raw score + raw signal text | Close modal |

### Screen 5: Payment Gate

| State | Trigger | Shows | Actions |
|-------|---------|-------|---------|
| Default | Tap Unlock CTA on Screen 4 | Price ($5), what's included, Stripe card form, "Cancel" link | Submit payment, cancel |
| Processing | Submit payment | Stripe element in loading state, CTA spinner | None |
| Error | Stripe declines | Inline Stripe error message below card form | Re-enter card |
| Success | Payment confirmed | Auto-navigate to Screen 6 | — |

### Screen 6: Paid Results — Full Audit

| State | Trigger | Shows | Actions |
|-------|---------|-------|---------|
| Default | Payment confirmed | Per-photo cards sorted best → worst; per-criterion score + explanation; fix suggestion on cards with any criterion < 3; priority fix callout; re-audit CTA | Tap card → Disagree modal |
| Disagree modal open | Tap any photo card | Modal: photo, 5 criteria raw scores + raw signal text, optional feedback text field | Close modal, submit feedback |

---

## 8. Data Model

All data is session-scoped and ephemeral. Nothing is written to a persistent database. The signed session cookie holds `session_id`, `payment_status`, and a compressed JSON payload of `PhotoResult[]` (thumbnails base64, results text). Session TTL: 2 hours.

### Session (cookie-stored)

| Field | Type | Description |
|-------|------|-------------|
| `session_id` | `string (uuid)` | Unique session identifier |
| `status` | `enum: uploading \| moderating \| analyzing \| scored \| paid \| error` | Current pipeline stage; drives UI state |
| `payment_status` | `enum: unpaid \| paid` | Determines whether Screen 6 is accessible |
| `stripe_payment_intent_id` | `string \| null` | Set when payment intent created; verified on confirmation |
| `photos` | `PhotoResult[]` | Array of scored photo results (see below) |
| `priority_fix_photo_id` | `string \| null` | photo_id of the photo with the lowest aggregate score |
| `created_at` | `number (unix ms)` | Session creation timestamp |
| `expires_at` | `number (unix ms)` | Session expiry (created_at + 2h) |

### PhotoResult

| Field | Type | Description |
|-------|------|-------------|
| `photo_id` | `string (uuid)` | Unique ID for this photo in the session |
| `original_filename` | `string` | For display only, not used post-upload |
| `thumbnail_base64` | `string` | Server-generated 200×200 JPEG thumbnail, base64; never stored to disk |
| `moderation_status` | `enum: passed \| rejected` | Result of moderation check |
| `rejection_reason` | `string \| null` | Plain-language reason if rejected (e.g. "More than one face detected") |
| `aggregate_score` | `number (1.0–5.0)` | Average of the 5 criterion scores, rounded to 1 decimal |
| `is_best` | `boolean` | True for the photo with highest aggregate_score |
| `is_worst` | `boolean` | True for the photo with lowest aggregate_score |
| `criteria` | `CriteriaScores` | See below |
| `fix_suggestion` | `string \| null` | Paid only. Specific one-sentence fix if any criterion score < 3. Null if all criteria ≥ 3. |
| `is_priority_fix` | `boolean` | Paid only. True if this photo is the one the user should fix first (lowest aggregate). |

### CriteriaScores

Each criterion field is a `CriterionResult`:

| Field | Type | Description |
|-------|------|-------------|
| `score` | `number (1–5)` | Integer score from vision model |
| `passed` | `boolean` | True if score ≥ 3 (used for free-tier checkmark display) |
| `raw_signal` | `string` | Exact one-line text output from the model (always present, shown in disagree modal on free tier and in full on paid tier) |

Five criteria fields: `lighting_quality`, `face_visibility`, `focus_sharpness`, `framing_crop`, `background_clarity`.

**Enum / label consistency:** Criteria are always referred to by these exact names in code, API responses, UI labels, and this PRD. The design prototype shows "expression" and "style" as criteria — these are **rejected**; the source document's five criteria above are authoritative.

**Score scale:** 1–5 integer per criterion. Aggregate is a float average. The design prototype displays scores as X.X/10 — this is **rejected**; the source document's 1–5 scale is authoritative. Free-tier UI shows aggregate as "X.X / 5". Paid-tier shows per-criterion integer scores.

**Bottom tab navigation:** The design prototype shows a persistent bottom tab bar (Home, Upload, Scores, Audit). This is **rejected** for v1 — the product is a single linear session flow with no persistent navigation between stages. Users cannot navigate backward arbitrarily (no saved state to return to). A back/cancel action returns to Screen 2 only from Screen 5 (Payment Gate).

---

## 9. Screens & Acceptance Criteria

### Screen 1: Landing / Value Prop [Required v1]

**Purpose:** Explain the product clearly and get the user to upload photos.

**Visual elements:**
- Headline: "Is your dating profile photo technically good?" (or close equivalent — do not use attractiveness language)
- 3-bullet explainer: (1) what is scored and how, (2) what you get free vs. paid, (3) privacy promise
- Static example score card showing a sample photo with a score (e.g. "3.8 / 5") — clearly labeled "Example"
- "No account required" badge, "Photos deleted after analysis" badge
- Single CTA: "Audit my photos"
- Constraint callout: "Works best with solo clear-face photos — no groups, no sunglasses"

**User actions:** Tap "Audit my photos" → navigate to Screen 2.

**State changes:** None (stateless screen).

**Acceptance criteria:**
- [ ] Page renders without a session cookie (no redirect)
- [ ] CTA navigates to Screen 2
- [ ] No score language includes "attractiveness," "match," "perform better," or any platform name (Tinder, Hinge, Bumble)
- [ ] Sample score card is visually distinct from real results (labeled "Example" or equivalent)
- [ ] Privacy badge copy matches: "Photos analyzed and immediately deleted — never stored"

**Conditional states:** None.

---

### Screen 2: Upload [Required v1]

**Purpose:** Collect 3–6 valid solo clear-face photos and surface constraint violations before analysis.

**Visual elements:**
- Heading and subtext explaining the 3–6 constraint
- Drag-and-drop zone or file picker (accepts JPEG, PNG, WEBP)
- Thumbnail grid with remove button per photo
- Photo count indicator: "N / 6 photos"
- Inline constraint checklist (static reminders, not gating): "Solo photo?", "Face clearly visible?", "No sunglasses or hats?"
- Privacy notice (one line): "Analyzed and immediately deleted — never stored"
- Analyze CTA: disabled until count ≥ 3; label "Analyze [N] photos"

**User actions:**
- Add photo (drag-drop or file picker)
- Remove photo (× on thumbnail)
- Tap Analyze CTA

**State changes:**
- Adding photo: thumbnail appears in grid, count increments, CTA enables at count = 3
- Removing photo: thumbnail removed, count decrements, CTA disables if count drops below 3
- Tap Analyze: `session.status` → `uploading`; photos sent to `/api/moderate` endpoint; CTA shows spinner; page advances to Screen 3 on first moderation response

**Acceptance criteria:**
- [ ] File picker accepts only JPEG, PNG, WEBP; selecting any other format shows inline error: "File must be JPEG, PNG, or WEBP"
- [ ] Files over 10 MB rejected with inline error: "File must be under 10 MB"
- [ ] Analyze CTA is disabled (not clickable) when photo count < 3
- [ ] Analyze CTA label updates to reflect current count: "Analyze 3 photos", "Analyze 4 photos", etc.
- [ ] Add zone is hidden or disabled when count = 6
- [ ] Removing a photo removes its thumbnail immediately; count decrements
- [ ] Tapping Analyze with count 3–6 navigates to Screen 3 (after upload POST returns)

**Conditional states:**

_File error:_ When a file fails client-side validation, an inline error message appears beneath the drop zone or the rejected thumbnail. Valid photos in the grid are not affected.

_Rejection modal:_ When the server returns `moderation_status: rejected` for one or more photos, a modal appears per rejected photo (or grouped if multiple): shows photo thumbnail, plain-language `rejection_reason` (e.g. "This photo appears to show more than one person — please upload solo photos only"), and two options: "Remove photo" and (if remaining count ≥ 3) "Continue without it". The modal uses no harsh or judgmental language. After all rejections are resolved, if remaining count ≥ 3 the flow advances to Screen 3; if remaining count < 3 the user is returned to the upload state with an instruction to add more photos.

---

### Screen 3: Processing / Loading [Required v1]

**Purpose:** Hold the user during moderation and scoring (estimated 5–15 seconds total).

**Visual elements:**
- Animated progress indicator (spinner or scan animation — Optional polish: pulsing ring with scan line)
- Status message that updates per phase:
  - Phase 1 (moderation): "Checking photos…"
  - Phase 2 (scoring): "Analyzing photo N of M…"
- Privacy reminder: "Photos are deleted after analysis is complete"

**User actions:** None. Screen advances automatically on completion.

**State changes:** `session.status` progresses `moderating → analyzing → scored`; on `scored`, client auto-navigates to Screen 4.

**Acceptance criteria:**
- [ ] User cannot navigate back to Screen 2 from Screen 3 (back button is either hidden or navigates to Screen 1 / upload start)
- [ ] Status message text updates from "Checking photos…" to "Analyzing photo N of M…" when scoring begins
- [ ] N in "photo N of M" increments from 1 to total count as each photo completes scoring
- [ ] On scoring complete, client navigates to Screen 4 without user action
- [ ] On API error (moderation or scoring), screen shows error message with "Try again" CTA that returns user to Screen 2

**Conditional states:**

_Error:_ `session.status = error`. Shows "Something went wrong — please try again" with a CTA back to Screen 2. Original photos are not retained; user must re-upload.

---

### Screen 4: Free Results — Score Reveal [Required v1]

**Purpose:** Show per-photo scores to create desire for the paid explanations.

**Visual elements:**
- One score card per photo, ordered by `aggregate_score` descending (best first)
- Each card: photo thumbnail, aggregate score (e.g. "4.2 / 5"), criterion checkmark row (5 icons: ✓ for `passed: true`, ✗ for `passed: false` — no text labels, no raw signal text on this tier)
- Best photo card: "Your technical best" badge
- Worst photo card: "Needs work" badge
- Unlock CTA: "Unlock full explanations — $5" (sticky bottom or inline below last card)
- Each card is tappable (opens Disagree modal)

**User actions:**
- Tap any score card → opens Disagree / Raw Breakdown Modal
- Tap Unlock CTA → navigates to Screen 5

**State changes:**
- Tap card: modal opens, no session state change
- Tap Unlock: `session.status` remains `scored`; Stripe payment intent created server-side

**Acceptance criteria:**
- [ ] Exactly one card shows "Your technical best" badge (`is_best: true`)
- [ ] Exactly one card shows "Needs work" badge (`is_worst: true`)
- [ ] If only 3 photos are submitted and 2 have identical aggregate scores, best/worst assignment is deterministic (first by insertion order as tiebreak)
- [ ] Criterion checkmark row shows exactly 5 icons per card — one per criterion in order: lighting, face visibility, focus, framing, background
- [ ] Checkmark row shows ✓ for `passed: true` (score ≥ 3) and ✗ for `passed: false` (score < 3) — no text reasons visible on this screen
- [ ] Raw signal text is NOT visible on this screen (text is present in session data but not rendered here)
- [ ] Unlock CTA button text is exactly "Unlock full explanations — $5"
- [ ] Tapping any score card opens the Disagree / Raw Breakdown Modal for that photo
- [ ] Tapping Unlock CTA navigates to Screen 5

**Conditional states:** None beyond default.

**Disagree / Raw Breakdown Modal (triggered from Screen 4 and Screen 6):**

Purpose: Surface exact model output when a user disputes a score, preventing trust collapse.

Visual elements:
- Photo thumbnail
- Per-criterion row: criterion name, integer score (1–5), `raw_signal` text (exact model output, no softening)
- Close button
- Optional: free-text "Tell us what you think" field (Optional polish)

Acceptance criteria:
- [ ] Modal shows all 5 criteria with their integer score and exact `raw_signal` text
- [ ] `raw_signal` text is the unmodified string from the model output — no rewording
- [ ] Modal is accessible from Screen 4 (free tier) without payment
- [ ] Close button dismisses modal and returns to the originating screen and card position

---

### Screen 5: Payment Gate [Required v1]

**Purpose:** One-time $5 charge to unlock full explanations.

**Visual elements:**
- Price: "$5 — one-time"
- What's included: "Full explanation for every photo · One specific fix per weak photo · Priority fix order"
- Stripe card element (embedded form)
- "Cancel and keep scores" link (returns to Screen 4)
- "No account created" note

**User actions:**
- Enter card details and submit payment
- Cancel → return to Screen 4

**State changes:**
- Submit payment: Stripe payment intent confirmed → `session.payment_status = paid` → auto-navigate to Screen 6
- Cancel: navigate to Screen 4, `session.payment_status` remains `unpaid`

**Acceptance criteria:**
- [ ] Stripe payment element renders in the page (not a redirect to Stripe-hosted page)
- [ ] Submit button is disabled until Stripe element reports card details are complete
- [ ] On payment success (Stripe `paymentIntent.status === 'succeeded'`), `session.payment_status` is set to `paid` and user navigates to Screen 6
- [ ] On Stripe decline, inline error from Stripe SDK is displayed below the card form; user can retry without re-uploading
- [ ] "Cancel and keep scores" navigates back to Screen 4; scores remain visible
- [ ] Direct navigation to Screen 6 URL without `payment_status: paid` redirects to Screen 4

---

### Screen 6: Paid Results — Full Audit [Required v1]

**Purpose:** Deliver the complete audit with explanations and actionable fixes.

**Visual elements:**
- Per-photo cards sorted by `aggregate_score` descending (best → worst)
- Each card: thumbnail, aggregate score, per-criterion row showing criterion name + integer score + `raw_signal` explanation text
- Fix suggestion block on any card where `fix_suggestion` is non-null: "Fix: [fix_suggestion]"
- Priority fix callout at top of the highest-priority card (`is_priority_fix: true`): "Start here — [fix_suggestion]"
- Disagree button on each card (thumbs-down icon or equivalent)
- Re-audit CTA at bottom (Optional polish): "Retook your photos? Audit again — $5" → navigates to Screen 2 (new session)

**User actions:**
- Tap Disagree on any card → opens Disagree / Raw Breakdown Modal
- Tap Re-audit CTA → navigates to Screen 2, current session expires

**State changes:**
- Tap Disagree: modal opens (no state change)
- Tap Re-audit: `session` cleared, new session started, navigate to Screen 2

**Acceptance criteria:**
- [ ] Screen 6 is only accessible when `session.payment_status === 'paid'`; direct URL access without paid status redirects to Screen 4
- [ ] All 5 per-criterion `raw_signal` texts are visible on each card (not hidden behind a tap)
- [ ] `fix_suggestion` block appears only on cards where `fix_suggestion` is non-null (i.e., at least one criterion scored < 3)
- [ ] Exactly one card shows the "Start here" priority fix callout (`is_priority_fix: true`)
- [ ] Cards are ordered by `aggregate_score` descending; ties broken by `photo_id` insertion order
- [ ] Disagree modal is accessible from every card on this screen
- [ ] Re-audit CTA, when tapped, clears the current session and navigates to Screen 2

---

## 10. External Integrations

**Service:** OpenAI Vision API (GPT-4o)
**Purpose:** Single-pass scoring of each photo on 5 criteria
**Specifics:**
- One API call per photo with the image encoded as base64 in the message
- System prompt defines the 5 criteria, the 1–5 integer scoring scale, and the required JSON output schema: `{ "criteria": { "lighting_quality": { "score": int, "raw_signal": string }, ... }, "fix_suggestion": string | null }`
- Temperature set to 0 for consistency
- Images resized to max 1024px on longest edge server-side (via `sharp`) before encoding — reduces token cost and latency
- **Risk:** Model output format must be strictly validated; malformed JSON triggers a retry (max 1 retry); on second failure the photo is marked with a scoring error and the session continues with remaining photos
- Prompt must explicitly instruct the model to score technical criteria only, never reference attractiveness, dating outcomes, or platform suitability

**Service:** OpenAI Moderation API (or AWS Rekognition)
**Purpose:** Reject NSFW photos and photos with no detected face or multiple detected faces
**Specifics:**
- OpenAI moderation endpoint (`POST /v1/moderations`) for NSFW detection — free, synchronous, fast
- Face count check: either (a) AWS Rekognition `DetectFaces` API (returns bounding boxes per face; reject if count ≠ 1) or (b) a separate GPT-4o Vision call with a face-count prompt if Rekognition cost is not justified at low volume
- Moderation runs before scoring; photos failing moderation never reach the vision model
- Rejection reasons mapped to plain language before returning to client (never expose raw API error strings to users)
- **Decision to make before build:** benchmark Rekognition vs. Vision prompt on 20 borderline photos (sunglasses, partial faces, mirror selfies with background people) and commit to one approach

**Service:** Stripe
**Purpose:** One-time $5 payment to unlock full audit explanations
**Specifics:**
- Server creates a `PaymentIntent` for $5 USD when user taps Unlock on Screen 4
- Client-side: Stripe Elements (`CardElement` or `PaymentElement`) embedded in Screen 5
- On client confirmation (`stripe.confirmCardPayment`), `paymentIntent.status` is checked; on `succeeded`, an API route sets `session.payment_status = paid`
- No webhook required for v1 — client polls `stripe.retrievePaymentIntent` after confirmation
- No subscription, no customer object, no saved payment method
- Stripe secret key stored in environment variable only; never exposed to client

---

## 11. Open Risks & Assumptions to Verify Before Build

- **Risk/Assumption:** A vision model can reliably score the 5 technical criteria across diverse photo types (skin tones, ages, lighting conditions, intentional style choices) in a way users accept as credible. — **What to verify:** Run 30–50 diverse sample photos (including intentionally "stylish but technically imperfect" shots — moody shadows, tight crops, dramatic backlight) through a draft prompt before writing any UI code. Establish an acceptance threshold: ≥ 80% of scores feel reasonable on manual review. If not, the product ships a polished random-number generator.

- **Risk/Assumption:** Showing per-photo aggregate scores for free creates sufficient desire to convert users to the $5 paid explanation. — **What to verify:** Track free-to-paid conversion in the first 50 sessions. If < 5% convert, test showing one free per-criterion explanation before the gate, or lowering the price to $2–3. Do not optimize the gate before seeing real conversion data.

- **Risk/Assumption:** Moderation correctly handles borderline cases: mirror selfies with visible bystanders in background, heavy filters, partial face (chin cut off), sunglasses outdoors, faces at profile angle. — **What to verify:** Build a test set of 20 borderline images and run both Rekognition and Vision-prompt approaches before committing. Define explicit rejection thresholds (e.g. Rekognition FaceDetail Confidence threshold). False positives (rejecting valid photos) damage trust before the user reaches the paywall.

- **Risk/Assumption:** The session cookie (signed, HttpOnly) can hold the full result payload (6 photos × 5 criteria × ~100-char raw signal text + thumbnails) within the 4 KB cookie size limit. — **What to verify:** Calculate payload size with 6 photos before choosing cookie-only storage. If payload exceeds 4 KB even with base64 thumbnails omitted from cookie and served from a temp in-memory store, add Redis (Upstash free tier) as a session backing store. Thumbnails should not be stored in the cookie — serve them from a signed 10-minute URL or include them in the initial score response only.

- **Risk/Assumption:** Per-session API cost (moderation + 3–6 vision calls) leaves adequate margin at $5 price point. — **What to verify:** Run cost calculation before launch: GPT-4o vision input cost per 1024px image × 6 photos + moderation call + output tokens. If break-even is > $3.50 per session, either raise the price or reduce image resolution further.

- **Risk/Assumption:** Users will trust uploading their personal dating photos to an unknown web app with no account. — **What to verify:** Privacy copy alone does not solve this. Verify by including a "How does this work?" one-paragraph explainer (processing pipeline, deletion timing) accessible from the upload screen. Monitor bounce rate on Screen 2 — if > 60% of landing visitors who reach Screen 2 abandon without uploading, the trust barrier is too high.

---

## 12. Visual Reference

- **Live prototype (static mock — visual layout reference only):** https://jakebuild.github.io/idea-factory/38-ai-photo-ranker-for-attractive/
- **Screen files on GitHub:** https://github.com/jakebuild/idea-factory/tree/gh-pages/38-ai-photo-ranker-for-attractive
- **Source idea (authoritative):** https://github.com/jakebuild/idea-factory/blob/main/38-ai-photo-ranker-for-attractive/idea-v3.md
- **Source critique:** https://github.com/jakebuild/idea-factory/blob/main/38-ai-photo-ranker-for-attractive/critique-v3.md

**Design deviations explicitly adopted from prototype:**
- Dark navy/slate color scheme (#0a0f1a, #0d1424) with blue accent (#3b82f6) — adopted
- Card-based layout for score results — adopted
- Scan animation on loading screen — adopted as Optional polish

**Design deviations explicitly rejected (source document is authoritative):**
- Prototype score scale X.X/10 → **rejected**; use 1–5 per criterion with float aggregate displayed as X.X / 5
- Prototype criteria "expression" and "style" → **rejected**; use the 5 criteria from source doc: lighting quality, face visibility, focus/sharpness, framing/crop, background clarity
- Prototype "12 distinct aesthetic vectors" language → **rejected**; copy must state "5 technical criteria"
- Prototype bottom tab navigation bar (Home, Upload, Scores, Audit) → **rejected**; use a linear session flow, no persistent tab bar

---

## 13. Suggested Folder Structure

```
/
├── app/
│   ├── page.tsx                    # Screen 1: Landing
│   ├── upload/
│   │   └── page.tsx                # Screen 2: Upload
│   ├── processing/
│   │   └── page.tsx                # Screen 3: Processing
│   ├── results/
│   │   ├── page.tsx                # Screen 4: Free Results
│   │   └── full/
│   │       └── page.tsx            # Screen 6: Paid Results
│   ├── pay/
│   │   └── page.tsx                # Screen 5: Payment Gate
│   └── api/
│       ├── session/
│       │   └── route.ts            # GET/POST session state
│       ├── moderate/
│       │   └── route.ts            # POST: NSFW + face count check
│       ├── score/
│       │   └── route.ts            # POST: vision model scoring
│       ├── payment-intent/
│       │   └── route.ts            # POST: create Stripe PaymentIntent
│       └── confirm-payment/
│           └── route.ts            # POST: verify payment, set paid status
├── components/
│   ├── ScoreCard.tsx               # Photo card with score + criterion row
│   ├── DisagreeModal.tsx           # Raw breakdown modal
│   ├── CriterionRow.tsx            # 5-criterion checkmark / score row
│   ├── UploadGrid.tsx              # Drag-drop grid with thumbnails
│   ├── RejectionModal.tsx          # Inline rejection handling
│   └── StripePaymentForm.tsx       # Stripe Elements wrapper
├── lib/
│   ├── session.ts                  # iron-session config + helpers
│   ├── openai.ts                   # Vision scoring + moderation calls
│   ├── rekognition.ts              # AWS face detection (or omit if Vision-only)
│   ├── stripe.ts                   # Stripe server-side helpers
│   └── scoring.ts                  # Score aggregation, best/worst logic
├── types/
│   └── index.ts                    # Session, PhotoResult, CriteriaScores types
├── .env.local                      # OPENAI_API_KEY, STRIPE_SECRET_KEY, SESSION_SECRET, AWS_* 
└── next.config.ts
```
