# PRD: VoiceMirror

## 1. Overview
VoiceMirror is a web app for job seekers preparing for behavioral interviews who need tighter, more defensible STAR stories. Users record or type an answer to a curated behavioral question, receive a critique-first diagnosis of missing STAR parts, evidence gaps, and answer length, then turn that answer into a reusable Story Card for follow-up drills and retry comparison. In v1, the product is a local-first interview sprint tool, not a general career coach, not a habit app, and not a voice-cloning product.

## 2. Target User
The target user is an active job seeker with an interview coming soon who struggles to explain past work clearly under pressure. Existing solutions fail because generic AI chat tools produce broad advice, inconsistent structure checks, and disposable one-off answers that are hard to reuse across rounds. This user wants strict, repeatable drilling on their own stories without giving up control of what is true.

## 3. Tech Stack
- Platform: Web
- Language + framework: TypeScript + Next.js 15 (App Router) + React 19
- Key libraries: `openai` for transcription and analysis API calls, `dexie` for IndexedDB local persistence, `zustand` for app state, `react-hook-form` + `zod` for forms and validation, `tailwindcss` + `shadcn/ui` for UI primitives
- Hosting / deployment: Vercel
- Why this stack fits the solo dev + 2–4 week constraint: one codebase, fast deployment, native support for server actions and API routes, no separate backend required for v1, and IndexedDB keeps the local-first promise without building auth or sync

## 4. V1 Scope
- **Required v1** Behavioral interview drilling only
- **Required v1** 25 curated primary questions mapped to 8 story themes
- **Required v1** Sprint setup with interview date, target role, and current confidence level
- **Required v1** Record answer and type answer input modes
- **Required v1** Transcription for recorded answers
- **Required v1** Critique-first diagnosis with explainable checks only: STAR coverage, answer length, evidence gaps, weak action detail
- **Required v1** Auto-generated Story Card with user editing for situation, task, action, result, proof points, notes, and follow-up probes
- **Required v1** Follow-up drill flow tied to an existing Story Card
- **Required v1** Retry comparison for two takes on the same story
- **Required v1** Local-first session history and per-session deletion
- **Required v1** Privacy screen with delete-all local data control
- **Optional polish** Optional rewrite view that generates a tighter outline from the Story Card

## 5. Out of Scope
- Recruiter screen practice
- Salary negotiation practice
- General career conversations
- Voice cloning
- Personality, confidence, or executive-presence scoring
- User-generated question packs
- Community or marketplace features
- Mobile apps
- Account auth and cross-device sync
- Payments and subscriptions
- Full scripted rewrite generation
- Daily countdown plans

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Landing Page | Required v1 |
| 2 | Sprint Setup | Required v1 |
| 3 | Story Bank Dashboard | Required v1 |
| 4 | Question Drill | Required v1 |
| 5 | Processing | Required v1 |
| 6 | Answer Diagnosis | Required v1 |
| 7 | Story Card Editor | Required v1 |
| 8 | Optional Rewrite View | Optional polish |
| 9 | Follow-up Drill | Required v1 |
| 10 | Retry Comparison | Required v1 |
| 11 | Session History | Required v1 |
| 12 | Privacy & Data Controls | Required v1 |

## 7. State Model

### Global
- **State name:** Local-only visitor
  - Trigger: app opens in v1
  - Shows: full app with no sign-in and no account requirement
  - Actions: start sprint, continue to dashboard, view history, open privacy controls
- **State name:** Authenticated account state not available
  - Trigger: always in v1
  - Shows: no sign-in, no account menu, no cloud sync messaging
  - Actions: none
- **State name:** IndexedDB unavailable error
  - Trigger: browser blocks IndexedDB or storage quota fails on boot
  - Shows: blocking error with retry and explanation that v1 needs local storage
  - Actions: retry initialization, leave app
- **State name:** Microphone permission unknown
  - Trigger: before first recording attempt
  - Shows: record option enabled, permission prompt on use
  - Actions: start recording, switch to typed answer
- **State name:** Microphone permission denied
  - Trigger: browser denies microphone access
  - Shows: record controls disabled with explanation and typed-answer CTA
  - Actions: retry permission request, switch to typed answer

### Screen 1. Landing Page
- **State name:** First-time visitor
  - Trigger: no `UserProfile` exists
  - Shows: product explanation, critique-first workflow, privacy stance, start sprint CTA
  - Actions: go to Screen 2, go to Screen 12
