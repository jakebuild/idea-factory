# PRD: Chat Unsticker

## 1. Overview

Chat Unsticker is a single-task mobile web app for one specific moment: your dating conversation has gone quiet and you don't know what to say next. The user pastes the last 10–15 messages, reviews an auto-redacted version of the conversation (names and identifiers replaced with placeholders before anything leaves the device), taps Confirm, and receives a one-line evidence-only diagnosis plus three context-specific reply options in different tones. Each reply has a copy-to-clipboard button. The product wins by being faster and lower-friction than opening ChatGPT cold — if it isn't, it has no reason to exist.

---

## 2. Target User

Someone in their 20s–30s on a dating app whose conversation has stalled after a few exchanges. They want a reply idea right now, on their phone, without the overhead of crafting a ChatGPT prompt from scratch. Existing solutions (ChatGPT, Gemini) require context-setting and prompt work that adds friction at a high-emotion moment; generic pickup-artist scripts feel fake and impersonal.

---

## 3. Tech Stack

- **Platform:** Mobile-first Progressive Web App (PWA). Runs in browser on iOS and Android with no App Store approval cycle — critical for a 2–4 week solo ship.
- **Language + framework:** TypeScript + React (Vite). Fast dev cycle, strong ecosystem, AI tooling support.
- **Key libraries:**
  - `compromise` (client-side NLP for name/entity detection — runs fully in-browser, no server round-trip)
  - `react-hot-toast` or similar for copy-confirmation feedback
  - `idb` (IndexedDB wrapper) for local session log storage
  - `openai` SDK (or `@anthropic-ai/sdk`) for LLM API calls — pick one provider before build, document in env vars
- **Hosting / deployment:** Vercel (zero-config, free tier, instant deploys from `main`). No backend server required — LLM API calls go through a Vercel Edge Function to keep the API key off the client.
- **Why this stack:** PWA removes App Store friction entirely. Vite + React is the fastest path to a polished mobile UI. `compromise` is the only production-ready client-side NLP library that runs in-browser without a model download. Vercel Edge Functions handle the API proxy without spinning up a separate server.

---

## 4. V1 Scope

- **Required v1** — Landing screen with speed-focused headline and single CTA
- **Required v1** — Text paste input with message count guidance and character count
- **Required v1** — Privacy disclosure panel on input screen (technically accurate, not overblown)
- **Required v1** — Client-side redaction step: `compromise`-based name/entity detection, highlighted placeholder tags, per-placeholder toggle to accept or revert
- **Required v1** — Manual "add redaction" tool on redaction review screen
- **Required v1** — Confirm & Analyze sends only redacted text to LLM API via Edge Function
- **Required v1** — LLM-generated evidence-only one-line diagnosis (observable counts only, no psychology)
- **Required v1** — Three reply option cards: tone label, reply text, one-line usage note, copy-to-clipboard
- **Required v1** — "Analyze another conversation" CTA on results screen (resets flow)
- **Required v1** — Error screen with plain-language failure message and retry/back actions
- **Required v1** — Privacy / How It Works modal accessible from landing footer and input screen
- **Required v1** — Loading state with status line and cancel/back button
- **Optional polish** — "Save this session" button writing date + diagnosis snippet + copied tone to local IndexedDB log
- **Optional polish** — History Log screen showing saved sessions

---

## 5. Out of Scope

- Rizz Score or any numeric scoring (4-dimension framework)
- Screenshot / OCR input
- Outcome tracking (yes/no reply tracking, summary stats)
- "Personal track record" or personalization claims
- Account system, cloud sync, multi-device
- Monetization / paywall
- Native iOS or Android app (App Store distribution)
- Deep links to specific dating apps
- Platform-specific advice (Hinge vs. Tinder differences)

---

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Landing / Home | Required v1 |
| 2 | Input | Required v1 |
| 3 | Redaction Review | Required v1 |
| 4 | Loading / Analysis | Required v1 |
| 5 | Results | Required v1 |
| 6 | History Log | Optional polish |
| 7 | Privacy / How It Works Modal | Required v1 |
| 8 | Error State | Required v1 |

