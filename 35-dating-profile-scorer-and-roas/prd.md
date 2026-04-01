# PRD: HingeFix

## 1. Overview

HingeFix is a one-shot Hinge profile optimization tool. Users paste their bio and prompt answers into a Hinge-aware structured editor, pay $12.99 once, and receive a four-dimension audit plus one rewritten bio and three rewritten prompt answers in under 60 seconds. There are no accounts, no subscriptions, no photos, and no free tier. The product's differentiator is structural: Hinge's actual prompt categories are hardcoded as a dropdown, fields enforce Hinge's real character limits, and an inline cliché detector flags banned phrases before submission — making "Hinge-specific" tangible to the user, not just a pitch claim.

---

## 2. Target User

Active Hinge users (25–35) who are getting few matches or poor-quality conversations and suspect their profile copy is the problem. They have already tried self-editing but hit a wall — they are too close to their own words to see what reads as generic. Existing solutions fail them: ChatGPT has no Hinge-specific format awareness, generic "dating profile tips" content is vague, and professional profile writers are $100+. This user wants a credible, fast, affordable fix — not a subscription.

---

## 3. Tech Stack

**Platform:** Web (mobile-first responsive, no native app)

**Language + Framework:** TypeScript + Next.js 14 (App Router)

- Next.js API routes handle the LLM call and email dispatch server-side — no separate backend needed
- Server Components for the landing page (SEO-critical); Client Components for the interactive profile editor

**Key Libraries:**
- `@stripe/stripe-js` + `stripe` (Node) — Stripe Checkout for payment; webhook to gate result generation
- `openai` SDK — GPT-4o for audit + rewrite generation (single structured prompt, one API call per paid session)
- `@upstash/redis` (Vercel KV) — store results JSON by UUID token; TTL 7 days
- `resend` — transactional email (receipt + tokenized results link)
- `nanoid` — generate result token
- Tailwind CSS — matches prototype design system (utility-first, no separate CSS files)
- `@radix-ui/react-select` — accessible Hinge prompt category dropdown

**Hosting / Deployment:** Vercel (zero-config Next.js, built-in KV, serverless functions within free/hobby tier for MVP)

**Why this stack:** Next.js on Vercel is the fastest solo-dev path from zero to deployed for a web-only, text-in/text-out product. API routes eliminate a separate backend. Vercel KV is the minimum viable stateful layer for tokenized result links without a full database. Stripe Checkout removes PCI scope. GPT-4o is the best single-call option for structured JSON output. Total infrastructure cost at launch is near zero.

---

## 4. V1 Scope

- **Required v1** — Landing page with real before/after profile examples and single CTA
- **Required v1** — Profile input editor: bio field (150-char limit), up to 6 prompt-answer pairs (Hinge prompt dropdown + 150-char answer field per pair), minimum 1 prompt pair required
- **Required v1** — Inline cliché detector: flags hardcoded banned phrases in bio and prompt answer fields as user types; highlights in yellow with label
- **Required v1** — Stripe one-time payment ($12.99) via Stripe Checkout; no account creation
- **Required v1** — LLM-generated structured results: 4-dimension audit (First Impression, Personality Signal, Specificity, Conversation Starter Potential) + 1 best bio rewrite + 3 best prompt rewrites, each with rationale
- **Required v1** — Results page with copy-to-clipboard per output (bio + each prompt answer)
- **Required v1** — Tokenized results URL stored in Vercel KV (7-day TTL); no login required to revisit
- **Required v1** — Transactional email receipt with tokenized results link (Resend)
- **Optional polish** — "Copy All Rewrites" button on Results — Rewrites screen
- **Optional polish** — "Email me these results" inline field on Results — Rewrites screen (submits email post-payment to send the same tokenized link)
- **Optional polish** — Bottom tab bar navigation between Checkout / Audit / Rewrites screens (matches prototype; linear CTA flow is the Required v1 fallback)

---

## 5. Out of Scope

- Free audit tier or teaser output
- Photo upload or photo optimization
- 1–10 score display or roast card / share mechanic
- Multiple bio variants (3 bios)
- Email drip / reminder sequences
- Returning-user skip-audit flow
- Account system or saved profiles
- Support for Bumble, Tinder, or any non-Hinge app
- Stripe subscription or recurring billing
- Admin dashboard or analytics UI
- Refund or support ticket flow

---

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Landing Page | Required v1 |
| 2 | Profile Input | Required v1 |
| 3 | Checkout | Required v1 |
| 4 | Results — Audit | Required v1 |
| 5 | Results — Rewrites | Required v1 |

