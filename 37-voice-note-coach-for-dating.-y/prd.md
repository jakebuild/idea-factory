# PRD: Voice Note Polish — Pre-Send Panic Button for Dating

## 1. Overview

Voice Note Polish is a session-only mobile utility for people about to send a dating voice note they're not sure about. The user records their note in the app, receives three factual metrics (duration in seconds, filler word count with examples, hedge/apology phrase count with examples), reads two AI-generated rewrites (trimmed and front-loaded), optionally re-records using a rewrite as a guide, and sees the metric delta between takes. There is no account, no history, and no stored audio — the session is ephemeral by design. The payoff is the before/after delta: a concrete, specific proof that the second take was better.

---

## 2. Target User

Someone actively dating who sends voice notes on WhatsApp, iMessage, or a dating app, and sometimes cringes before hitting send — unsure whether they sounded too eager, rambling, or awkward. Existing solutions either don't exist (re-recording natively gives no feedback) or over-promise (AI "confidence coaches" with vague vibe scores). This user wants a factual, fast answer — not a judgment, not a score, not a lesson.

---

## 3. Tech Stack

**Platform:** Cross-platform mobile (iOS + Android)

**Language + Framework:** TypeScript + React Native with Expo (SDK 52+)

**Key libraries:**
- `expo-audio` — microphone access and audio recording; handles iOS/Android permission flows
- `expo-file-system` — read recorded audio files as blobs for Whisper upload; delete temp files on session clear
- React Navigation (Stack navigator, no tab bar) — matches the linear forward/back session flow; no persistent bottom nav
- Zustand — lightweight session-only state; no persistence layer needed since there is no history
- `openai` (official JS SDK) — Whisper transcription (`audio.transcriptions.create`) and GPT-4o analysis/rewrite (`chat.completions.create`)
- `expo-clipboard` — copy rewrite text to clipboard on Screen 5
- `expo-constants` — app version for Settings screen

**Hosting / Deployment:** EAS Build + EAS Submit to App Store and Play Store

**Why this stack:** Expo eliminates the native build friction that kills solo timelines. React Navigation's stack navigator maps directly to the app's linear session flow without a persistent tab bar. Zustand is zero-config for session state that never needs to persist across app launches. The OpenAI SDK covers both API calls (Whisper + GPT-4o) with one dependency.

---

## 4. V1 Scope

- **Required v1** — In-app audio recording with live elapsed timer
- **Required v1** — Microphone permission request with explanatory copy before first record attempt
- **Required v1** — Whisper API transcription of recorded audio
- **Required v1** — GPT-4o analysis returning exactly three signals: duration in seconds, filler word count + examples, hedge/apology phrase count + examples
- **Required v1** — 2–3 plain-language observations drawn from the transcript (no vibe labels, no scores)
- **Required v1** — Two rewrites per session: Trimmed (same voice, shorter) and Front-Loaded (point first)
- **Required v1** — Re-record flow with the chosen rewrite text visible above the record button
- **Required v1** — Metric delta display after re-recording (Original vs. New Take comparison table)
- **Required v1** — One-tap "Delete everything and go back" on the Comparison screen (deletes both transcripts, metrics, and temp audio files from device)
- **Required v1** — Whisper API disclosure on Screen 1: "Audio is sent to OpenAI for transcription and is not stored by this app." Displayed every session, not dismissible
- **Required v1** — Settings screen with expanded privacy explanation and one-tap "Clear all session data" button
- **Required v1** — Timer turns amber at 60 seconds during active recording (informational only)
- **Optional polish** — Copy rewrite text to clipboard on Screen 5
- **Optional polish** — Incrementally reveal transcript text during Processing screen as Whisper streams tokens (gives user something to read, builds trust)
- **Optional polish** — Haptic feedback on record start/stop

---

## 5. Out of Scope

- History screen or any cross-session persistence of transcripts, metrics, or recordings
- Trend charts, filler-per-minute tracking, or any metrics over time
- Point-burial detection as a named feature
- Pacing anomaly detection
- Waveform visualization during recording
- Confidence, neediness, attractiveness, or vibe scoring of any kind (including "Clarity Score" — explicitly rejected from the design mock)
- Any universal benchmark presented as a rule ("45 seconds is too long")
- Paste-a-transcript input mode
- Job interview, apology, or non-dating positioning
- User accounts or sign-in of any kind
- Audio file storage by the app (audio leaves the device once for Whisper; never stored server-side by this app)
- Share-out flow (sending the re-recorded audio directly from the app to a messaging app)
- Third "Refined Draft" rewrite tab (present in design mock — rejected; exactly two rewrites per session)
- "Clarity Score" percentage display on Comparison screen (present in design mock — rejected; explicitly out of scope per idea-v3)
- Bottom navigation tab bar (present in design mock — rejected; navigation is flow-based, not tab-based)
- Wellness/mindfulness language framing (present in design mock copy — rejected; app is a no-nonsense utility)