---

## 7. State Model

### Screen 1: Landing / Home
- **Default:** Headline, subhead, CTA button, footer privacy link. Single static state — no conditional variants.

### Screen 2: Input
- **Empty:** Textarea is blank; Submit button is disabled; character count shows `0`; message count guidance visible.
- **Below threshold:** User has typed/pasted content but not reached minimum threshold (50 characters or ~3 lines); Submit button remains disabled; guidance note visible.
- **Ready:** Content meets threshold; Submit button becomes active.
- **Submitting:** User taps Submit; app runs client-side redaction locally; transitions to Screen 3. No loading spinner required — redaction is synchronous and near-instant.

### Screen 3: Redaction Review
- **Default:** Processed conversation text shown with detected tokens highlighted as placeholder tags. Each tag has accept/revert toggle. "Add more redactions" tool available. Confirm & Analyze button active. Back button available.
- **No detections:** Summary line reads "We found 0 names to redact — review below." Confirm & Analyze still available; user can add manual redactions.
- **Manual selection active:** User has highlighted text to add a custom redaction; tag appears inline before confirmation.

### Screen 4: Loading / Analysis
- **Loading:** Animation + status line "Reading your conversation…". Cancel/back button available. API call in flight.
- **Timeout (>15s):** Transitions to Screen 8 (Error) with "This is taking longer than expected" message.

### Screen 5: Results
- **Default:** Diagnosis line at top. Three reply cards visible. Copy buttons per card. "Save this session" button (optional polish). "Analyze another conversation" CTA.
- **Copied:** After tapping copy on a reply card, that card's copy button shows a transient "Copied!" confirmation for 2 seconds, then reverts. If session save is enabled: `session.copiedTone` records which tone label was copied.
- **Session saved (optional polish):** After tapping "Save this session," button label changes to "Saved ✓" and does not reset. Field `session.savedAt` is written to IndexedDB.

### Screen 6: History Log (Optional polish)
- **Populated:** Chronological list of saved sessions. Each row: date, diagnosis snippet, tone label.
- **Empty:** No saved sessions exist; prompt reads "No sessions saved yet. Analyze a conversation to get started." CTA to Screen 2.

### Screen 7: Privacy Modal
- **Open:** Modal overlays current screen. Sections: what is sent, where it goes, what is stored, what the LLM provider does, what redaction does, contact info.
- **Closed:** Dismissed by tap outside or close button; returns to origin screen.

### Screen 8: Error
- **API failure:** Plain-language error message, retry button (re-fires API call with same redacted text), back-to-input button.
- **Bad input / empty response:** Separate message: "The AI couldn't generate replies for this conversation. Try pasting more messages." Back-to-input button.

---

## 8. Data Model

### Entity: `Session` (stored in IndexedDB, local only)
- `id`: string — UUID generated at save time
- `savedAt`: ISO 8601 string — timestamp when user tapped "Save this session"
- `diagnosisSnippet`: string — the one-line evidence-only diagnosis returned by LLM
- `copiedTone`: `"PLAYFUL" | "DIRECT" | "CURIOUS" | null` — tone label of the last reply the user copied; null if user saved without copying

### Entity: `AnalysisResult` (in-memory only, not persisted)
- `diagnosis`: string — evidence-only one-line diagnosis
- `replies`: `Reply[]` — always exactly 3 items

### Entity: `Reply` (in-memory only)
- `tone`: `"PLAYFUL" | "DIRECT" | "CURIOUS"`
- `text`: string — the actual suggested reply
- `usageNote`: string — one-line note on when to use it

### Entity: `RedactionToken` (in-memory only, ephemeral per session)
- `original`: string — the original detected token
- `placeholder`: string — e.g. `[Name]`, `[Her]`, `[Him]`, `[You]`
- `accepted`: boolean — whether user has accepted this replacement (defaults to `true`)