- **State name:** Returning visitor
  - Trigger: `UserProfile` exists
  - Shows: same landing layout plus continue sprint CTA
  - Actions: go to Screen 3, go to Screen 11, go to Screen 12

### Screen 2. Sprint Setup
- **State name:** Empty form
  - Trigger: new user with no saved profile
  - Shows: role field, interview date field, confidence selector, v1 scope note
  - Actions: enter values, submit, cancel back to Screen 1
- **State name:** Prefilled form
  - Trigger: existing `UserProfile`
  - Shows: saved values loaded into form
  - Actions: edit values, save, cancel to Screen 3
- **State name:** Validation error
  - Trigger: submit with missing role, missing date, or invalid past date
  - Shows: inline field errors
  - Actions: correct fields, resubmit

### Screen 3. Story Bank Dashboard
- **State name:** Empty dashboard
  - Trigger: `PracticeSession` count is 0
  - Shows: no stories yet message, first recommended question card, start drill CTA
  - Actions: start Screen 4, open Screen 11
- **State name:** Populated dashboard
  - Trigger: at least one saved `StoryCard`
  - Shows: theme cards, missing proof summaries, next recommended drill, recent activity
  - Actions: start recommended drill, open Story Card on Screen 7, open Screen 11, open Screen 12
- **State name:** Content load error
  - Trigger: curated question set fails to load
  - Shows: blocking error banner and retry
  - Actions: retry loading questions

### Screen 4. Question Drill
- **State name:** Record answer mode
  - Trigger: user selects `Record answer`
  - Shows: prompt, optional context, timer, record button, stop button after recording starts, submit button after capture
  - Actions: start recording, stop recording, discard, submit
- **State name:** Type answer mode
  - Trigger: user selects `Type answer`
  - Shows: prompt, optional context, multiline text area, character count, submit button
  - Actions: type answer, clear, submit
- **State name:** Draft ready
  - Trigger: recorded audio captured or typed answer has content
  - Shows: review state with submit enabled
  - Actions: submit to Screen 5, edit input, cancel to Screen 3
- **State name:** Input error
  - Trigger: submit with empty answer or failed audio capture
  - Shows: inline error and disabled progress
  - Actions: fix input, retry

### Screen 5. Processing
- **State name:** Transcribing
  - Trigger: audio answer submitted
  - Shows: progress message for transcription, privacy note that raw audio is not retained after processing
  - Actions: none
- **State name:** Analyzing
  - Trigger: transcript available or typed answer submitted
  - Shows: progress message for diagnosis and Story Card generation
  - Actions: none
- **State name:** Processing error
  - Trigger: transcription or analysis API failure
  - Shows: failure message with retry and return-to-edit actions
  - Actions: retry processing, return to Screen 4

### Screen 6. Answer Diagnosis
- **State name:** Diagnosis available
  - Trigger: `AnalysisResult.status = "Complete"`
  - Shows: transcript, STAR checks, evidence gap list, duration, word count, issue explanations, build/update Story Card CTA
  - Actions: edit transcript, build or update Story Card on Screen 7, start retry on Screen 4, open rewrite on Screen 8 if enabled
- **State name:** Transcript edited
  - Trigger: user saves transcript edits
  - Shows: updated transcript and stale-analysis warning until reanalysis completes
  - Actions: rerun analysis, continue to Screen 7 with previous analysis still visible
- **State name:** Reanalysis pending
  - Trigger: transcript edit saved and reanalysis requested
  - Shows: loading inline in diagnosis panel
  - Actions: wait, cancel back to previous complete result

### Screen 7. Story Card Editor
- **State name:** Auto-generated draft
  - Trigger: first visit after diagnosis with generated `StoryCard`
  - Shows: fields prefilled from diagnosis, highlighted low-confidence fields, follow-up probes
  - Actions: edit fields, save, launch follow-up drill on Screen 9, return to Screen 6
- **State name:** Manual edit in progress
  - Trigger: user changes any Story Card field
  - Shows: dirty form state and save button
  - Actions: save, discard changes
- **State name:** Saved Story Card
  - Trigger: save succeeds
  - Shows: persisted values and enabled follow-up drill CTA
  - Actions: start Screen 9, open Screen 8 if enabled
- **State name:** Validation error
  - Trigger: save with all core STAR fields empty
  - Shows: inline validation on missing required fields
  - Actions: complete required fields, save again

### Screen 8. Optional Rewrite View
- **State name:** No outline generated
  - Trigger: feature enabled but no `RewriteDraft` exists
  - Shows: warning that output is a draft to edit, generate outline CTA
  - Actions: generate outline, return to Screen 7