---

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Home / Record | Required v1 |
| 2 | Recording Active | Required v1 |
| 3 | Processing | Required v1 |
| 4 | Feedback Card | Required v1 |
| 5 | Rewrite View | Required v1 |
| 6 | Re-record | Required v1 |
| 7 | Comparison / Delta | Required v1 |
| 8 | Settings | Required v1 |

---

## 7. State Model

### Screen 1 — Home / Record

| State | Trigger | Shows | Actions available |
|---|---|---|---|
| `idle` | App launch or session cleared | Record button, privacy disclosure, gear icon | Tap Record → Screen 2; Tap gear → Screen 8 |
| `mic_permission_denied` | User denied microphone permission | Alert overlay explaining why mic access is required; "Open Settings" deep-link button | Open device settings |
| `session_in_progress` | Returning to home mid-session | Same as idle; session state retained in memory | Tap Record again → starts new session (clears previous) |

### Screen 2 — Recording Active

| State | Trigger | Shows | Actions available |
|---|---|---|---|
| `recording` | Tap Record on Screen 1 or Screen 6 | Pulsing record indicator, live elapsed timer, Stop button | Tap Stop |
| `timer_amber` | Elapsed time ≥ 60 seconds | Timer text color changes from default to amber | Tap Stop |

### Screen 3 — Processing

| State | Trigger | Shows | Actions available |
|---|---|---|---|
| `transcribing` | Stop tapped on Screen 2 | "Transcribing your note…" status line + animated indicator | None (no cancel) |
| `analyzing` | Whisper response received | "Analyzing…" status line; transcript text visible (incrementally or complete) | None |
| `transcription_error` | Whisper API call fails | Error message: "Transcription failed. Check your connection and try again." + "Go back" button | Tap Go back → Screen 1, session cleared |
| `analysis_error` | GPT-4o call fails after successful transcription | Error message: "Analysis failed. Check your connection and try again." + "Go back" button | Tap Go back → Screen 1, session cleared |

### Screen 4 — Feedback Card

| State | Trigger | Shows | Actions available |
|---|---|---|---|
| `feedback_ready` | Analysis complete | Three metric rows, 2–3 observations, two rewrite buttons | Tap "Trimmed Version" or "Front-Loaded Version" → Screen 5 |

### Screen 5 — Rewrite View

| State | Trigger | Shows | Actions available |
|---|---|---|---|
| `trimmed_active` | Arrived from Screen 4 or swipe/tap to Trimmed tab | Trimmed rewrite text, "Re-record using this" CTA, "Copy" secondary | Tap Re-record → Screen 6 (selectedRewrite = 'trimmed'); Tap Copy → clipboard |
| `front_loaded_active` | Tap Front-Loaded tab | Front-Loaded rewrite text, "Re-record using this" CTA, "Copy" secondary | Tap Re-record → Screen 6 (selectedRewrite = 'frontLoaded'); Tap Copy → clipboard |

### Screen 6 — Re-record

| State | Trigger | Shows | Actions available |
|---|---|---|---|
| `rerecording` | Tap "Re-record using this" on Screen 5 | Rewrite text in scrollable card above record button, "Take 2" label, pulsing indicator, live timer, Stop button | Tap Stop → Screen 3 (re-processing flow) → Screen 7 |
| `timer_amber` | Elapsed time ≥ 60 seconds | Timer text turns amber | Tap Stop |

> Note: Re-record routes through Screen 3 (Processing) before reaching Screen 7, because the new take must also be transcribed and analyzed to produce comparable metrics.

### Screen 7 — Comparison / Delta

| State | Trigger | Shows | Actions available |
|---|---|---|---|
| `comparison_ready` | Re-record analysis complete | Three-column comparison table (Metric / Original / New Take / Delta), delta values | Tap "Delete everything and go back" or "Done" |
| `delete_confirmed` | Tap "Delete everything" | Confirmation alert: "This will clear both recordings and transcripts." with Confirm/Cancel | Confirm → session cleared → Screen 1 |
| `done` | Tap "Done" | — | Navigate to Screen 1; session state kept in memory until next app launch or new session start |

### Screen 8 — Settings