**Persisted UI state mapping:**
- Results screen "Saved ✓" button state → `Session.savedAt` (non-null = saved)
- Results screen copied tone → `Session.copiedTone`
- History log rows → all `Session` records in IndexedDB ordered by `savedAt` desc

---

## 9. Screens & Acceptance Criteria

### Screen 1: Landing / Home [Required v1]

**Purpose:** Convert visitor to first paste within seconds.

**Visual elements:**
- App name "Chat Unsticker" — large, top-center
- Headline: "Your chat went quiet. Get a reply idea in 30 seconds."
- Subhead: 1–2 lines explaining the flow (paste → redact → analyze → copy)
- Primary CTA button: "Analyze a conversation" — full-width, blue (`#3b82f6`)
- Footer: single-line privacy link ("How this works & privacy") that opens Screen 7 modal
- Optional static example teaser: a redacted conversation snippet → one result card (visual only, no interaction)
- Dark navy background (`#0a0f1a`), off-white text (`#f9fafb`)

**User actions:**
- Tap "Analyze a conversation" → navigates to Screen 2
- Tap privacy footer link → opens Screen 7 modal

**Acceptance criteria:**
- [ ] Tapping "Analyze a conversation" navigates to Screen 2 (Input)
- [ ] Tapping the footer privacy link opens the Screen 7 modal without leaving Screen 1
- [ ] Landing screen renders correctly on 390px wide viewport (iPhone 14 baseline)
- [ ] No score, no personalization claims, no "Rizz Score" language appears anywhere on this screen

---

### Screen 2: Input [Required v1]

**Purpose:** Collect the conversation text from the user.

**Visual elements:**
- Screen title: "Paste your conversation"
- Large textarea: labeled "Paste the last 10–15 messages (both sides)" — dark card surface (`#111827`), border `#1f2937`
- Character count indicator below textarea (e.g., "0 characters")
- Guidance note: "Include both your messages and theirs. More context = better replies."
- Privacy disclosure panel (not buried): "Your redacted conversation is sent to an AI API for processing. We do not log or store it on our end. The AI provider may process it transiently per their terms." Link to Screen 7 modal inline.
- Submit button: "Analyze →" — disabled state when below threshold, active (blue) when ready
- Back chevron to Screen 1

**User actions:**
- Paste or type text into textarea
- Tap "Analyze →" when active → triggers client-side redaction, transitions to Screen 3
- Tap privacy link → opens Screen 7 modal
- Tap back → returns to Screen 1

**State changes:**
- Character count updates on every keystroke
- Submit button transitions from disabled → active when content ≥ 50 characters

**Acceptance criteria:**
- [ ] Submit button is disabled when textarea contains fewer than 50 characters
- [ ] Submit button becomes active when textarea contains 50 or more characters
- [ ] Character count indicator updates in real time as user types or pastes
- [ ] Tapping "Analyze →" when active runs client-side redaction synchronously and transitions to Screen 3 without any server call at this step
- [ ] Tapping back from Screen 2 returns to Screen 1
- [ ] Privacy disclosure panel is visible without scrolling on a 390×844px viewport (not buried below the fold)
- [ ] Tapping the inline privacy link on this screen opens Screen 7 modal

**Conditional states:**
- *Empty:* Submit button disabled, character count shows `0`
- *Below threshold:* Submit disabled; no inline error shown until user taps the button (if tapped, show inline: "Paste at least a few messages to continue")
- *Ready:* Submit button active (blue)

---

### Screen 3: Redaction Review [Required v1]

**Purpose:** Let the user review and confirm what gets sent before the API call fires. This is the trust differentiator.

**Visual elements:**
- Screen title: "Review before sending"
- Summary line: "We found [N] possible names — review below" (where N = count of detected tokens)
- Conversation text rendered with detected tokens replaced by colored placeholder tags: `[Name]`, `[Her]`, `[Him]`, `[You]` — each tag is a tappable chip
- Each chip shows a toggle: accept replacement (tag remains) or revert (original text restored)
- "Add more redactions" tool: user can highlight any word/phrase to manually add a placeholder
- Confirm & Analyze button (blue, full-width): fires API call with accepted redactions applied
- Back button: returns to Screen 2 with original text preserved