> **Design reference note:** The live prototype at `jakebuild.github.io` shows 4 screens numbered 1–4 (Profile Input, Checkout, Results Audit, Results Rewrites). The Landing Page (Screen 1 in this PRD) is **absent** from the prototype — it must be built from the idea-v3.md spec, not derived from the prototype. Screen numbers in this PRD add 1 to each prototype screen number to accommodate Screen 1 (Landing Page). All references below use this PRD's numbering exclusively.
>
> **Prototype discrepancy — rejected:** The Results — Audit screen in the prototype includes a "Main photo shadowed, looks distant" audit finding. Photos are explicitly out of scope. The audit covers bio and prompts only. Any photo reference in the prototype visual is rejected and must not appear in the implementation.
>
> **Prototype element — adopted:** The 4-tab bottom nav bar (Profile / Checkout / Audit / Rewrites) shown in the prototype is adopted as **Optional polish**. The Required v1 navigation model is linear: each screen has a single forward CTA and a back button where applicable.

---

## 7. State Model

### Screen 1: Landing Page
| State | Trigger | Shows | Actions |
|---|---|---|---|
| `default` | Any visit | Hero, before/after examples, CTA | "Fix My Profile" → navigate to Screen 2 |

### Screen 2: Profile Input
| State | Trigger | Shows | Actions |
|---|---|---|---|
| `editing` | Initial load | Bio field empty, 0 prompt pairs added; CTA disabled | Type bio, add prompt pair, select category, type answer |
| `has-cliché` | Flagged phrase detected in bio or any answer field | Yellow highlight on offending text, cliché label chip above field | User can proceed; detector is advisory, not blocking |
| `ready-to-checkout` | Bio non-empty AND at least 1 prompt pair has category selected + answer non-empty | CTA enabled: "Continue to Checkout — $12.99" | Tap CTA → navigate to Screen 3 with input payload in session storage |
| `over-limit` | Character count exceeds 150 for bio or any answer | Counter turns red; CTA remains disabled until fixed | Trim text |

### Screen 3: Checkout
| State | Trigger | Shows | Actions |
|---|---|---|---|
| `idle` | Arrived from Screen 2 | Order summary, email field, Stripe card form, Pay button | Enter email, enter card details, tap Pay |
| `processing` | Pay button tapped, Stripe JS processing | Pay button disabled/loading spinner | Wait |
| `error` | Stripe returns payment failure | Inline error message below Pay button; form re-enabled | Retry payment |
| `success` | Stripe webhook confirms payment; server generates results and stores token | Redirect to Screen 4 via tokenized URL | — |

### Screen 4: Results — Audit
| State | Trigger | Shows | Actions |
|---|---|---|---|
| `loading` | Token URL loaded, LLM call in flight (if not yet cached) | Skeleton cards or spinner | Wait |
| `ready` | Results JSON present in KV for token | 4 audit dimension cards | "Your Rewrites →" CTA → navigate to Screen 5 |
| `expired` | Token not found in KV (TTL elapsed) | "Results expired" message with support email | — |
| `error` | LLM call failed or malformed response | "Something went wrong" message with support email | — |

### Screen 5: Results — Rewrites
| State | Trigger | Shows | Actions |
|---|---|---|---|
| `default` | Arrived from Screen 4 or direct token URL | Bio rewrite card + 3 prompt rewrite cards | Copy per item, Copy All (optional polish), Email field (optional polish) |
| `item-copied` | Copy button tapped on individual item | Button label changes to "Copied ✓" for 2 seconds, then resets | — |
| `all-copied` | "Copy All" tapped (optional polish) | Button label changes to "Copied ✓" for 2 seconds | — |
| `email-submitted` | Email entered and "Send" tapped on optional email field | Send button changes to "Sent ✓", field disabled | — |

---

## 8. Data Model

### Entity: ProfileInput *(ephemeral — session storage only, never persisted to server)*
| Field | Type | Description |
|---|---|---|
| `bio` | `string` | User's Hinge bio text; max 150 chars |
| `prompts` | `PromptPair[]` | 1–6 prompt-answer pairs |

### Entity: PromptPair *(child of ProfileInput)*
| Field | Type | Description |
|---|---|---|
| `category` | `string` | Selected value from `HINGE_PROMPTS` enum (see below) |
| `answer` | `string` | User's answer text; max 150 chars |