- **State name:** Outline generated
  - Trigger: `RewriteDraft` exists
  - Shows: bullet outline derived from Story Card and truth warning
  - Actions: regenerate outline, copy text, return to Screen 7
- **State name:** Rewrite error
  - Trigger: outline generation fails
  - Shows: inline error
  - Actions: retry generation

### Screen 9. Follow-up Drill
- **State name:** Probe ready
  - Trigger: launched from a saved `StoryCard`
  - Shows: linked Story Card summary, one follow-up probe, record or type controls
  - Actions: answer probe, submit, skip to next probe
- **State name:** Probe completed
  - Trigger: follow-up answer analyzed
  - Shows: summary of new weakness or improvement plus retry CTA
  - Actions: retry same probe, go to Screen 10 if retry exists, return to Screen 7
- **State name:** No probes available
  - Trigger: Story Card has empty follow-up probe list
  - Shows: fallback message and retry original story CTA
  - Actions: go to Screen 4

### Screen 10. Retry Comparison
- **State name:** Comparison available
  - Trigger: two `PracticeAttempt` records exist for same `StoryCard` and same prompt family
  - Shows: take one vs take two transcript snippets, duration difference, STAR change summary, evidence improvement summary
  - Actions: start another retry, return to Screen 7, open Screen 11
- **State name:** Comparison unavailable
  - Trigger: only one attempt exists
  - Shows: message that a second take is required
  - Actions: start retry on Screen 4 or Screen 9

### Screen 11. Session History
- **State name:** Empty history
  - Trigger: no saved `PracticeSession`
  - Shows: empty message and start first drill CTA
  - Actions: go to Screen 4
- **State name:** Populated history
  - Trigger: at least one non-deleted `PracticeSession`
  - Shows: session list with timestamps, linked Story Card title, question label, status, resume action, delete action
  - Actions: open diagnosis, open Story Card, resume follow-up drill if applicable, delete session
- **State name:** Delete confirmation
  - Trigger: user taps delete session
  - Shows: confirmation modal
  - Actions: confirm delete, cancel

### Screen 12. Privacy & Data Controls
- **State name:** Standard
  - Trigger: screen opens normally
  - Shows: local storage explanation, temporary audio processing explanation, delete session guidance, delete-all CTA
  - Actions: delete all data, return
- **State name:** Delete-all confirmation
  - Trigger: user taps delete all data
  - Shows: destructive confirmation
  - Actions: confirm wipe, cancel
- **State name:** Delete-all completed
  - Trigger: local wipe succeeds
  - Shows: success message and CTA to return to Screen 1
  - Actions: go to Screen 1

## 8. Data Model

### Entity: UserProfile
- **Fields:**
  - `id: string` — single local profile id
  - `targetRole: string` — role entered on Screen 2
  - `interviewDate: string` — ISO date from Screen 2
  - `confidenceLevel: "Low" | "Medium" | "High"` — exact UI value from Screen 2
  - `setupCompletedAt: string | null` — timestamp when setup first saved
  - `lastOpenedAt: string` — last app open timestamp
- **Relationships:** has many `PracticeSession`; has many `StoryCard`

### Entity: Theme
- **Fields:**
  - `id: string` — theme id
  - `name: string` — exact theme label shown in dashboard
  - `sortOrder: number` — dashboard ordering
- **Relationships:** has many `Question`; has many `StoryCard`

### Entity: Question
- **Fields:**
  - `id: string` — question id
  - `themeId: string` — owning theme
  - `label: string` — exact prompt label shown in UI
  - `promptText: string` — full question text
  - `questionType: "Primary question" | "Follow-up probe"` — exact UI value
  - `contextHint: string | null` — optional context shown on drill screen
  - `sortOrder: number` — content ordering
- **Relationships:** belongs to `Theme`; has many `PracticeSession`

### Entity: StoryCard
- **Fields:**
  - `id: string` — story card id
  - `themeId: string` — mapped story theme
  - `sourceSessionId: string` — originating practice session
  - `title: string` — user-facing story label
  - `situation: string` — STAR situation field
  - `task: string` — STAR task field
  - `action: string` — STAR action field
  - `result: string` — STAR result field
  - `proofPoints: string[]` — measurable facts or concrete proof
  - `notes: string` — freeform user notes
  - `status: "Draft" | "Confirmed"` — exact UI state of Story Card completion
  - `needsReviewFields: ("Situation" | "Task" | "Action" | "Result" | "Proof points")[]` — fields highlighted as low confidence
  - `updatedAt: string` — last save timestamp