**User actions:**
- Toggle each detected placeholder chip to accept or revert
- Highlight text to add a manual redaction
- Tap "Confirm & Analyze" → fires API call → transitions to Screen 4
- Tap back → returns to Screen 2 (textarea retains original pasted text)

**State changes:**
- Toggling a chip to "revert" restores original text inline; toggling to "accept" re-applies placeholder
- Manual highlight adds a new placeholder chip inline
- Tapping "Confirm & Analyze" triggers the API call with the current accepted state of all tokens applied to the text

**Acceptance criteria:**
- [ ] Every token detected by the client-side NLP step appears as a highlighted chip on this screen
- [ ] Toggling a chip to "revert" restores the original text in the preview immediately without re-running detection
- [ ] Toggling a chip back to "accept" re-applies the placeholder immediately
- [ ] The text sent to the API on "Confirm & Analyze" contains only placeholders for all accepted tokens — original values for reverted tokens
- [ ] Back button on this screen returns to Screen 2 and the textarea retains the text the user originally pasted
- [ ] "Confirm & Analyze" is only tappable after the redaction preview has fully rendered (prevents double-submit)
- [ ] When 0 tokens are detected, summary line reads "We found 0 names to redact — review below" and Confirm & Analyze is still available

**Conditional states:**
- *No detections:* Summary line shows 0 count; conversation text shown as-is with no chips; user may still add manual redactions before confirming
- *Manual selection active:* Selected text highlighted before user assigns placeholder label

---

### Screen 4: Loading / Analysis [Required v1]

**Purpose:** Manage user wait while the LLM API processes the redacted conversation.

**Visual elements:**
- Centered layout
- Brief animation (pulsing dots or spinner — match design reference)
- Status line: "Reading your conversation…"
- Estimated time note: "Usually under 5 seconds"
- Cancel / back button: returns to Screen 2 (cancels in-flight request)

**User actions:**
- Tap cancel → aborts API request, returns to Screen 2 with original text preserved

**State changes:**
- On successful API response → transitions to Screen 5
- On API error or timeout (>15s) → transitions to Screen 8

**Acceptance criteria:**
- [ ] Loading screen appears immediately after "Confirm & Analyze" is tapped on Screen 3
- [ ] Cancel button aborts the in-flight API request and returns to Screen 2 with the textarea text intact
- [ ] If API responds successfully in under 15 seconds, app transitions to Screen 5 without user action
- [ ] If API call fails or exceeds 15 seconds, app transitions to Screen 8

---

### Screen 5: Results [Required v1]

**Purpose:** Deliver the value — diagnosis and three ready-to-send reply options.

**Visual elements:**
- Evidence-only one-line diagnosis at top, in a distinct panel — e.g., "You've sent 3 messages in a row. They've asked 0 questions in the last 8 messages."
- Three reply cards, each in a distinct zone:
  - Tone label badge: `PLAYFUL` | `DIRECT` | `CURIOUS`
  - Reply text (the actual suggested message, specific to the conversation)
  - One-line usage note: e.g., "Use this to re-engage lightly without pressure"
  - Copy-to-clipboard button per card
- "Analyze another conversation" CTA (full-width, bottom) — resets to Screen 2
- **Optional polish:** "Save this session" button — writes `Session` record to IndexedDB

**User actions:**
- Tap copy on a reply card → copies reply text to clipboard; button shows "Copied!" for 2 seconds, then reverts
- Tap "Save this session" (optional) → writes session to IndexedDB; button label changes to "Saved ✓" permanently for this session
- Tap "Analyze another conversation" → resets app state and navigates to Screen 2

**State changes:**
- Copy button: transient "Copied!" state for 2 seconds per card, independent per card
- If "Save this session" tapped: `Session` record created with `savedAt` = now, `diagnosisSnippet` = diagnosis text, `copiedTone` = tone of last copied reply (or `null` if none copied yet). Button becomes "Saved ✓" and is non-interactive.