### Entity: ResultsRecord *(persisted in Vercel KV, key = UUID token)*
| Field | Type | Description |
|---|---|---|
| `token` | `string` | `nanoid(21)` UUID — forms the results URL path |
| `createdAt` | `number` | Unix timestamp ms |
| `email` | `string` | Receipt email captured at checkout |
| `stripeSessionId` | `string` | Stripe Checkout Session ID for reference |
| `audit` | `AuditDimension[4]` | Exactly 4 audit objects |
| `bioRewrite` | `BioRewrite` | 1 rewritten bio object |
| `promptRewrites` | `PromptRewrite[3]` | 3 rewritten prompt objects |

### Entity: AuditDimension *(child of ResultsRecord.audit)*
| Field | Type | Description |
|---|---|---|
| `dimension` | `"first_impression" \| "personality_signal" \| "specificity" \| "conversation_starter"` | Fixed enum |
| `label` | `string` | Display label ("First Impression", "Personality Signal", "Specificity", "Conversation Starter Potential") |
| `verdict` | `string` | One paragraph — AI generated |
| `fix` | `string` | One bolded concrete fix sentence — AI generated |

### Entity: BioRewrite *(child of ResultsRecord)*
| Field | Type | Description |
|---|---|---|
| `text` | `string` | Rewritten bio; respects 150-char Hinge limit |
| `rationale` | `string` | 2-sentence rationale: what changed and why |

### Entity: PromptRewrite *(child of ResultsRecord.promptRewrites)*
| Field | Type | Description |
|---|---|---|
| `originalCategory` | `string` | The prompt category label from input |
| `originalAnswer` | `string` | The user's original answer |
| `newAnswer` | `string` | Rewritten answer; respects 150-char Hinge limit |
| `rationale` | `string` | 2-sentence rationale: what changed and why |

### Entity: EmailResultsRequest *(ephemeral — fired client-side, optional polish)*
| Field | Type | Description |
|---|---|---|
| `email` | `string` | Email entered on Screen 5 optional field |
| `token` | `string` | Current results token from URL |

This maps to the `email-submitted` state on Screen 5: when the user submits the optional email field, the `email_submitted` boolean in client state is set to `true`, disabling the field and showing "Sent ✓" on the button. No new field is persisted to the ResultsRecord — Resend sends the same tokenized link already used for the receipt.

### Enum: HINGE_PROMPTS *(hardcoded client-side array, ordered alphabetically)*
```
"A life goal of mine"
"A shower thought I had recently"
"All I ask is that you"
"Best travel story"
"Dating me is like"
"First round is on me if"
"Give me travel tips for"
"I go crazy for"
"I geek out on"
"I'm convinced that"
"I'm looking for"
"I want someone who"
"I'll know it's time to delete Hinge when"
"My most controversial opinion"
"My simple pleasures"
"Never have I ever"
"The award I should be nominated for is"
"The key to my heart is"
"The way to win me over is"
"Two truths and a lie"
"Typical Sunday"
"We're the same if"
"Worst idea I've ever had"
"You should not date me if"
```
> **Maintenance note:** Hinge periodically changes prompt options. Whoever owns this product must audit the enum against the live Hinge app before launch and budget 30 minutes/quarter to keep it current. This is an explicit maintenance obligation, not a one-time task.

### Constant: CLICHÉ_PHRASES *(hardcoded client-side array, matched case-insensitively)*
```
"fluent in sarcasm"
"love to laugh"
"loves to laugh"
"here for a good time"
"good vibes only"
"good vibes"
"work hard play hard"
"partner in crime"
"looking for my partner in crime"
"not here for hookups"
"swipe left if"
"not looking for anything serious"
"foodie"
"sapiosexual"
"fluent in"
"live laugh love"
"adventure is out there"
"lover of life"
"young professional"
```

---

## 9. Screens & Acceptance Criteria

---

### Screen 1: Landing Page [Required v1]

**Purpose:** Convert cold visitors into paying users by showing credible before/after profile examples and presenting a single clear CTA.

**Visual elements:**
- Full-width hero section: bold headline ("Your Hinge profile is costing you matches"), subheadline ("Get a full audit + rewritten bio and prompts in under 60 seconds — $12.99, one time"), primary CTA button ("Fix My Profile")
- 2–3 before/after example cards below the fold: each shows original prompt/bio text on the left, rewritten version on the right, with improvements visually highlighted (strikethrough or diff-style)
- Brief "What you get" summary: 3 bullet points (Full audit · 1 rewritten bio · 3 rewritten prompt answers)
- Single price displayed prominently: "$12.99 one-time — no subscription, no account"
- No nav links, no footer clutter, no free tier mention
- Color system and typography matching prototype: primary `#b52330`, headline font "Plus Jakarta Sans", body font "Manrope"