- **Relationships:** belongs to `Theme`; belongs to `PracticeSession`; has many `FollowUpProbeAssignment`; has many `PracticeAttempt`; has zero or one `RewriteDraft`

### Entity: FollowUpProbeAssignment
- **Fields:**
  - `id: string` — assignment id
  - `storyCardId: string` — owning Story Card
  - `questionId: string` — linked follow-up probe question
  - `status: "Not started" | "Completed"` — exact UI value
  - `lastAnsweredAt: string | null` — last completion timestamp
- **Relationships:** belongs to `StoryCard`; belongs to `Question`

### Entity: PracticeSession
- **Fields:**
  - `id: string` — session id
  - `questionId: string` — primary question or probe used for the session
  - `storyCardId: string | null` — linked Story Card when available
  - `sessionType: "Initial drill" | "Follow-up drill" | "Retry"` — exact UI value
  - `inputMethod: "Record answer" | "Type answer"` — exact UI value
  - `status: "Draft" | "Processing" | "Diagnosed" | "Deleted"` — exact UI value
  - `startedAt: string` — session start timestamp
  - `completedAt: string | null` — finished timestamp
  - `deletedAt: string | null` — per-session deletion timestamp
- **Relationships:** belongs to `Question`; belongs to zero or one `StoryCard`; has many `PracticeAttempt`; has one `AnalysisResult`

### Entity: PracticeAttempt
- **Fields:**
  - `id: string` — attempt id
  - `sessionId: string` — owning session
  - `storyCardId: string | null` — linked Story Card
  - `takeLabel: "Take 1" | "Take 2" | "Take 3"` — exact UI value for comparison
  - `answerText: string` — typed answer or edited transcript
  - `audioDurationSec: number | null` — recorded answer duration
  - `wordCount: number` — answer word count
  - `transcriptEdited: boolean` — whether the transcript was changed after transcription
  - `createdAt: string` — attempt timestamp
- **Relationships:** belongs to `PracticeSession`; belongs to zero or one `StoryCard`

### Entity: AnalysisResult
- **Fields:**
  - `id: string` — analysis id
  - `sessionId: string` — owning session
  - `attemptId: string` — analyzed attempt
  - `status: "Complete" | "Failed"` — exact UI value
  - `situationStatus: "Present" | "Missing" | "Weak"` — exact STAR check value
  - `taskStatus: "Present" | "Missing" | "Weak"` — exact STAR check value
  - `actionStatus: "Present" | "Missing" | "Weak"` — exact STAR check value
  - `resultStatus: "Present" | "Missing" | "Weak"` — exact STAR check value
  - `lengthStatus: "On target" | "Too short" | "Too long"` — exact UI value
  - `evidenceGapLabels: ("No numbers" | "No ownership detail" | "No tradeoff explained" | "No outcome proof")[]` — exact gap labels shown in UI
  - `issueExplanations: { label: string; reason: string }[]` — one-sentence why-it-matters explanations
  - `generatedStoryCardId: string | null` — Story Card created from this result
  - `createdAt: string` — analysis timestamp
- **Relationships:** belongs to `PracticeSession`; belongs to `PracticeAttempt`; may create one `StoryCard`

### Entity: RewriteDraft
- **Fields:**
  - `id: string` — draft id
  - `storyCardId: string` — linked Story Card
  - `sourceAttemptId: string` — attempt used for generation
  - `draftType: "Bullet outline"` — exact v1 output type
  - `content: string` — generated outline text
  - `createdAt: string` — generation timestamp
- **Relationships:** belongs to `StoryCard`; belongs to `PracticeAttempt`

### Entity: PrivacySettings
- **Fields:**
  - `id: string` — single settings record id
  - `audioRetentionPolicy: "Process only, do not retain"` — exact privacy label shown in UI
  - `deleteAllCompletedAt: string | null` — last full wipe timestamp
- **Relationships:** global singleton

## 9. Screens & Acceptance Criteria