**Acceptance criteria:**
- [ ] Diagnosis text displayed contains only observable counts/patterns — no psychological interpretation or attraction claims
- [ ] Exactly three reply cards are displayed, each with a distinct tone label (`PLAYFUL`, `DIRECT`, `CURIOUS`)
- [ ] Each reply card has its own copy-to-clipboard button
- [ ] Tapping copy on any reply card copies that card's reply text to the system clipboard
- [ ] Copy button on a tapped card shows "Copied!" for exactly 2 seconds then reverts to copy icon
- [ ] Tapping "Analyze another conversation" clears in-memory `AnalysisResult` and navigates to Screen 2 with an empty textarea
- [ ] **Optional polish:** Tapping "Save this session" creates a `Session` record in IndexedDB with `savedAt`, `diagnosisSnippet`, and `copiedTone` fields populated correctly
- [ ] **Optional polish:** After "Save this session" is tapped, button label permanently changes to "Saved ✓" for the duration of this session and cannot be tapped again

---

### Screen 6: History Log [Optional polish]

**Purpose:** Lightweight local record of past saved sessions.

**Visual elements:**
- Screen title: "Past Sessions"
- Chronological list (newest first) of saved `Session` records
- Each row: formatted date, diagnosis snippet (truncated at ~80 chars), tone label badge of what was copied
- "Analyze a new conversation" CTA (top or bottom)
- No summary stats, no percentages, no "your best tone" claims

**User actions:**
- Tap "Analyze a new conversation" → navigates to Screen 2
- Scroll through session list

**Acceptance criteria:**
- [ ] Sessions appear in descending `savedAt` order (newest first)
- [ ] Each row displays `savedAt` formatted as human-readable date, `diagnosisSnippet` truncated to ~80 chars with ellipsis, and `copiedTone` as a badge (or "—" if null)
- [ ] No statistics, percentages, or aggregated claims appear anywhere on this screen
- [ ] Tapping "Analyze a new conversation" navigates to Screen 2

**Conditional states:**
- *Empty:* No `Session` records in IndexedDB. Shows: "No sessions saved yet. Analyze a conversation to get started." with CTA to Screen 2.

---

### Screen 7: Privacy / How It Works Modal [Required v1]

**Purpose:** Transparent disclosure for skeptical users; accessible from anywhere.

**Visual elements:**
- Modal overlay (dark scrim over current screen)
- Close button (X) top-right; tap outside also closes
- Sections:
  1. **What is sent:** "The redacted version of your conversation — original names and identifiers are replaced with placeholders before anything leaves your device."
  2. **Where it goes:** Named LLM provider (e.g., "OpenAI GPT-4o" or "Anthropic Claude") — must be the actual provider name, not a placeholder.
  3. **What we store:** "Nothing on our servers. If you choose to save a session, it is stored locally on your device only."
  4. **What the LLM provider does:** "Your conversation is processed transiently. [Provider] may process content for safety monitoring per their terms." Link to provider's privacy policy.
  5. **What redaction does:** "We detect likely first names and identifiers and replace them with placeholders. This reduces obvious identifying information but does not fully anonymize a conversation."
  6. **Contact:** Email or link for privacy concerns.

**User actions:**
- Tap X or tap outside modal → closes modal, returns to origin screen
- Tap LLM provider privacy link → opens provider's privacy policy in new tab

**Acceptance criteria:**
- [ ] Modal is accessible from Landing screen footer link (Screen 1) and from the inline privacy link on Screen 2
- [ ] Modal closes and returns focus to the originating screen without navigation side effects
- [ ] All six content sections are present and contain technically accurate copy (no "we never store anything" overclaims)
- [ ] The named LLM provider in section 2 matches the actual API being called — must be confirmed before launch
- [ ] The redaction limitation disclaimer in section 5 is present and does not overstate anonymization guarantees

---

### Screen 8: Error State [Required v1]

**Purpose:** Handle API failure or bad input without losing user's work.