**User actions:**
- Tap "Fix My Profile" CTA → navigate to Screen 2

**State changes:** None — stateless page.

**Acceptance criteria:**
- [ ] Tapping "Fix My Profile" navigates to Screen 2 (Profile Input)
- [ ] Page contains no mention of a free tier, free audit, or free sample
- [ ] Price "$12.99" appears at least once above the fold on a 390px viewport
- [ ] At least 2 before/after example pairs are visible below the fold
- [ ] Page renders correctly on 390px (iPhone 14) and 1280px (desktop) viewports
- [ ] Page has a `<title>` and `<meta name="description">` for SEO

**Conditional states:** None — this screen has no loading or error states.

> **Note:** Before/after examples must use real (anonymized, consented) Hinge profile content. Using fabricated examples is a trust risk; source genuine rewrites from manual test sessions before launch.

---

### Screen 2: Profile Input [Required v1]

**Purpose:** Collect the user's Hinge bio and prompt answers in a Hinge-aware structured editor before payment.

**Visual elements:**
- Screen header: "Profile Input" title, no back button (first step)
- **Bio section:**
  - Label: "The Short Bio"
  - Textarea with placeholder: "Paste your Hinge bio here…"
  - Live character counter: "N / 150" — counter text turns red when N > 150
  - Cliché detection chip: appears above the textarea when a flagged phrase is detected; label: "Cliché Detected: '[phrase]'" in error/warning color with yellow highlight on matching text in textarea; dismisses when phrase is removed
- **Prompt section:**
  - "Add a Prompt" button adds a new prompt-answer pair (up to 6 pairs)
  - Each pair: Hinge prompt dropdown (populated from `HINGE_PROMPTS` enum, placeholder "Select a Hinge prompt") + answer textarea with live character counter "N / 150" + cliché chip per answer field
  - Remove button (×) on each pair (hidden if only 1 pair remains)
- **CTA:** Full-width primary button "Continue to Checkout — $12.99" with forward arrow icon; disabled (visually muted, not tappable) until: bio is non-empty AND at least 1 prompt pair has both a selected category and a non-empty answer AND no field exceeds 150 characters
- Bottom tab bar (Optional polish): 4 tabs — Profile (active), Checkout, Audit, Rewrites
- Colors and typography: match prototype Screen 1 design system

**User actions:**
- Type into bio textarea → character counter updates, cliché detector runs
- Tap "Add a Prompt" → new prompt-answer pair appended
- Select prompt category from dropdown → field activates
- Type into prompt answer → character counter updates, cliché detector runs
- Tap remove (×) on a prompt pair → pair removed
- Tap CTA (when enabled) → profile input serialized to session storage, navigate to Screen 3

**State changes:**
- `bio` field in session ProfileInput updated on each keystroke
- Each `PromptPair.category` and `PromptPair.answer` updated on change
- CTA enabled/disabled state derived from validation conditions above (no persisted state needed)

**Acceptance criteria:**
- [ ] Bio character counter displays "N / 150" and updates on every keystroke
- [ ] Counter text color is default (non-red) when N ≤ 150 and red when N > 150
- [ ] CTA is disabled when bio is empty
- [ ] CTA is disabled when no prompt pair has both category selected and answer non-empty
- [ ] CTA is disabled when any field (bio or answer) exceeds 150 characters
- [ ] CTA is enabled when bio is non-empty, at least 1 complete prompt pair exists, and no field exceeds 150 chars
- [ ] At least one phrase from `CLICHÉ_PHRASES` (e.g. "fluent in sarcasm") typed into bio triggers the cliché chip above the bio textarea
- [ ] At least one phrase from `CLICHÉ_PHRASES` typed into a prompt answer triggers the cliché chip above that answer field
- [ ] Cliché chip disappears when the flagged phrase is removed from the field
- [ ] Cliché detection does not block CTA — user can proceed with flagged phrases present
- [ ] Dropdown is populated with all entries from `HINGE_PROMPTS` enum in the order specified
- [ ] "Add a Prompt" adds a new empty pair; up to 6 pairs can exist simultaneously
- [ ] Remove (×) button removes the corresponding pair; button is hidden when only 1 pair exists
- [ ] Tapping CTA serializes `{bio, prompts}` to `sessionStorage` key `hingefix_input` and navigates to Screen 3
- [ ] Navigating back from Screen 3 to Screen 2 restores previously entered values from `sessionStorage`