| State | Trigger | Shows | Actions available |
|---|---|---|---|
| `settings_default` | Tap gear on Screen 1 | Expanded Whisper disclosure, "Clear all session data" button, app version | Tap Clear → confirmation alert → session cleared; Back navigation |
| `no_active_session` | No session in progress | "Clear all session data" button is disabled (grayed out) | Back navigation only |

---

## 8. Data Model

All state is in-memory only (Zustand store). Nothing is persisted to device storage or any backend except the temporary audio file written by `expo-audio` (deleted on session clear or app close). There is no database.

### Entity: `Session` (Zustand store, ephemeral)

| Field | Type | Description |
|---|---|---|
| `status` | `'idle' \| 'recording' \| 'processing' \| 'feedback' \| 'rewrite' \| 'rerecording' \| 'reprocessing' \| 'comparison'` | Current phase of the session; drives screen navigation |
| `originalAudioUri` | `string \| null` | Temporary file path of the original recording written by expo-audio; null when cleared |
| `originalDurationSeconds` | `number \| null` | Measured recording duration in whole seconds |
| `originalTranscript` | `string \| null` | Raw transcript text returned by Whisper |
| `originalMetrics` | `Metrics \| null` | Parsed analysis for the original take |
| `observations` | `string[]` | 2–3 plain-language observation sentences from GPT-4o; empty array until analysis complete |
| `rewrites` | `{ trimmed: string; frontLoaded: string } \| null` | Two rewrite strings from GPT-4o; null until analysis complete |
| `selectedRewrite` | `'trimmed' \| 'frontLoaded' \| null` | Which rewrite the user chose before re-recording; null if no rewrite selected yet |
| `retakeAudioUri` | `string \| null` | Temporary file path of the re-recorded take; null if no retake |
| `retakeDurationSeconds` | `number \| null` | Measured re-record duration in whole seconds |
| `retakeTranscript` | `string \| null` | Whisper transcript of re-recorded take |
| `retakeMetrics` | `Metrics \| null` | Parsed analysis for the re-recorded take |
| `activeRecordingStartTime` | `number \| null` | `Date.now()` when recording started; used to compute live elapsed timer |
| `processingError` | `'transcription' \| 'analysis' \| null` | Which step failed, to show the correct error state on Screen 3 |
| `micPermissionGranted` | `boolean \| null` | null = not yet requested; false = denied; true = granted |

### Entity: `Metrics` (inline type, not persisted)

| Field | Type | Description |
|---|---|---|
| `durationSeconds` | `number` | Recording length in whole seconds |
| `fillerCount` | `number` | Total filler word/phrase occurrences |
| `fillerExamples` | `{ word: string; count: number }[]` | e.g. `[{ word: "um", count: 4 }, { word: "like", count: 3 }]` |
| `hedgeCount` | `number` | Total hedge/apology phrase occurrences |
| `hedgeExamples` | `string[]` | e.g. `["sorry if this is weird", "I don't know if you'd be into this"]` |

### Delta (computed, not stored)

The metric delta displayed on Screen 7 is computed at render time from `originalMetrics` and `retakeMetrics`:
- `deltaDurationSeconds = retakeMetrics.durationSeconds - originalMetrics.durationSeconds`
- `deltaFillerCount = retakeMetrics.fillerCount - originalMetrics.fillerCount`
- `deltaHedgeCount = retakeMetrics.hedgeCount - originalMetrics.hedgeCount`

Negative deltas render in green with a "−" prefix. Positive deltas render in a neutral color with a "+" prefix.

---

## 9. Screens & Acceptance Criteria

---

### Screen 1: Home / Record [Required v1]

**Purpose:** Single entry point to the app; initiates recording and surfaces the privacy disclosure.