**Visual elements:**
- Error icon or indicator
- Plain-language failure reason — two variants:
  - API/network failure: "Something went wrong — the AI couldn't process this. Try again or paste a shorter conversation."
  - Bad input / empty response: "The AI couldn't generate replies for this conversation. Try pasting more messages."
- Retry button (re-fires API call with the same redacted text, no re-entering Screen 3)
- "Edit conversation" button → returns to Screen 2 with original text preserved

**User actions:**
- Tap retry → re-fires API call → transitions to Screen 4, then Screen 5 or back to Screen 8
- Tap "Edit conversation" → returns to Screen 2 with original textarea text intact

**Acceptance criteria:**
- [ ] Error screen appears if the API call fails for any reason (network error, non-200 response, timeout)
- [ ] Retry re-fires the API call using the same redacted text without requiring the user to re-enter Screen 3
- [ ] "Edit conversation" returns to Screen 2 with textarea text intact (not cleared)
- [ ] No user data (pasted conversation, redacted text) is silently discarded when the error screen appears

---

## 10. External Integrations

### LLM API (OpenAI or Anthropic — choose one before build)
- **Purpose:** Generate evidence-only diagnosis + three context-specific reply options from the redacted conversation text
- **Auth:** API key stored as an environment variable in Vercel; never exposed to the client. All LLM calls go through a Vercel Edge Function (`/api/analyze`).
- **Key call:** Single `POST /api/analyze` from client → Edge Function calls LLM chat completions endpoint with a structured system prompt enforcing evidence-only diagnosis and three-tone reply format
- **Response format:** Edge Function parses and validates LLM JSON response into `{ diagnosis: string, replies: [{tone, text, usageNote}] }` before returning to client. If parsing fails → 500 → Screen 8.
- **Gotchas:**
  - System prompt must explicitly prohibit psychological interpretation ("you cannot assess attraction, emotions, or the other person's feelings")
  - Response must be validated server-side before returning — a hallucinated extra reply field or missing tone label must not reach the client
  - Prompt must be tested across at least 10 representative stalled-chat examples before ship
  - LLM provider's data processing and abuse monitoring policies must be read and accurately reflected in the Screen 7 privacy modal

### `compromise` (client-side NLP)
- **Purpose:** Detect likely first names and identifiers in the pasted conversation before the API call
- **Auth:** None — runs fully in-browser
- **Key call:** `nlp(text).people().out('array')` to extract candidate names; supplemented by simple regex for handles (`@word`) and common date/location patterns
- **Gotchas:**
  - Will miss nicknames, inside jokes, locations, schools, emojis with meaning — this must be reflected in the Screen 7 privacy modal ("reduces obvious identifiers, does not fully anonymize")
  - False positives (common words detected as names) should be surfaced as toggleable chips so users can revert them
  - Library bundle size must be checked — `compromise` is ~200KB gzipped; acceptable for PWA but measure initial load time on mobile

### IndexedDB (via `idb`)
- **Purpose:** Local-only storage for optional saved sessions (Screen 6)
- **Auth:** None — browser storage, device-local
- **Key calls:** `db.add('sessions', session)` on save; `db.getAll('sessions')` on Screen 6 load; no delete required in v1
- **Gotchas:** IndexedDB is unavailable in private/incognito mode in some browsers — handle gracefully (disable "Save this session" button with tooltip "Saving unavailable in private mode")

---

## 11. Open Risks & Assumptions to Verify Before Build

- **Risk: Speed benchmark may not be achievable.** The product's existence depends on beating a saved ChatGPT prompt from cold open to usable reply on a real phone, redaction included, in under 30 seconds. **What to verify:** Time the full flow (open PWA → paste → redact → confirm → wait → copy reply) on an actual mobile device in week 1. If it doesn't beat ChatGPT clearly, stop and reposition or kill.

- **Risk: Users may not trust the app enough to paste dating conversations even after seeing the redaction screen.** The entire privacy story may still be insufficient for the "intimate conversation" use case. **What to verify:** Show 5–10 target users the redaction review screen on mobile and ask if they'd paste a real stalled chat. If the majority say no, the product needs rethinking before build.