**Conditional states:**
- **Empty (initial load):** Bio textarea empty, no prompt pairs visible, CTA disabled, "Add a Prompt" button shown prominently
- **Over-limit:** Character counter red, CTA disabled; no additional error message needed beyond counter color

---

### Screen 3: Checkout [Required v1]

**Purpose:** Capture payment and email via Stripe Checkout, then trigger LLM result generation.

**Visual elements:**
- Screen header: "Checkout" title, back button (arrow_back icon) → navigates back to Screen 2
- Subheading: "Finalize Your Profile Audit"
- **Order summary card:**
  - Title: "Order Summary"
  - Line item: "Professional Profile Audit — $12.99"
  - Checklist (check_circle icons): "1 × Optimized Bio", "3 × Custom Prompt Rewrites", "4-Dimension Audit"
  - Total row: "Total Due  $12.99"
- **Payment form:**
  - Email field: label "Email for Receipt", required
  - Stripe Elements card form (card number, MM/YY, CVC) OR redirect to Stripe Checkout (hosted page) — see implementation note below
  - Pay button: "🔒 Pay $12.99"
  - Security badge: verified_user icon + "Secure SSL Encryption"
- No subscription upsell, no account creation prompt
- Bottom tab bar (Optional polish): Checkout tab active

> **Implementation note:** Stripe Checkout (hosted, redirect-based) is preferred over Stripe Elements (embedded form) for v1. It removes card form implementation, PCI complexity, and error-handling from scope. The email field is collected on Stripe's hosted page. If the design requires the embedded form shown in the prototype, use Stripe Elements — but account for the additional implementation time (~1 day).

**User actions:**
- Tap back → navigate to Screen 2 (input preserved in sessionStorage)
- Enter email + card details → tap Pay
- On Stripe success callback → redirect to `/results/[token]` (Screen 4)

**State changes:**
- On payment success: server-side API route receives Stripe webhook, reads ProfileInput from session, calls LLM, stores ResultsRecord in Vercel KV by token, returns redirect URL
- `ResultsRecord.email`, `ResultsRecord.stripeSessionId`, `ResultsRecord.token`, `ResultsRecord.createdAt` all set at this point

**Acceptance criteria:**
- [ ] Back button on Screen 3 navigates to Screen 2 with previously entered profile data still populated
- [ ] Pay button is disabled until email field is non-empty and Stripe form is complete
- [ ] Successful payment redirects to `/results/[token]` (Screen 4)
- [ ] Failed payment (declined card) displays an inline error message below the Pay button and re-enables the form
- [ ] ResultsRecord is created in Vercel KV only after Stripe webhook confirms `payment_intent.succeeded` (not on client-side redirect alone)
- [ ] Email receipt is sent to the address provided within 60 seconds of payment confirmation
- [ ] No account is created; no password is set; no session cookie beyond the results token is set
- [ ] Order summary shows exactly: "1 × Optimized Bio", "3 × Custom Prompt Rewrites", "4-Dimension Audit" — no other line items

**Conditional states:**
- **Processing:** Pay button shows loading indicator, form inputs disabled
- **Error:** Stripe decline message shown inline; form re-enabled for retry
- **Back-navigated:** sessionStorage `hingefix_input` restores Screen 2 state

---

### Screen 4: Results — Audit [Required v1]

**Purpose:** Display the four-dimension profile audit after payment.

**Visual elements:**
- Screen header: "Profile Audit Results"
- Tagline line below header: AI-generated one-sentence diagnosis (e.g. "High Potential, Low Signal")
- **4 audit dimension cards** displayed vertically, in fixed order:
  1. First Impression (visibility icon)
  2. Personality Signal (psychology icon)
  3. Specificity (target icon)
  4. Conversation Starter Potential (forum icon)
  - Each card: icon + dimension label (bold) + one-paragraph verdict + bolded "The Fix:" line with one concrete fix
- **Transition CTA** below the 4 cards: "Your Rewrites →" button → navigates to Screen 5 (or scrolls if on same page)
- Bottom tab bar (Optional polish): Audit tab active
- All icons: Material Symbols Outlined
- Colors: match prototype design system

> **Audit scope constraint:** Audit dimensions cover bio text and prompt answers only. First Impression in this product means the first-read quality of the bio text — not photos. The prototype's reference to photo quality is rejected. LLM prompts must be constrained accordingly.

**User actions:**
- Tap "Your Rewrites →" → navigate to Screen 5
- (Optional polish) Tap tab bar → navigate between Audit and Rewrites

**State changes:** None — results are read-only.