**Visual elements:**
- Status bar (system)
- App name "Voice Note Polish" in top center (Plus Jakarta Sans, 700)
- Gear icon (settings) in top-right corner
- Large centered mic icon / record button — primary CTA
- Below the button: "Ready to record" label in secondary text
- Privacy disclosure in small gray text below: "Audio is sent to OpenAI for transcription and is not stored by this app."
- No bottom navigation bar
- Background: cream (#fffbff); Primary accent: terracotta (#90544c)

**Design note — copy rejected from prototype:** The design mock uses "Share your story" and "Take a breath. Record your voice note for a mindful connection." This wellness/mindfulness framing is explicitly rejected. Use direct, utility-first copy: "Ready to record" / "Voice Note Polish."

**Design note — bottom nav rejected:** The prototype shows a persistent bottom navigation bar (Record / Review / Settings). This is rejected. Navigation is flow-based. The gear icon is the only navigation chrome on this screen.

**User actions:**
- Tap record button → requests mic permission if not granted → Screen 2
- Tap gear icon → Screen 8

**State changes:**
- `session.status` → `'recording'` on tap (after permission granted)
- `session.micPermissionGranted` updated on permission response

**Acceptance criteria:**
- [ ] Screen 1 is the only entry point to the app; there is no other home or list screen
- [ ] The privacy disclosure text "Audio is sent to OpenAI for transcription and is not stored by this app." is visible on every visit to Screen 1 without any user action to reveal it
- [ ] If `session.micPermissionGranted` is `null`, tapping the record button triggers the native mic permission dialog before proceeding
- [ ] If `session.micPermissionGranted` is `false`, tapping the record button shows the `mic_permission_denied` state with an "Open Settings" button that deep-links to device settings
- [ ] If `session.micPermissionGranted` is `true`, tapping the record button navigates to Screen 2 immediately
- [ ] The gear icon navigates to Screen 8 from any session state
- [ ] Returning to Screen 1 after a completed or cleared session shows the `idle` state (no residual session UI)

**Conditional states:**
- `mic_permission_denied`: shown instead of navigating to Screen 2; displays explanation and "Open Settings" link

---

### Screen 2: Recording Active [Required v1]

**Purpose:** Full-screen recording UI; the user records their voice note.

**Visual elements:**
- Full-screen layout, no header chrome
- Large centered pulsing record indicator (animated ring or dot — no waveform)
- Live elapsed timer in large text, format `M:SS` (e.g., "0:34") — timer color is default until 60 seconds
- At 60+ seconds: timer text turns amber; no other change, no alert, no rule stated
- Stop button below the timer (pill-shaped, terracotta)
- No other controls

**User actions:**
- Tap Stop → ends recording → navigate to Screen 3

**State changes:**
- `session.activeRecordingStartTime` set on screen entry
- `session.originalAudioUri` set to temp file path when recording ends
- `session.originalDurationSeconds` computed from recording duration
- `session.status` → `'processing'`

**Acceptance criteria:**
- [ ] Elapsed timer starts at 0:00 and increments each second while recording
- [ ] Timer text color is amber when `elapsedSeconds >= 60`; default color when `elapsedSeconds < 60`
- [ ] No benchmark text or rule is shown alongside the amber timer
- [ ] Tapping Stop ends the recording, sets `session.originalDurationSeconds` to the elapsed whole-second count, and navigates to Screen 3
- [ ] The screen shows no back button or navigation chrome other than Stop
- [ ] If the app is backgrounded during recording and returned to, recording continues (or resumes from last state — expo-audio behavior; acceptable to show error state if interrupted)

**Conditional states:**
- `timer_amber`: timer text color is amber; no modal, no pause

---

### Screen 3: Processing [Required v1]

**Purpose:** Shows transcription and analysis progress while the user waits.

**Visual elements:**
- Minimal centered layout
- Status line 1: "Transcribing your note…" with animated spinner
- Status line 2: "Analyzing…" (appears after Whisper completes, replaces status line 1)
- Transcript text area below: shows transcript text when Whisper response arrives (optional polish: incremental token streaming)
- No fake progress bar; no percentage

**User actions:**
- None during processing
- If error: "Try again" or "Go back" button

**State changes:**
- On Whisper success: `session.originalTranscript` set; status line advances to "Analyzing…"
- On GPT-4o success: `session.originalMetrics`, `session.observations`, `session.rewrites` set; `session.status` → `'feedback'`; navigate to Screen 4
- On Whisper failure: `session.processingError` = `'transcription'`; show error state
- On GPT-4o failure: `session.processingError` = `'analysis'`; show error state
- When reached from Screen 6 (re-record): same flow but populates `retakeTranscript` and `retakeMetrics`; navigates to Screen 7 on success

**Acceptance criteria:**
- [ ] "Transcribing your note…" is shown immediately on screen entry
- [ ] "Analyzing…" replaces or follows "Transcribing…" only after Whisper API returns successfully
- [ ] If Whisper fails, the error state shows "Transcription failed. Check your connection and try again." with a "Go back" button; tapping "Go back" returns to Screen 1 and clears the session
- [ ] If GPT-4o fails after Whisper succeeds, the error state shows "Analysis failed. Check your connection and try again." with a "Go back" button; tapping "Go back" returns to Screen 1 and clears the session
- [ ] No score, percentage, or vibe label appears on this screen
- [ ] When reached from Screen 6, successful completion navigates to Screen 7, not Screen 4

**Conditional states:**
- `transcription_error`: error message + Go back button; `session.processingError = 'transcription'`
- `analysis_error`: error message + Go back button; `session.processingError = 'analysis'`

---

### Screen 4: Feedback Card [Required v1]

**Purpose:** Presents the three factual metrics and plain-language observations from the original recording; entry point to rewrites.

**Visual elements:**
- Scrollable screen
- Three metric rows, each with an icon, label, and value:
  1. **Length** — `session.originalDurationSeconds` formatted as "X seconds" or "X minutes Y seconds" — no rule or judgment alongside it
  2. **Fillers** — "N filler words — 'um' (×4), 'like' (×3), 'you know' (×2)" using `session.originalMetrics.fillerExamples`
  3. **Hedges** — "N apology phrases — 'sorry if this is weird', 'I don't know if you'd be into this'" using `session.originalMetrics.hedgeExamples`
- Below metrics: 2–3 plain-language observation sentences from `session.observations` (no labels like "needy," "confident," or "attractive")
- Two equal-weight CTA buttons at bottom: **"Trimmed Version"** and **"Front-Loaded Version"**

**Design note — "Clarity Score" rejected:** The design mock shows "Clarity Score: 92%." This is explicitly out of scope. No score of any kind appears on this screen.

**Design note — duration format:** The prototype displays "3:45" (MM:SS). The PRD adopts the idea-v3 format: duration expressed in seconds or minutes + seconds (e.g., "1 minute 27 seconds" or "87 seconds") on the feedback card. The MM:SS format is only used for the live recording timer on Screens 2 and 6.

**User actions:**
- Tap "Trimmed Version" → set `session.selectedRewrite = 'trimmed'` → Screen 5 (Trimmed tab active)
- Tap "Front-Loaded Version" → set `session.selectedRewrite = 'frontLoaded'` → Screen 5 (Front-Loaded tab active)
- Back navigation → Screen 3 result is already computed; back returns to Screen 1 (session still in memory)

**State changes:**
- `session.selectedRewrite` set on button tap

**Acceptance criteria:**
- [ ] All three metric rows are populated from `session.originalMetrics` before Screen 4 is shown
- [ ] Filler examples display each unique filler word and its count, drawn from `session.originalMetrics.fillerExamples`
- [ ] Hedge examples show up to 3 example phrases from `session.originalMetrics.hedgeExamples`
- [ ] No score, percentage, vibe label ("needy," "confident"), or benchmark ("45 seconds is too long") appears on this screen
- [ ] Observations section shows between 2 and 3 sentences; each sentence is drawn from `session.observations`
- [ ] Tapping "Trimmed Version" sets `session.selectedRewrite = 'trimmed'` and navigates to Screen 5 with Trimmed tab active
- [ ] Tapping "Front-Loaded Version" sets `session.selectedRewrite = 'frontLoaded'` and navigates to Screen 5 with Front-Loaded tab active

---

### Screen 5: Rewrite View [Required v1]

**Purpose:** Shows the two AI-generated rewrites; entry point to the re-record flow.

**Visual elements:**
- Header with back arrow and screen title "Rewrite View"
- Two tabs or a swipeable panel: **"Trimmed"** and **"Front-Loaded"** — exactly two, no third tab
- Active tab shows the full rewrite text in a scrollable card (sourced from `session.rewrites.trimmed` or `session.rewrites.frontLoaded`)
- Primary CTA button: **"Re-record using this"**
- Secondary action: **"Copy"** (copies active rewrite text to clipboard) — Optional polish

**Design note — third tab rejected:** The design mock shows "Trimmed Front-Loaded," "Refined Draft," and a copy icon as three tabs. Exactly two rewrites exist per session per idea-v3. The "Refined Draft" third tab is rejected.

**User actions:**
- Switch tabs → changes displayed rewrite text; does not change `session.selectedRewrite` until "Re-record using this" is tapped
- Tap "Re-record using this" → set `session.selectedRewrite` to active tab → Screen 6
- Tap "Copy" → clipboard write → optional toast confirmation
- Back → Screen 4

**State changes:**
- `session.selectedRewrite` set on "Re-record using this" tap

**Acceptance criteria:**
- [ ] Exactly two tabs are shown: "Trimmed" and "Front-Loaded"
- [ ] Active tab on arrival matches `session.selectedRewrite` set on Screen 4
- [ ] Switching tabs shows the correct rewrite text from `session.rewrites`
- [ ] Tapping "Re-record using this" sets `session.selectedRewrite` to the active tab value and navigates to Screen 6
- [ ] The rewrite text on each tab is the full string from `session.rewrites.trimmed` or `session.rewrites.frontLoaded`
- [ ] No score, percentage, or label accompanies the rewrite text

---

### Screen 6: Re-record [Required v1]

**Purpose:** Second recording take, with the chosen rewrite text visible as a guide.

**Visual elements:**
- Label at top: "Take 2 — record a new version"
- Scrollable card showing the chosen rewrite text (from `session.rewrites[session.selectedRewrite]`) above the recording controls
- Same recording UI as Screen 2: pulsing indicator, live elapsed timer (M:SS), amber at 60+ seconds
- Stop button (pill-shaped, terracotta)
- No other controls

**User actions:**
- Tap Stop → ends re-recording → navigate to Screen 3 (re-processing)

**State changes:**
- `session.retakeAudioUri` set to temp file path
- `session.retakeDurationSeconds` set
- `session.status` → `'reprocessing'`

**Acceptance criteria:**
- [ ] The rewrite text shown is `session.rewrites[session.selectedRewrite]` — the exact string from the Rewrite View, not a truncated version
- [ ] The rewrite card scrolls independently of the recording controls (record indicator and Stop button remain fixed at bottom)
- [ ] Timer behavior matches Screen 2: starts at 0:00, increments per second, turns amber at 60+ seconds
- [ ] Tapping Stop sets `session.retakeDurationSeconds` and navigates to Screen 3
- [ ] Screen 3 reached from Screen 6 populates retake fields (`retakeTranscript`, `retakeMetrics`) and navigates to Screen 7 on success

---

### Screen 7: Comparison / Delta [Required v1]

**Purpose:** Shows the side-by-side metric delta between the original and re-recorded take; session endpoint.

**Visual elements:**
- Header: "Your improvement" (or "Take 2 results")
- Three-column comparison table:

  | Metric | Original | New Take | Delta |
  |--------|----------|----------|-------|
  | Duration | [originalDurationSeconds formatted] | [retakeDurationSeconds formatted] | [delta, green if negative] |
  | Fillers | [fillerCount] | [retakeFillerCount] | [delta, green if negative] |
  | Hedges | [hedgeCount] | [retakeHedgeCount] | [delta, green if negative] |

- Delta values prefixed with "−" (green) if improvement, "+" (neutral) if worse or unchanged
- Two CTAs at bottom:
  - **"Delete everything and go back"** — destructive; triggers confirmation alert
  - **"Done"** — non-destructive; returns to Screen 1, session stays in memory until next launch

**Design note — "Clarity Score" rejected:** The design mock shows "Clarity Score increased by 22%." No computed score or percentage of any kind appears on this screen. The comparison table is the entire payoff.

**User actions:**
- Tap "Delete everything and go back" → confirmation alert → on confirm: delete `session.originalAudioUri` and `session.retakeAudioUri` temp files, clear all session fields → Screen 1
- Tap "Done" → Screen 1 (session data stays in memory; not persisted; cleared on next session start)

**State changes:**
- On "Delete everything" confirm: all `session.*` fields reset to null/`'idle'`; temp audio files deleted via `expo-file-system`

**Acceptance criteria:**
- [ ] All three delta rows are populated before Screen 7 is shown (no placeholder values)
- [ ] Negative delta values (improvement) render in green with a "−" prefix
- [ ] Positive delta values (regression) render in a neutral color with a "+" prefix
- [ ] Tapping "Delete everything and go back" shows a confirmation alert with at minimum "Confirm" and "Cancel" options before any deletion occurs
- [ ] On confirmation: `session.originalAudioUri` and `session.retakeAudioUri` temp files are deleted from device storage; all session fields reset; navigates to Screen 1
- [ ] Tapping "Done" navigates to Screen 1 without deleting anything
- [ ] No "Clarity Score" or any computed percentage appears on this screen
- [ ] Navigating back from Screen 7 to Screen 4 is blocked (back navigation from Screen 7 goes to Screen 1, not to feedback)

---

### Screen 8: Settings [Required v1]

**Purpose:** Expanded privacy disclosure and emergency session clear; accessible only from Screen 1 gear icon.

**Visual elements:**
- Header with back arrow and "Settings" title
- **Privacy section:** Heading "About your audio" + expanded explanation: "When you tap Record, your audio is uploaded to OpenAI's Whisper API for transcription. The audio file is not stored by this app or OpenAI beyond the transcription request. Only the text transcript and metrics are kept in memory for your current session."
- **"Clear all session data"** button — disabled (grayed) if `session.status === 'idle'` and all session fields are null
- **App version** in small text at bottom (from `expo-constants`)

**User actions:**
- Tap "Clear all session data" → confirmation alert → on confirm: all session fields reset, temp audio files deleted → remain on Screen 8
- Back → Screen 1

**State changes:**
- On confirm clear: same reset as the "Delete everything" path on Screen 7

**Acceptance criteria:**
- [ ] Screen 8 is accessible only from the gear icon on Screen 1
- [ ] The full privacy explanation text is visible without any expand/collapse interaction
- [ ] "Clear all session data" is visually disabled when `session.status === 'idle'` and `session.originalTranscript === null`
- [ ] Tapping "Clear all session data" shows a confirmation alert before any data is cleared
- [ ] On confirmation, all `session.*` fields are reset and temp audio files are deleted
- [ ] App version string is visible at the bottom of the screen

---

## 10. External Integrations

### Whisper API

- **Service:** OpenAI Whisper (`whisper-1` model)
- **Purpose:** Convert the recorded audio file to a text transcript
- **Auth:** OpenAI API key — stored as an environment variable, never bundled in client JS; proxy through a minimal server function (e.g., Expo API route or a single Vercel function) to avoid key exposure in the mobile bundle
- **Key call:** `POST /v1/audio/transcriptions` with the audio file as `multipart/form-data`, `model: "whisper-1"`, `response_format: "text"`
- **Gotchas:**
  - Audio file must be sent as a binary blob, not a base64 string; use `expo-file-system` to read the file as a blob
  - Whisper has a 25 MB file size limit; a 90-second M4A recording is well within this
  - Accuracy on slang, noisy environments, and non-native accents is unverified — see Section 11

### GPT-4o (Analysis + Rewrites)

- **Service:** OpenAI Chat Completions (`gpt-4o` or `gpt-4o-mini` for cost)
- **Purpose:** Two calls per session — (1) analysis: extract three signals + 2–3 observations; (2) rewrites: generate trimmed and front-loaded versions
- **Auth:** Same API key / proxy as Whisper
- **Key call:** `POST /v1/chat/completions` with a structured system prompt; use `response_format: { type: "json_object" }` to enforce parseable output
- **Analysis prompt contract:** Must return JSON with fields: `durationSeconds` (integer), `fillerCount` (integer), `fillerExamples` (array of `{ word, count }`), `hedgeCount` (integer), `hedgeExamples` (array of strings), `observations` (array of 2–3 strings)
- **Rewrite prompt contract:** Must return JSON with fields: `trimmed` (string), `frontLoaded` (string)
- **Gotchas:**
  - `durationSeconds` should be sourced from the actual recording duration, not inferred from the transcript (Whisper does not reliably preserve timing). Pass the measured `originalDurationSeconds` to the analysis prompt as context.
  - GPT-4o may hallucinate filler examples not present in the transcript; the prompt must instruct the model to quote only from the provided transcript text
  - `gpt-4o-mini` is cheaper and fast enough for this use case; test quality before committing to full `gpt-4o`

---

## 11. Open Risks & Assumptions to Verify Before Build

- **Risk — Whisper accuracy on real dating audio:** Transcription accuracy on short (30–90s), conversational, emotionally-inflected audio with slang, background noise, and non-native accents is unverified. **What to verify:** Run 20 real-world sample recordings (varied accents, noisy environments, mumbled speech) through Whisper before writing any app code. If filler counts are frequently wrong by 30%+, constrain v1 to duration-only analysis and drop filler/hedge detection.

- **Risk — User acquisition via separate-app friction:** The core assumption is that users will leave WhatsApp/iMessage, open this app, re-record, read feedback, re-record again, then go back and send manually. This workflow has at least 5 more steps than native re-recording. **What to verify:** Run a manual concierge test with 5–10 real users before building: WhatsApp DM them the transcript + two rewrites manually; ask if they re-recorded. If they didn't re-record, the whole flow is broken.

- **Risk — Privacy trust as a conversion blocker:** Intimate dating audio going to OpenAI may cause users to bounce before first use, regardless of disclosure copy. **What to verify:** A/B test the disclosure copy on a landing page (or fake app store preview) before launch. Measure drop-off at the disclosure. If >40% bounce, consider a different privacy architecture (on-device Whisper via whisper.cpp).

- **Risk — API key exposure in mobile bundle:** Storing the OpenAI API key in the app binary is a security vulnerability that allows key theft and unauthorized spend. **What to verify:** Confirm the proxy/server-function approach before first commit; never ship with a hardcoded key in the app bundle.

- **Risk — "Front-Loaded" rewrite introduces subjective judgment:** The critique flags that showing observations like "you spend the first 40 seconds on context before mentioning the invite" is point-burial detection by another name. Blindly minimizing hedges ("maybe," "sorry") may also make some users sound robotic in a dating context. **What to verify:** In the GPT-4o prompt, instruct the model to identify structurally where the main point appears (factual, not labeled as "bad"), and to note that not all hedges are weakness — preserve warmth in the front-loaded rewrite. Review 10 real outputs before launch.

- **Risk — Expo audio recording reliability on real devices:** expo-audio behavior under interruption (phone call, backgrounding, OS memory pressure) varies across iOS and Android. **What to verify:** Test recording interruption scenarios on at minimum one physical iOS device and one Android device before ship. Define the graceful error state for interrupted recordings.

---

## 12. Visual Reference

- **Live prototype (static mock — visual layout reference only):** https://jakebuild.github.io/idea-factory/37-voice-note-coach-for-dating.-y/
- **Screen files on GitHub:** https://github.com/jakebuild/idea-factory/tree/gh-pages/37-voice-note-coach-for-dating.-y
- **Source idea:** https://github.com/jakebuild/idea-factory/blob/main/37-voice-note-coach-for-dating.-y/idea-v3.md
- **Source critique:** https://github.com/jakebuild/idea-factory/blob/main/37-voice-note-coach-for-dating.-y/critique-v3.md

**Color tokens (adopt from prototype):**
- `--color-primary`: #90544c (terracotta)
- `--color-surface`: #fffbff (cream)
- `--color-surface-secondary`: #f7f3ef (light neutral)
- `--color-text-primary`: #393835 (dark brown)
- `--color-text-secondary`: #72605e (muted taupe)
- `--color-delta-positive`: #16a34a (green — negative delta)

**Typography (adopt from prototype):**
- Headlines: Plus Jakarta Sans, 700
- Body / labels: Manrope

**Explicitly rejected from prototype:**
- Wellness/mindfulness copy ("mindful connection," "sacred commitment," "growth measured") — rejected; use direct utility copy
- Persistent bottom navigation tab bar (Record / Review / Settings) — rejected; stack navigator only
- "Clarity Score" percentage (Comparison screen) — rejected; no scores
- Third "Refined Draft" rewrite tab (Rewrite View screen) — rejected; exactly two rewrites
- Duration format "3:45" on Feedback Card — rejected; use "X seconds" or "X minutes Y seconds" format

---

## 13. Suggested Folder Structure

```
voice-note-polish/
├── app/                          # Expo Router app directory
│   ├── _layout.tsx               # Root Stack navigator
│   ├── index.tsx                 # Screen 1: Home / Record
│   ├── recording.tsx             # Screen 2: Recording Active
│   ├── processing.tsx            # Screen 3: Processing
│   ├── feedback.tsx              # Screen 4: Feedback Card
│   ├── rewrite.tsx               # Screen 5: Rewrite View
│   ├── rerecord.tsx              # Screen 6: Re-record
│   ├── comparison.tsx            # Screen 7: Comparison / Delta
│   └── settings.tsx              # Screen 8: Settings
├── src/
│   ├── store/
│   │   └── session.ts            # Zustand store — Session entity and all actions
│   ├── api/
│   │   ├── whisper.ts            # Whisper upload function
│   │   └── analysis.ts           # GPT-4o analysis + rewrite calls
│   ├── components/
│   │   ├── MetricRow.tsx         # Reusable metric row (icon + label + value)
│   │   ├── RecordButton.tsx      # Pulsing record indicator + timer
│   │   ├── DeltaTable.tsx        # Comparison table on Screen 7
│   │   └── PrivacyDisclosure.tsx # Disclosure text component
│   ├── hooks/
│   │   └── useRecording.ts       # expo-audio recording logic + permission handling
│   └── utils/
│       ├── formatDuration.ts     # seconds → "X minutes Y seconds" or "X seconds"
│       └── parseAnalysis.ts      # JSON parse + validate GPT-4o response
├── server/
│   └── proxy.ts                  # Minimal API proxy (Vercel function or Expo API route)
│                                 # keeps OpenAI key off the client bundle
├── assets/
│   └── fonts/                    # Plus Jakarta Sans + Manrope font files
├── app.json
├── eas.json
└── package.json
```