### Screen 1: Landing Page [Required v1]
**Purpose:** Explain the product, its critique-first workflow, and the local-first privacy posture.
**Visual elements:** Hero section, one-line product description, three-step workflow summary, privacy summary, primary CTA, secondary links to history and privacy for returning users.
**User actions:** Start sprint, continue sprint, open session history, open privacy controls.
**State changes:** Starting a sprint navigates to Screen 2 if `UserProfile.setupCompletedAt` is null, otherwise Screen 3. No persisted data changes occur on this screen.
**Acceptance criteria:**
- [ ] Screen 1 shows no account creation, sign-in, or subscription UI anywhere in v1.
- [ ] If `UserProfile.setupCompletedAt` is null, the primary CTA from Screen 1 navigates to Screen 2.
- [ ] If `UserProfile.setupCompletedAt` is not null, Screen 1 exposes a continue action that navigates to Screen 3.
- [ ] Screen 1 includes privacy copy that matches `PrivacySettings.audioRetentionPolicy` exactly: `Process only, do not retain`.
**Conditional states:** Returning visitor shows continue and history links. First-time visitor shows only start sprint and privacy links. If local storage boot fails, the global IndexedDB error blocks normal content.

### Screen 2: Sprint Setup [Required v1]
**Purpose:** Collect the minimum profile fields needed to contextualize drills.
**Visual elements:** Form with target role input, interview date picker, confidence selector with `Low`, `Medium`, `High`, save CTA, back action, note that v1 is behavioral-only.
**User actions:** Enter role, choose date, choose confidence, save, go back.
**State changes:** Saving writes `UserProfile.targetRole`, `UserProfile.interviewDate`, `UserProfile.confidenceLevel`, and `UserProfile.setupCompletedAt`.
**Acceptance criteria:**
- [ ] Screen 2 blocks save until `targetRole`, `interviewDate`, and `confidenceLevel` are present.
- [ ] Saving Screen 2 persists `UserProfile.targetRole`, `UserProfile.interviewDate`, and `UserProfile.confidenceLevel` in IndexedDB.
- [ ] After successful save on Screen 2, the app navigates to Screen 3 in the same session.
- [ ] Reopening Screen 2 after save repopulates all fields from `UserProfile`.
**Conditional states:** Empty form on first run. Prefilled form for returning users. Inline validation for missing or invalid values.

### Screen 3: Story Bank Dashboard [Required v1]
**Purpose:** Show story coverage, current weaknesses, and the next best drill.
**Visual elements:** Theme cards, missing proof indicators, recommended drill card, recent activity list, links to history and privacy.
**User actions:** Start recommended drill, open Story Card, open history, open privacy.
**State changes:** Starting a drill creates a new `PracticeSession` with `status = "Draft"` and navigates to Screen 4.
**Acceptance criteria:**
- [ ] If there are zero non-deleted `PracticeSession` records, Screen 3 renders the empty dashboard state instead of theme progress.
- [ ] If there is at least one `StoryCard`, Screen 3 displays cards grouped by `Theme.name`.
- [ ] Tapping the recommended drill on Screen 3 creates a `PracticeSession` with `sessionType = "Initial drill"` and `status = "Draft"`.
- [ ] Recent activity on Screen 3 excludes sessions where `PracticeSession.status = "Deleted"`.
**Conditional states:** Empty dashboard when there is no practice history. Blocking error if curated question content does not load.

### Screen 4: Question Drill [Required v1]
**Purpose:** Capture one answer to a behavioral prompt by recording or typing.
**Visual elements:** Question prompt, optional context hint, toggle between `Record answer` and `Type answer`, recording controls, timer, typed input area, submit CTA.
**User actions:** Toggle input method, record, stop, discard, type answer, submit, cancel.
**State changes:** Updating input method writes `PracticeSession.inputMethod`. Submitting creates a `PracticeAttempt` and moves `PracticeSession.status` to `Processing`.
**Acceptance criteria:**
- [ ] Screen 4 shows exactly two input modes labeled `Record answer` and `Type answer`.
- [ ] Choosing `Record answer` updates `PracticeSession.inputMethod` to `Record answer` before submission.
- [ ] Choosing `Type answer` updates `PracticeSession.inputMethod` to `Type answer` before submission.
- [ ] Submitting from Screen 4 creates a `PracticeAttempt` linked to the current `PracticeSession.id`.
- [ ] Submitting from Screen 4 sets `PracticeSession.status` to `Processing` and navigates to Screen 5.
**Conditional states:** If microphone permission is denied, recording controls are disabled and typed input remains available. If the answer is empty, submit remains blocked and an input error is shown.