**Acceptance criteria:**
- [ ] Exactly 4 audit cards are displayed, in fixed order: First Impression, Personality Signal, Specificity, Conversation Starter Potential
- [ ] Each card contains: dimension label, verdict paragraph, and a bolded "The Fix:" sentence
- [ ] No audit card contains any reference to photos or photos advice
- [ ] "Your Rewrites →" CTA is visible and tapping it navigates to Screen 5
- [ ] Page loads from tokenized URL `/results/[token]` without login
- [ ] If token is not found in Vercel KV (expired or invalid), page shows "Results expired or not found" with a support email address instead of audit cards
- [ ] Tagline is dynamically generated by the LLM (not hardcoded), max 10 words

**Conditional states:**
- **Loading:** Skeleton cards (or spinner) shown while LLM result is being generated; displayed only if result is not yet cached in KV when user lands on this URL. Target: LLM call completes before redirect arrives (<15s p95).
- **Expired:** Token not found in KV; shows static "Results expired" message.
- **Error:** LLM call failed or returned malformed JSON; shows "Something went wrong — email [support address] to get your results."

---

### Screen 5: Results — Rewrites [Required v1]

**Purpose:** Display the rewritten bio and 3 rewritten prompt answers with copy-to-clipboard actions.

**Visual elements:**
- Screen header: "Your Rewrites" title, back button → navigates to Screen 4
- Subtitle: "Your profile, rewritten for Hinge."
- **Bio rewrite card:**
  - Card title: "Main Bio"
  - Rewritten bio text in a visually distinct block (quoted/card style)
  - "The Strategy" section label + 2-sentence rationale text
  - Copy button: content_copy icon + "Copy" label
- **Optimized Prompts section:**
  - Section title: "Optimized Prompts" (auto_awesome icon)
  - 3 prompt rewrite cards, each containing:
    - Original prompt category label (e.g. "A shower thought I had recently")
    - Rewritten answer text
    - "The Strategy" rationale (2 sentences)
    - Copy button: content_copy icon + "Copy"
- **Footer actions:**
  - **Required v1:** No footer CTA needed beyond the 4 copy buttons above
  - **Optional polish:** Full-width "Copy All Rewrites" button
  - **Optional polish:** "Email me these results" text input + "Send" button
- Bottom tab bar (Optional polish): Rewrites tab active

**User actions:**
- Tap back → navigate to Screen 4
- Tap Copy on bio card → bio text copied to clipboard; button label changes to "Copied ✓" for 2 seconds
- Tap Copy on each prompt card → that prompt answer copied to clipboard; button label changes to "Copied ✓" for 2 seconds
- (Optional polish) Tap "Copy All Rewrites" → all 4 outputs concatenated and copied; button shows "Copied ✓" for 2 seconds
- (Optional polish) Enter email and tap Send → fires `POST /api/email-results` with `{email, token}`; button shows "Sent ✓", field disabled

**State changes:**
- `item-copied` state: client-only, no persistence; reset after 2 seconds per item
- `all-copied` state: client-only, reset after 2 seconds
- `email-submitted` state: client-only boolean, disables field + button after submission; no new KV record written

**Acceptance criteria:**
- [ ] Bio rewrite card is displayed first, followed by exactly 3 prompt rewrite cards
- [ ] Each rewrite card contains: original prompt label, rewritten text, 2-sentence rationale, Copy button
- [ ] Bio rewrite respects 150-character Hinge limit (enforced in LLM prompt output validation; if exceeded, server truncates to 150 chars before storing)
- [ ] Each prompt rewrite answer respects 150-character Hinge limit (same enforcement)
- [ ] Tapping Copy on bio copies only the bio rewrite text (not rationale, not labels)
- [ ] Tapping Copy on a prompt copies only that prompt's rewritten answer
- [ ] Copy button label changes to "Copied ✓" within 100ms of tap and resets to "Copy" after 2000ms
- [ ] Back button navigates to Screen 4
- [ ] Page loads from the same tokenized URL as Screen 4 (`/results/[token]`) — Screens 4 and 5 are sections of the same results page, differentiated by scroll position or tab state, not separate routes; **OR** they are separate routes `/results/[token]/audit` and `/results/[token]/rewrites` — implementer chooses; either is acceptable if back-navigation works correctly
- [ ] (Optional polish) "Copy All Rewrites" copies bio + all 3 prompt answers in a single clipboard payload, separated by newlines
- [ ] (Optional polish) "Email me these results" Send button is disabled until email field contains a valid email format; on success shows "Sent ✓" and disables field