- **Risk: LLM output may be generic despite full conversation context.** Three replies "in different tones" is dangerously close to template sludge if prompt engineering is weak. **What to verify:** Test the structured prompt against 10+ diverse stalled-chat examples before starting UI build. If outputs feel generic in more than 3/10 cases, iterate on the prompt until resolved — this is the core value delivery.

- **Risk: `compromise` NLP will miss creative identifiers and produce false confidence.** Nicknames, locations, emojis, and inside references will not be caught. **What to verify:** Run `compromise` against 10 sample conversations and count misses vs. detections. Confirm the privacy modal copy accurately reflects actual detection capability before launch.

- **Risk: Redaction UX adds enough friction to break the "30 seconds" headline.** If users need to make 3+ manual corrections per conversation, the speed promise collapses. **What to verify:** Measure median time spent on Screen 3 across test users. If >15 seconds on average, simplify the UX (e.g., "accept all and confirm" as a single-tap path).

- **Assumption: LLM API provider's transient processing policy is acceptable and accurately describable in the privacy modal.** **What to verify:** Read the actual privacy terms for the chosen provider (OpenAI, Anthropic, or other) and confirm the exact language for Screen 7 before launch. Do not launch with placeholder copy.

---

## 12. Visual Reference

- **Live prototype (static mock — visual layout reference only):** https://jakebuild.github.io/idea-factory/46-rizz-score-is-a-dating-convers/
- **Screen files on GitHub:** https://github.com/jakebuild/idea-factory/tree/gh-pages/46-rizz-score-is-a-dating-convers
- **Source idea:** https://github.com/jakebuild/idea-factory/blob/main/46-rizz-score-is-a-dating-convers/idea-v3.md
- **Source critique:** https://github.com/jakebuild/idea-factory/blob/main/46-rizz-score-is-a-dating-convers/critique-v3.md

**Design system adopted from prototype:**
- Background: `#0a0f1a` (dark navy)
- Card surfaces: `#111827`
- Borders: `#1f2937`
- Primary accent: `#3b82f6` (blue)
- Text primary: `#f9fafb`
- Text secondary: `#6b7280`
- Typography: system fonts (SF Pro on iOS, Segoe UI on Windows); size hierarchy via weight and size
- Mobile viewport baseline: 390×844px

**Divergences from prototype accepted into PRD:** None — prototype matches source documents.
**Divergences rejected:** The prototype's two-column desktop layout (mobile frame + timeline sidebar) is a presentation device for the design review, not a production UI pattern. The production app is single-column mobile-first only.

---

## 13. Suggested Folder Structure

```
chat-unsticker/
├── public/
│   ├── manifest.json          # PWA manifest
│   └── favicon.ico
├── src/
│   ├── components/
│   │   ├── screens/
│   │   │   ├── LandingScreen.tsx
│   │   │   ├── InputScreen.tsx
│   │   │   ├── RedactionReviewScreen.tsx
│   │   │   ├── LoadingScreen.tsx
│   │   │   ├── ResultsScreen.tsx
│   │   │   ├── HistoryLogScreen.tsx  # optional polish
│   │   │   └── ErrorScreen.tsx
│   │   ├── PrivacyModal.tsx
│   │   ├── ReplyCard.tsx
│   │   └── RedactionChip.tsx
│   ├── lib/
│   │   ├── redaction.ts       # compromise NLP + regex detection logic
│   │   ├── db.ts              # idb IndexedDB helpers (Session CRUD)
│   │   └── types.ts           # AnalysisResult, Reply, RedactionToken, Session types
│   ├── App.tsx                # screen router (simple useState-based, no router library needed)
│   └── main.tsx
├── api/
│   └── analyze.ts             # Vercel Edge Function — LLM API proxy
├── .env.local                 # OPENAI_API_KEY or ANTHROPIC_API_KEY (never committed)
├── vite.config.ts
├── tsconfig.json
└── package.json
```