### Screen 5: Processing [Required v1]
**Purpose:** Handle transcription and analysis latency without hiding what is happening.
**Visual elements:** Progress indicator, current step label, privacy note, retry/back actions on failure only.
**User actions:** Retry on failure, return to edit on failure.
**State changes:** Successful processing writes `AnalysisResult`, updates `PracticeSession.status` to `Diagnosed`, and may write `StoryCard`.
**Acceptance criteria:**
- [ ] If `PracticeSession.inputMethod = "Record answer"`, Screen 5 shows a transcription step before the analysis step.
- [ ] If `PracticeSession.inputMethod = "Type answer"`, Screen 5 skips transcription and shows analysis directly.
- [ ] On successful processing, Screen 5 writes one `AnalysisResult` with `status = "Complete"` and navigates to Screen 6.
- [ ] On processing failure, Screen 5 does not create a partial `StoryCard` and exposes a retry action.
**Conditional states:** Transcribing for recorded input, analyzing for all inputs, and error on third-party failure.

### Screen 6: Answer Diagnosis [Required v1]
**Purpose:** Show strict, explainable feedback before any rewrite assistance.
**Visual elements:** Transcript, STAR status rows, evidence gap chips, length status, one-sentence issue explanations, CTA to build or update Story Card, retry CTA.
**User actions:** Edit transcript, rerun analysis, open Story Card editor, retry the answer, open optional rewrite if enabled.
**State changes:** Saving transcript edits updates `PracticeAttempt.answerText` and `PracticeAttempt.transcriptEdited`; rerunning analysis replaces `AnalysisResult`; building a Story Card writes `StoryCard`.
**Acceptance criteria:**
- [ ] Screen 6 shows the transcript from `PracticeAttempt.answerText`.
- [ ] Screen 6 shows four STAR statuses sourced from `AnalysisResult.situationStatus`, `taskStatus`, `actionStatus`, and `resultStatus`.
- [ ] Screen 6 shows the answer length label from `AnalysisResult.lengthStatus`.
- [ ] Editing the transcript on Screen 6 sets `PracticeAttempt.transcriptEdited` to `true`.
- [ ] Building or updating from Screen 6 creates or updates one `StoryCard` and stores its id in `AnalysisResult.generatedStoryCardId`.
**Conditional states:** Diagnosis available after processing success. Inline reanalysis loading after transcript edits. Failure state only if a rerun analysis fails.

### Screen 7: Story Card Editor [Required v1]
**Purpose:** Turn diagnosis output into a reusable story artifact the user can defend.
**Visual elements:** Story title, STAR fields, proof points list, notes field, low-confidence highlights, follow-up probe list, save CTA, follow-up drill CTA.
**User actions:** Edit fields, add or remove proof points, save, discard, start follow-up drill.
**State changes:** Saving updates `StoryCard` fields and `StoryCard.status`; starting a follow-up drill creates a new `PracticeSession` with `sessionType = "Follow-up drill"`.
**Acceptance criteria:**
- [ ] The first time Screen 7 opens from Screen 6, the form is prefilled from the auto-generated `StoryCard`.
- [ ] Saving Screen 7 persists `StoryCard.situation`, `StoryCard.task`, `StoryCard.action`, `StoryCard.result`, `StoryCard.proofPoints`, and `StoryCard.notes`.
- [ ] Screen 7 highlights any field listed in `StoryCard.needsReviewFields`.
- [ ] Saving Screen 7 sets `StoryCard.status` to `Confirmed` when at least one of `situation`, `task`, `action`, or `result` is non-empty.
- [ ] Starting a follow-up drill from Screen 7 creates a `PracticeSession` with `sessionType = "Follow-up drill"` and `storyCardId` equal to the open `StoryCard.id`.
**Conditional states:** Auto-generated draft on first open, dirty form while editing, and inline validation if all core fields are left empty.

### Screen 8: Optional Rewrite View [Optional polish]
**Purpose:** Provide a constrained bullet outline after critique and Story Card confirmation.
**Visual elements:** Warning banner, generate outline CTA, generated bullet outline, regenerate CTA, back action.
**User actions:** Generate outline, regenerate, copy outline, return to Story Card editor.
**State changes:** Generating creates or replaces `RewriteDraft`.
**Acceptance criteria:**
- [ ] Screen 8 is accessible only from Screen 6 or Screen 7 and is not part of the default core path.
- [ ] Generating an outline on Screen 8 creates one `RewriteDraft` with `draftType = "Bullet outline"`.
- [ ] Screen 8 does not offer any output type other than `Bullet outline` in v1.
- [ ] Screen 8 displays a warning that the outline is a draft to edit rather than text to memorize.
**Conditional states:** No outline generated, outline generated, and inline rewrite error.