**Conditional states:**
- **Expired / Error:** Same as Screen 4 — if token is invalid, show the same expiry/error message
- **Rationale loading:** If rationale text is empty (LLM omitted it), hide "The Strategy" section rather than showing an empty block

---

## 10. External Integrations

**Service:** Stripe  
**Purpose:** One-time payment of $12.99; payment confirmation gates LLM result generation  
**Specifics:**
- Use Stripe Checkout (hosted) for v1 — redirect to Stripe, redirect back on success/cancel
- Create a `price` object for the $12.99 one-time product in the Stripe dashboard
- Pass `client_reference_id` = session UUID to correlate with ProfileInput in session storage
- Listen to `checkout.session.completed` webhook; verify `payment_status === 'paid'` before generating results
- Store `stripeSessionId` in ResultsRecord
- Gotcha: Stripe webhook must be handled idempotently — duplicate delivery is possible; check KV for existing token before re-generating

**Service:** OpenAI (GPT-4o)  
**Purpose:** Generate structured audit + bio rewrite + 3 prompt rewrites in a single API call  
**Specifics:**
- Use `gpt-4o` with `response_format: { type: "json_object" }` for reliable structured output
- Single system prompt per request; include ProfileInput (bio + prompts) as user message
- Response schema must match ResultsRecord shape: `{ tagline, audit[4], bioRewrite, promptRewrites[3] }`
- Server validates: exactly 4 audit dimensions with correct enum keys, bio ≤ 150 chars, each prompt answer ≤ 150 chars; truncate if LLM exceeds limits
- Gotcha: GPT-4o latency p95 ~5–12s for this payload size; set `fetch` timeout to 30s; display loading skeleton on Screen 4 if redirect arrives before KV is populated
- Gotcha: LLM may generate rationales that sound generic or post-hoc — prompt engineering is the hard part; budget Days 5–8 as specified in idea-v3

**Service:** Vercel KV (Upstash Redis)  
**Purpose:** Store ResultsRecord by token; enables tokenized URL revisit without accounts  
**Specifics:**
- Key: `results:${token}`; value: JSON-serialized ResultsRecord
- TTL: 7 days (`EX 604800`)
- Read on every `/results/[token]` page load
- Gotcha: cold-start latency on Vercel hobby tier; use `UPSTASH_REDIS_REST_URL` env var

**Service:** Resend  
**Purpose:** Send transactional email receipt with tokenized results link  
**Specifics:**
- Single template: subject "Your HingeFix results are ready", body includes tokenized URL and plain-text summary of what was generated
- Send after `checkout.session.completed` webhook, after ResultsRecord is written to KV
- Use `from:` address on a verified domain (e.g. `results@hingefix.com`)
- Gotcha: Resend free tier allows 100 emails/day — sufficient for MVP; upgrade when volume demands
- No drip, no follow-up sequences; this is the only email sent per session

---

## 11. Open Risks & Assumptions to Verify Before Build

- **Risk: $12.99 upfront without a free sample does not convert.** Before/after landing page examples are doing all trust-building work cold. **Verify:** Run a "manual sell" test — publish a minimal landing page and offer to hand-deliver 5 rewrites manually (via DM). If zero of 20 targeted Reddit/Discord users click through or DM, the price/trust problem is real. Fallback: add a single free "top problem with your profile" teaser (1 sentence, gated behind email). Do not restore the full free audit.

- **Risk: LLM output is not demonstrably better than pasting into ChatGPT.** The UI constraints are a packaging improvement, not output quality proof. **Verify:** Run 10–15 real Hinge profiles through both HingeFix's prompts and a plain ChatGPT session; blind-compare with 5 target users before writing any front-end code. If users cannot reliably prefer the output, re-position the value as structured format + copy convenience, not AI quality.

- **Risk: HINGE_PROMPTS enum is out of date at launch.** Hinge updates prompts without notice. **Verify:** Open the live Hinge app and manually compare against the enum the week before launch. Schedule a 30-minute quarterly audit.

- **Risk: Reddit acquisition is not viable.** Subreddit mods may ban promotional posts; users may redirect to "just use ChatGPT." **Verify:** Before launch, post a genuine before/after rewrite as community content (no product link) in r/hingeapp. Measure organic upvotes and DMs. If reception is hostile or zero-traction, test a $50 Meta ad (Hinge + dating app interests targeting) against the landing page before investing in SEO.