### Screen 9: Follow-up Drill [Required v1]
**Purpose:** Pressure-test a saved Story Card from a new angle.
**Visual elements:** Story summary, follow-up probe question, answer capture controls, submit CTA, skip CTA.
**User actions:** Answer the probe, submit, skip to the next probe, retry the same probe after diagnosis.
**State changes:** Submitting creates a `PracticeAttempt` under a `PracticeSession` with `sessionType = "Follow-up drill"`; completion updates `FollowUpProbeAssignment.status`.
**Acceptance criteria:**
- [ ] Screen 9 can only open with a valid `storyCardId`.
- [ ] Submitting a probe answer from Screen 9 creates a `PracticeAttempt` linked to the current follow-up `PracticeSession.id`.
- [ ] Completing a probe on Screen 9 sets the matching `FollowUpProbeAssignment.status` to `Completed`.
- [ ] If a `StoryCard` has zero `FollowUpProbeAssignment` records, Screen 9 shows the no-probes fallback state instead of a blank question area.
**Conditional states:** Probe ready, probe completed, or no probes available.

### Screen 10: Retry Comparison [Required v1]
**Purpose:** Show whether the user improved between two takes on the same story or probe family.
**Visual elements:** Side-by-side or stacked take cards, transcript snippets, duration delta, STAR change summary, evidence improvement summary, retry CTA.
**User actions:** Start another retry, return to Story Card, open history.
**State changes:** Starting another retry creates a new `PracticeSession` with `sessionType = "Retry"`.
**Acceptance criteria:**
- [ ] Screen 10 renders only when there are at least two `PracticeAttempt` records for the same `storyCardId` and prompt family.
- [ ] Screen 10 uses `PracticeAttempt.takeLabel` values to identify each compared take.
- [ ] Screen 10 shows duration data from `PracticeAttempt.audioDurationSec` when present and omits duration delta cleanly for typed answers.
- [ ] Starting a new retry from Screen 10 creates a `PracticeSession` with `sessionType = "Retry"`.
- [ ] Navigating back from Screen 10 returns to the originating `StoryCard.id` on Screen 7.
**Conditional states:** Comparison available with two or more takes. Comparison unavailable with CTA to record a second take.

### Screen 11: Session History [Required v1]
**Purpose:** Let users revisit, resume, or delete prior drills.
**Visual elements:** Session list with question label, timestamp, linked Story Card title, status, resume CTA, delete CTA.
**User actions:** Open diagnosis, open Story Card, resume follow-up drill, delete session.
**State changes:** Deleting sets `PracticeSession.status` to `Deleted` and writes `PracticeSession.deletedAt`.
**Acceptance criteria:**
- [ ] Screen 11 lists only sessions where `PracticeSession.status` is not `Deleted`.
- [ ] Each history row on Screen 11 shows `Question.label`, `PracticeSession.sessionType`, and `PracticeSession.startedAt`.
- [ ] Deleting a session from Screen 11 sets `PracticeSession.status` to `Deleted` instead of hard-removing the record immediately.
- [ ] A deleted session from Screen 11 no longer appears on Screen 3 recent activity or in the Screen 11 list after refresh.
**Conditional states:** Empty history, populated history, and delete confirmation modal.

### Screen 12: Privacy & Data Controls [Required v1]
**Purpose:** Explain storage and let the user remove their local data.
**Visual elements:** Local-first explanation, audio processing explanation, session deletion explanation, delete-all CTA, back action.
**User actions:** Confirm delete all, cancel, return.
**State changes:** Confirming delete all removes all local `UserProfile`, `StoryCard`, `PracticeSession`, `PracticeAttempt`, `AnalysisResult`, `RewriteDraft`, and `PrivacySettings` records and rewrites a fresh `PrivacySettings` record with updated `deleteAllCompletedAt`.
**Acceptance criteria:**
- [ ] Screen 12 states that transcripts, Story Cards, and session history are stored on-device by default.
- [ ] Screen 12 states that audio is processed remotely and matches `PrivacySettings.audioRetentionPolicy` exactly.
- [ ] Confirming delete all on Screen 12 clears every local entity listed in the data model except the newly recreated `PrivacySettings` record.
- [ ] After delete all completes on Screen 12, opening Screen 1 shows the first-time visitor state and opening Screen 11 shows the empty history state.
**Conditional states:** Standard, delete-all confirmation, and delete-all completed.