- **Risk: Stripe webhook + LLM call chain introduces a race condition where the user lands on Screen 4 before results are ready.** **Verify:** Test the full payment → redirect → results flow end-to-end in Stripe test mode. Implement a KV polling fallback on the results page (poll every 2s, max 30s) if the record is not yet present when the page loads.

- **Risk: Before/after examples require sourcing real profiles with consent and anonymization.** This is a prerequisite for the landing page, not a footnote. **Verify:** Secure a minimum of 3 consented, anonymized real profiles before writing landing page copy. Count the sourcing labor explicitly — this is Days 1–2 of the build, not an afterthought.

---

## 12. Visual Reference

- **Live prototype (static mock — visual layout reference only):** https://jakebuild.github.io/idea-factory/35-dating-profile-scorer-and-roas/
- **Screen files on GitHub:** https://github.com/jakebuild/idea-factory/tree/gh-pages/35-dating-profile-scorer-and-roas
- **Source idea:** https://github.com/jakebuild/idea-factory/blob/main/35-dating-profile-scorer-and-roas/idea-v3.md
- **Source critique:** https://github.com/jakebuild/idea-factory/blob/main/35-dating-profile-scorer-and-roas/critique-v3.md

**Prototype-to-PRD screen number mapping:**
| Prototype # | Prototype Label | PRD # | PRD Label |
|---|---|---|---|
| *(absent)* | *(not in prototype)* | 1 | Landing Page |
| 1 | Profile Input | 2 | Profile Input |
| 2 | Checkout | 3 | Checkout |
| 3 | Results Audit | 4 | Results — Audit |
| 4 | Results Rewrites | 5 | Results — Rewrites |

**Prototype elements rejected (do not implement):**
- App title "The Curated Connection" in browser tab → use "HingeFix"
- Photo quality audit findings in Results Audit screen → photos are out of scope; audit covers bio + prompts only

**Prototype elements adopted:**
- Color system: primary `#b52330`, secondary `#6c45c0`, tertiary `#00a879`, background `#f9f9f9`, surface text `#1a1c1c`
- Typography: "Plus Jakarta Sans" headlines, "Manrope" body/labels
- Icons: Material Symbols Outlined
- Border radius tokens: 1rem default, 2rem large, 9999px pill
- 4-tab bottom nav bar (Profile / Checkout / Audit / Rewrites) — Optional polish; implement if time permits

---

## 13. Suggested Folder Structure

```
hingefix/
├── app/
│   ├── page.tsx                     # Screen 1: Landing Page
│   ├── profile/
│   │   └── page.tsx                 # Screen 2: Profile Input
│   ├── checkout/
│   │   └── page.tsx                 # Screen 3: Checkout
│   ├── results/
│   │   └── [token]/
│   │       ├── page.tsx             # Screen 4: Results — Audit (default tab)
│   │       └── rewrites/
│   │           └── page.tsx         # Screen 5: Results — Rewrites
│   └── api/
│       ├── checkout/
│       │   └── route.ts             # POST: create Stripe Checkout session
│       ├── webhook/
│       │   └── route.ts             # POST: Stripe webhook handler (generate + store results)
│       └── email-results/
│           └── route.ts             # POST: optional send-results email (Screen 5 polish)
├── components/
│   ├── ui/                          # Shared primitives (Button, Card, Badge, Input)
│   ├── profile-editor/
│   │   ├── BioField.tsx             # Bio textarea + counter + cliché chip
│   │   ├── PromptPair.tsx           # Dropdown + answer textarea + counter + cliché chip
│   │   └── ClicheChip.tsx           # Warning chip component
│   ├── checkout/
│   │   └── OrderSummary.tsx
│   ├── results/
│   │   ├── AuditCard.tsx            # Single audit dimension card
│   │   └── RewriteCard.tsx          # Bio or prompt rewrite card with copy button
│   └── BottomTabBar.tsx             # Optional polish: 4-tab nav
├── lib/
│   ├── cliches.ts                   # CLICHÉ_PHRASES constant
│   ├── hinge-prompts.ts             # HINGE_PROMPTS enum array
│   ├── llm.ts                       # OpenAI call + response validation/truncation
│   ├── kv.ts                        # Vercel KV read/write helpers
│   └── email.ts                     # Resend dispatch helpers
├── types/
│   └── index.ts                     # ProfileInput, ResultsRecord, AuditDimension, etc.
├── public/
│   └── before-after/                # Landing page example images or HTML cards
├── .env.local                       # OPENAI_API_KEY, STRIPE_*, UPSTASH_*, RESEND_API_KEY
├── next.config.ts
├── tailwind.config.ts               # Color tokens matching prototype design system
└── package.json
```