## 10. External Integrations
- **Service:** OpenAI Audio Transcription API
  - **Purpose:** Convert recorded answers into transcript text
  - **Specifics:** Server-side API key in Vercel env vars; use the `openai` Node SDK from a Next.js route handler; send uploaded audio blob for transcription; do not store raw audio after request completion; handle timeouts and oversized uploads by returning a retryable processing error on Screen 5
- **Service:** OpenAI Responses API
  - **Purpose:** Generate diagnosis output, Story Card draft fields, follow-up probes, and optional bullet outline
  - **Specifics:** Server-side API key in Vercel env vars; send transcript text plus structured schema request; require structured JSON output so STAR statuses and evidence gaps map directly into `AnalysisResult`; on schema validation failure, treat as processing error and do not persist partial analysis
- **Service:** Vercel
  - **Purpose:** Host the Next.js app and API routes
  - **Specifics:** Use preview and production deployments; configure environment variables for API keys; keep all persistent user data in IndexedDB on the client, not in Vercel storage

## 11. Open Risks & Assumptions to Verify Before Build
- **Risk/Assumption:** The diagnosis can feel strict and useful rather than generic — **What to verify:** manually run 20 sample behavioral answers through the prompt and confirm the JSON output correctly flags STAR gaps, evidence gaps, and length status with fewer than 3 obviously wrong judgments.
- **Risk/Assumption:** The Story Card workflow will not feel like clerical homework — **What to verify:** test the auto-generated Story Card draft on 5 sample answers and confirm the user only needs to edit suspicious fields, not retype the whole story.
- **Risk/Assumption:** The 25-question, 8-theme content set is enough to feel complete — **What to verify:** finish the full curated content sheet before coding and confirm each theme has primary questions, follow-up probes, and evidence-gap heuristics.
- **Risk/Assumption:** Remote transcription does not break the privacy promise badly enough to hurt trust — **What to verify:** pick the exact transcription endpoint, write the user-facing privacy copy, and confirm the copy still feels honest given that recorded audio leaves the device during processing.
- **Risk/Assumption:** Latency across transcription plus analysis is still acceptable in a drill loop — **What to verify:** measure end-to-end processing time on 60-second and 120-second recordings and confirm Screen 5 can reliably return results in a tolerable wait window for repeated practice.

## 12. Visual Reference
- Live prototype (static mock — visual layout reference only): https://jakebuild.github.io/idea-factory/70-voicemirror-is-a-career-anxiet/
- Screen files on GitHub: https://github.com/jakebuild/idea-factory/tree/gh-pages/70-voicemirror-is-a-career-anxiet
- Source idea: https://github.com/jakebuild/idea-factory/blob/main/70-voicemirror-is-a-career-anxiet/idea-v3.md
- Source critique: https://github.com/jakebuild/idea-factory/blob/main/70-voicemirror-is-a-career-anxiet/critique-v3.md

Prototype alignment notes:
- Adopt explicitly: the dark visual direction, mobile-first framing, and the existence of a landing page and session history view shown in the static mock.
- Reject explicitly: the prototype's current visible screen list as the source of truth. The live mock currently exposes only `Landing Page` and `Session History`; production v1 scope is the 12-screen inventory in Section 6.
- Reject explicitly: any interaction implied by the prototype that conflicts with Sections 7-10. The prototype is a static mock only and does not define production behavior.

## 13. Suggested Folder Structure

```text
70-voicemirror-is-a-career-anxiet/
  prd.md
  app/
    layout.tsx
    page.tsx
    setup/page.tsx
    dashboard/page.tsx
    drill/[sessionId]/page.tsx
    processing/[sessionId]/page.tsx
    diagnosis/[sessionId]/page.tsx
    story-card/[storyCardId]/page.tsx
    rewrite/[storyCardId]/page.tsx
    follow-up/[sessionId]/page.tsx
    comparison/[storyCardId]/page.tsx
    history/page.tsx
    privacy/page.tsx
    api/
      transcribe/route.ts
      analyze/route.ts
      rewrite/route.ts
  components/
    layout/
    landing/
    setup/
    dashboard/
    drill/
    diagnosis/
    story-card/
    comparison/
    history/
    privacy/
  lib/
    db/
      schema.ts
      client.ts
    ai/
      transcribe.ts
      analyze.ts
      rewrite.ts
    content/
      themes.ts
      questions.ts
    validation/
      profile.ts
      session.ts
      analysis.ts
  stores/
    app-store.ts
  types/
    entities.ts
  public/
  styles/
    globals.css
```
