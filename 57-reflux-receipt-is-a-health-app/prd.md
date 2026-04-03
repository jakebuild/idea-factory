# PRD: Reflux Receipt

## 1. Overview
Reflux Receipt is a local-first iPhone app for people who already suspect late eating contributes to nighttime reflux and want one structured 7-night tracking run instead of an open-ended health journal. The app guides a user through a fixed nightly log and morning check-in, then produces a blunt final report showing which tracked variables aligned with symptoms, which did not, and one single-variable follow-up experiment. It is a pattern-tracking utility, not a diagnostic tool, predictor, or ongoing coach.

## 2. Target User
The target user is an adult who already experiences occasional nighttime heartburn or reflux, already suspects dinner timing or a few food categories may matter, and wants a cleaner answer than memory or scattered Notes app entries. Existing solutions fail because they are either generic reflux articles, overly clinical tools, or endless journaling products that ask for too much data and never give a clear finish line. This user wants a short protocol, fast daily input, and a concise report they can review themselves.

## 3. Tech Stack
- Platform: iOS native
- Language + framework: Swift 6 + SwiftUI
- Key libraries: `SwiftData` for local persistence, `UserNotifications` for reminder scheduling, `ShareLink` for text sharing, `PDFKit` + `UIGraphicsPDFRenderer` for optional PDF export, `Foundation` date/time APIs for local-time reminder logic
- Hosting / deployment: No backend; distribute via TestFlight first, then App Store
- Why this stack fits the solo dev + 2–4 week constraint: single-platform iOS keeps notification behavior, local storage, share sheet support, and PDF generation simpler than cross-platform. The product has no auth, no backend, and no sync requirement, so native Apple frameworks reduce moving parts and App Store risk.

## 4. V1 Scope
- **Required v1** Safety-first onboarding that explains the 7-night protocol, what is tracked, and what the final report does and does not mean.
- **Required v1** Protocol setup with usual bedtime, night reminder time, morning reminder time, notification preference toggles, and one optional free-text suspected trigger note.
- **Required v1** Home progress tracker showing current night, completed mornings, next required action, reminder status, and report lock state.
- **Required v1** Night log with meal time, planned lie-down time, portion size, and up to two fixed trigger tags.
- **Required v1** Night saved confirmation with next reminder time and return to Home.
- **Required v1** Morning check-in with symptom severity, sleep disruption yes/no, and optional note.
- **Required v1** Final 7-night report generated after all 7 morning check-ins are completed.
- **Required v1** Settings screen for reminder controls, legal/safety copy, and delete-all-data.
- **Optional polish** Plain-text report copy/share from the final report.
- **Optional polish** PDF export of the final report.

## 5. Out of Scope
- Instant risk bands
- Predictive scores or time-window forecasts
- Camera scan or image recognition
- Nutrition database integrations
- Ongoing coaching or chat
- Community features
- Clinician dashboard
- Medication tracking
- Wearables
- Payments
- Multi-week comparison flow
- Standalone Findings Preview screen
- Authentication or cloud sync

## 6. Screen Inventory
| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Welcome / Safety Framing | Required v1 |
| 2 | Protocol Setup | Required v1 |
| 3 | Home / Progress Tracker | Required v1 |
| 4 | Night Log | Required v1 |
| 5 | Night Logged Confirmation | Required v1 |
| 6 | Morning Check-In | Required v1 |
| 7 | Final 7-Night Report | Required v1 |
| 8 | Settings / Legal / Data | Required v1 |
| 9 | Report Export / Share | Optional polish |

## 7. State Model
No authentication exists in v1. The app always runs in a local-only unauthenticated state. There is no authenticated state.

### Screen 1: Welcome / Safety Framing
- **App bootstrap loading:** triggered on cold launch before `AppState` is loaded; shows launch/loading treatment only; no actions.
- **First-run onboarding:** triggered when `AppState.has_completed_onboarding = false`; shows protocol overview, safety framing, and CTA to Screen 2; action: continue.
- **Returning user redirect:** triggered when `AppState.has_completed_onboarding = true`; Screen 1 is skipped and app opens Screen 3 or Screen 7 depending on `ProtocolRun.status`.
- **Bootstrap error:** triggered when local persistence cannot load; shows blocking error copy and retry action.

### Screen 2: Protocol Setup
- **Editable setup:** triggered when active protocol does not exist; shows bedtime, reminder times, reminder toggles, optional note, and protocol summary; actions: edit fields, enable notifications, start protocol.
- **Notification permission denied:** triggered when `AppState.notification_permission = denied`; shows inline warning and keeps reminder toggles disabled; actions: continue without reminders, open iOS Settings.
- **Validation error:** triggered when required fields are missing or invalid; shows inline field errors; actions: correct input.
- **Save in progress:** triggered after tapping Start; disables CTA until `ProtocolRun` and 7 `ProtocolNight` rows are created.
- **Save error:** triggered when setup persistence fails; shows retry action and preserves entered values in memory.

### Screen 3: Home / Progress Tracker
- **No active protocol:** triggered when no `ProtocolRun` exists or after delete-all-data; shows CTA to Screen 2; actions: start protocol.
- **Active protocol, night pending:** triggered when current `ProtocolNight.night_log_completed_at = null`; shows current night number, remaining nights, reminder status, and CTA to Screen 4; actions: start tonight's log, open settings.
- **Active protocol, morning pending:** triggered when current night log is complete and `ProtocolNight.morning_checkin_completed_at = null`; shows waiting state and CTA to Screen 6; actions: complete morning check-in, open settings.
- **Protocol complete, report ready:** triggered when `ProtocolRun.status = completed` and `ReportSnapshot.status = ready`; shows completion summary and CTA to Screen 7; actions: view final report, open settings.
- **Report locked:** triggered for all active runs before completion; shows locked report card with exact requirement: `Complete all 7 morning check-ins to unlock your final report`; no findings shown.
- **Missed-night state:** triggered when a scheduled day has passed without a night log or morning check-in for the current night; shows recovery copy and keeps the user on the same `night_number`; actions: log now, skip and continue only from Settings reset flow is out of scope.
- **Loading:** triggered while aggregating counts from local data.
- **Error:** triggered when required protocol records are missing or corrupted; shows reset-data CTA.
- **Empty state:** handled by `No active protocol`; not a separate screen.

### Screen 4: Night Log
- **Editable log:** triggered when current `ProtocolNight.night_log_completed_at = null`; shows time inputs, portion size selector, fixed trigger chips, and save CTA; actions: edit values, select up to two tags, save.
- **Tag limit reached:** triggered when two trigger tags are already selected; third tap shows inline message and does not change data until one tag is deselected.
- **Validation error:** triggered when meal time or planned lie-down time is missing; shows inline error and disables save.
- **Save in progress:** triggered after tap on save; disables controls until persistence succeeds.
- **Save error:** triggered when persistence fails; shows retry and keeps entered values.
- **Loading:** triggered when current night record is fetched.
- **Error:** triggered when there is no active protocol night to edit.

### Screen 5: Night Logged Confirmation
- **Saved state:** triggered immediately after a successful night log save; shows saved confirmation, next morning reminder time, and report lock copy; actions: return Home.
- **Direct-entry redirect:** triggered if opened without a just-saved night log; redirects to Screen 3.
- **Loading:** triggered for reminder-time lookup.
- **Error:** triggered if reminder metadata cannot load; still allows return Home.

### Screen 6: Morning Check-In
- **Editable check-in:** triggered when current `ProtocolNight.night_log_completed_at != null` and `morning_checkin_completed_at = null`; shows severity choices, sleep disruption toggle, optional note, and complete CTA; actions: select severity, toggle sleep disruption, enter note, complete.
- **Validation error:** triggered when severity is not selected; shows inline error and disables completion.
- **Save in progress:** triggered after completion tap; disables controls until persistence succeeds.
- **Save error:** triggered when persistence fails; shows retry and preserves inputs.
- **Already completed redirect:** triggered when the current morning check-in already exists; redirects to Screen 3.
- **Loading:** triggered while loading the current night record.
- **Error:** triggered when no eligible night exists for check-in.

### Screen 7: Final 7-Night Report
- **Locked:** triggered when `ProtocolRun.status != completed` or `ReportSnapshot.status != ready`; shows lock copy and no findings; actions: return Home.
- **Report ready:** triggered when all 7 morning check-ins are complete and report generation succeeded; shows counts, signal rows, no-signal rows, insufficient-data rows, and one next experiment; actions: copy/share text if enabled, open Screen 9 if implemented.
- **Generating:** triggered once after the 7th morning check-in while `ReportSnapshot.status = generating`; shows spinner and blocks interaction except back.
- **Generation error:** triggered when report generation fails; shows retry action.
- **Empty report fallback:** triggered when all 7 nights are complete but no variable crosses a signal or no-signal threshold; shows `No usable signal in this run` plus the generic next experiment.

### Screen 8: Settings / Legal / Data
- **Default:** shows reminder toggles, reminder times, safety/legal copy, export entry point if Screen 9 is implemented, and delete-all-data action; actions: edit reminder settings, open iOS Settings if permission denied, delete all data.
- **Notification permission denied:** triggered when `AppState.notification_permission = denied`; shows inline help text and disables reminder toggles.
- **Delete confirmation modal:** triggered on delete action; shows destructive confirmation; actions: confirm delete, cancel.
- **Delete in progress:** triggered after confirm; clears all local entities and scheduled notifications.
- **Delete error:** triggered when local delete fails; shows retry.
- **Loading:** triggered while reading settings.
- **Error:** triggered when settings cannot load.

### Screen 9: Report Export / Share
- **Not implemented:** if Optional polish is skipped, the entry point from Screen 7 and Screen 8 does not appear.
- **Ready:** triggered when `ReportSnapshot.status = ready`; shows copy text, share text, and optional export PDF actions; actions: copy summary text, open share sheet, generate PDF.
- **PDF generating:** triggered after tapping export PDF; disables export button until file generation completes.
- **PDF error:** triggered if PDF generation fails; shows retry.
- **Loading:** triggered while fetching the report snapshot.
- **Error:** triggered when no report snapshot exists.

## 8. Data Model
### Entity: AppState
- **Fields:**
  - `id: UUID` — singleton record id
  - `has_completed_onboarding: Bool` — whether Screen 1 should be skipped on next launch
  - `notification_permission: enum(unknown, granted, denied)` — local cache of iOS notification permission status
  - `active_protocol_run_id: UUID?` — current run shown on Screen 3
  - `last_opened_at: Date` — latest app open timestamp
- **Relationships:** references current `ProtocolRun`

### Entity: ProtocolRun
- **Fields:**
  - `id: UUID` — run id
  - `status: enum(setup, active, completed)` — overall protocol state
  - `started_at: Date` — when the run was created
  - `completed_at: Date?` — when the 7th morning check-in was saved
  - `usual_bedtime_minutes: Int` — minutes from midnight for the user's typical bedtime
  - `night_reminder_enabled: Bool` — whether the night reminder is scheduled
  - `night_reminder_minutes: Int` — minutes from midnight for the night reminder
  - `morning_reminder_enabled: Bool` — whether the morning reminder is scheduled
  - `morning_reminder_minutes: Int` — minutes from midnight for the morning reminder
  - `suspected_trigger_note: String?` — optional free-text note entered in setup
  - `required_night_count: Int` — always `7` in v1
  - `completed_morning_count: Int` — derived and persisted count for fast Home rendering
  - `current_night_number: Int` — next `ProtocolNight.night_number` the user should act on
- **Relationships:** has many `ProtocolNight`; has one `ReportSnapshot`

### Entity: ProtocolNight
- **Fields:**
  - `id: UUID` — night row id
  - `protocol_run_id: UUID` — owning run id
  - `night_number: Int` — values `1` through `7`
  - `scheduled_date_local: Date` — local calendar date anchor for this night
  - `meal_time_minutes: Int?` — minutes from midnight for the evening meal
  - `planned_lie_down_time_minutes: Int?` — minutes from midnight for planned lie-down
  - `portion_size: enum(small, medium, large)?` — exact UI values
  - `trigger_tags: [enum(spicy, tomato/citrus, fried/fatty, alcohol, caffeine/carbonated, chocolate/mint)]` — 0 to 2 selected tags
  - `night_log_completed_at: Date?` — when Screen 4 save succeeded
  - `symptom_severity: enum(none, mild, moderate, severe)?` — exact morning UI values
  - `sleep_disruption: Bool?` — `true` = yes, `false` = no
  - `morning_note: String?` — optional note from Screen 6
  - `morning_checkin_completed_at: Date?` — when Screen 6 save succeeded
- **Relationships:** belongs to `ProtocolRun`

### Entity: ReportSnapshot
- **Fields:**
  - `id: UUID` — snapshot id
  - `protocol_run_id: UUID` — owning run id
  - `status: enum(locked, generating, ready, error)` — report availability state
  - `generated_at: Date?` — when report generation finished
  - `summary_text: String?` — canonical plain-text report used by Screen 7 and Screen 9
  - `signal_items_json: String?` — serialized ordered rows for signals section
  - `no_signal_items_json: String?` — serialized ordered rows for no-signal section
  - `insufficient_data_items_json: String?` — serialized ordered rows for insufficient-data section
  - `next_experiment_text: String?` — one single-variable follow-up suggestion
  - `generation_error_message: String?` — last generation error if status is `error`
- **Relationships:** belongs to `ProtocolRun`

## 9. Screens & Acceptance Criteria
### Screen 1: Welcome / Safety Framing [Required v1]
**Purpose:** explain the 7-night experiment and set non-diagnostic expectations before setup.
**Visual elements:** top app bar, headline, short protocol explainer, safety copy card, 3-step protocol summary, finish-line/report preview, primary CTA.
**User actions:** tap continue.
**State changes:** tapping continue sets `AppState.has_completed_onboarding = true` and navigates to Screen 2.
**Acceptance criteria:**
- [ ] Screen 1 is shown only when `AppState.has_completed_onboarding = false`.
- [ ] Tapping the primary CTA on Screen 1 persists `AppState.has_completed_onboarding = true` before navigating to Screen 2.
- [ ] Screen 1 copy does not use diagnostic or predictive claims and explicitly states the app is for symptom tracking, not diagnosis.
**Conditional states:** loading shows bootstrap spinner; error shows retry button if `AppState` cannot load.

### Screen 2: Protocol Setup [Required v1]
**Purpose:** collect the minimal settings needed to run the 7-night protocol.
**Visual elements:** back button, bedtime picker, night reminder toggle and time picker, morning reminder toggle and time picker, optional suspected trigger note, short explanation of what gets logged, primary CTA.
**User actions:** edit times, toggle reminders, type optional note, open notification permission prompt, start protocol.
**State changes:** starting protocol creates one `ProtocolRun`, 7 `ProtocolNight` rows with `night_number` 1-7, one `ReportSnapshot` with `status = locked`, sets `AppState.active_protocol_run_id`, and schedules notifications when enabled and permitted.
**Acceptance criteria:**
- [ ] Tapping Start on Screen 2 creates exactly one `ProtocolRun` with `status = active`, `required_night_count = 7`, and `current_night_number = 1`.
- [ ] Tapping Start on Screen 2 creates exactly 7 `ProtocolNight` rows linked by `protocol_run_id`.
- [ ] If night reminders are enabled and `AppState.notification_permission = granted`, the app stores `ProtocolRun.night_reminder_enabled = true` and `ProtocolRun.night_reminder_minutes` from the chosen time.
- [ ] If morning reminders are enabled and `AppState.notification_permission = granted`, the app stores `ProtocolRun.morning_reminder_enabled = true` and `ProtocolRun.morning_reminder_minutes` from the chosen time.
- [ ] If notification permission is denied, Screen 2 disables reminder toggles and allows the user to continue with `night_reminder_enabled = false` and `morning_reminder_enabled = false`.
- [ ] The optional free-text field on Screen 2 persists only to `ProtocolRun.suspected_trigger_note`.
**Conditional states:** validation error for missing bedtime; loading while saving; save error with retry.

### Screen 3: Home / Progress Tracker [Required v1]
**Purpose:** tell the user what to do next and how far they are from completion.
**Visual elements:** progress header, night counter, completed mornings count, next-action card, reminder status card, locked-report card or report-ready card, entry points to Screen 4, Screen 6, Screen 7, and Screen 8.
**User actions:** open night log, open morning check-in, open final report, open settings.
**State changes:** navigation only; Home reflects persisted protocol and report fields.
**Acceptance criteria:**
- [ ] When `ProtocolRun.current_night_number = 3` and `ProtocolRun.completed_morning_count = 2`, Screen 3 shows night 3 as the next required step.
- [ ] Before `ProtocolRun.status = completed`, Screen 3 shows a locked report state and does not display any signal rows or findings text.
- [ ] After the 7th morning check-in sets `ProtocolRun.status = completed`, Screen 3 shows a report-ready CTA to Screen 7.
- [ ] If no `ProtocolRun` exists, Screen 3 shows only the setup CTA and no progress metrics.
- [ ] Navigating back to Screen 3 from Screen 4, Screen 5, Screen 6, Screen 7, or Screen 8 returns to the same active `ProtocolRun.id`.
**Conditional states:** no-active-protocol empty state; missed-night warning state; loading while counts aggregate; error with reset-data CTA.

### Screen 4: Night Log [Required v1]
**Purpose:** capture the nightly variables for the current protocol night.
**Visual elements:** back button, current night label, meal time picker, planned lie-down time picker, portion-size segmented control, 6 fixed trigger chips, helper text showing `Select up to 2`, primary save CTA.
**User actions:** edit meal time, edit planned lie-down time, choose one portion size, select or deselect trigger tags, save.
**State changes:** save writes values into the current `ProtocolNight`, sets `night_log_completed_at`, and routes to Screen 5.
**Acceptance criteria:**
- [ ] Screen 4 writes `meal_time_minutes`, `planned_lie_down_time_minutes`, `portion_size`, `trigger_tags`, and `night_log_completed_at` to the current `ProtocolNight`.
- [ ] Screen 4 allows at most 2 values in `ProtocolNight.trigger_tags`.
- [ ] Screen 4 exposes exactly these trigger labels and no others: `spicy`, `tomato/citrus`, `fried/fatty`, `alcohol`, `caffeine/carbonated`, `chocolate/mint`.
- [ ] Screen 4 cannot save until both `meal_time_minutes` and `planned_lie_down_time_minutes` are present and `portion_size` is selected.
- [ ] After save succeeds on Screen 4, reopening Screen 4 for the same `ProtocolNight.id` shows the persisted values.
- [ ] Screen 4 does not display risk scores, predictive language, or findings.
**Conditional states:** loading while current night loads; validation errors inline; save error with retry.

### Screen 5: Night Logged Confirmation [Required v1]
**Purpose:** confirm that the evening log was saved and tell the user what happens next.
**Visual elements:** success state, saved summary, next morning reminder time, progress-to-report copy, return-home CTA.
**User actions:** return Home.
**State changes:** no new data; uses persisted reminder data for display.
**Acceptance criteria:**
- [ ] Screen 5 appears only after a successful Screen 4 save for the current `ProtocolNight.id`.
- [ ] Screen 5 displays the next morning reminder time from `ProtocolRun.morning_reminder_minutes` when `ProtocolRun.morning_reminder_enabled = true`.
- [ ] Screen 5 states that the final report unlocks only after all 7 morning check-ins are completed.
- [ ] Tapping the primary CTA on Screen 5 returns to Screen 3 without changing any persisted fields.
**Conditional states:** if reminder data cannot load, Screen 5 still renders success copy and return-home CTA.

### Screen 6: Morning Check-In [Required v1]
**Purpose:** capture the symptom outcome for the previous night.
**Visual elements:** back button, severity selector with four options, sleep disruption yes/no toggle, optional note text area, primary completion CTA.
**User actions:** select severity, toggle sleep disruption, type note, complete check-in.
**State changes:** save writes morning values to the current `ProtocolNight`, increments `ProtocolRun.completed_morning_count`, advances `ProtocolRun.current_night_number`, sets `ProtocolRun.status = completed` and `completed_at` after night 7, and triggers report generation.
**Acceptance criteria:**
- [ ] Screen 6 writes one of exactly these values to `ProtocolNight.symptom_severity`: `none`, `mild`, `moderate`, `severe`.
- [ ] Screen 6 writes `sleep_disruption = true` for yes and `sleep_disruption = false` for no.
- [ ] Screen 6 persists optional text only to `ProtocolNight.morning_note`.
- [ ] Completing Screen 6 for night 4 increments `ProtocolRun.completed_morning_count` by 1 and sets `ProtocolRun.current_night_number = 5`.
- [ ] Completing Screen 6 for night 7 sets `ProtocolRun.status = completed`, sets `ProtocolRun.completed_at`, and sets `ReportSnapshot.status = generating`.
- [ ] Screen 6 cannot save until `ProtocolNight.symptom_severity` is selected.
**Conditional states:** loading while current night loads; already-completed redirect; save error with retry.

### Screen 7: Final 7-Night Report [Required v1]
**Purpose:** show the finished protocol summary after all 7 nights are complete.
**Visual elements:** report header, completed-night summary, signal section, no-signal section, insufficient-data section, one next-experiment card, optional export/share entry point.
**User actions:** read report, copy/share text if enabled, open Screen 9 if implemented, go back Home.
**State changes:** first render after generation persists `ReportSnapshot.status = ready`, `generated_at`, `summary_text`, and ordered report sections.
**Acceptance criteria:**
- [ ] Screen 7 remains locked until `ProtocolRun.status = completed` and `ReportSnapshot.status = ready`.
- [ ] Screen 7 never appears as a mid-protocol findings preview; there is no separate v1 screen for partial findings.
- [ ] Screen 7 includes counts for all 7 `ProtocolNight` rows and states how many nights had symptoms where `symptom_severity != none`.
- [ ] A report item is labeled `moderate signal` only if the exposure occurred on at least 3 completed nights and symptoms occurred on at least 75% of those exposed nights.
- [ ] A report item is labeled `weak signal` only if the exposure occurred on at least 2 completed nights and symptoms occurred on at least 75% of those exposed nights but the `moderate signal` rule is not met.
- [ ] A report item is labeled `no signal` only if the exposure occurred on at least 2 completed nights and symptoms occurred on fewer than 50% of those exposed nights.
- [ ] Exposures with fewer than 2 occurrences are listed only in the insufficient-data section.
- [ ] Screen 7 generates exactly one `next_experiment_text` that changes one variable only.
- [ ] If no exposure meets `weak signal`, `moderate signal`, or `no signal`, Screen 7 shows `No usable signal in this run` and still renders one generic next experiment.
**Conditional states:** locked state before completion; generating spinner after final morning save; generation error with retry.

### Screen 8: Settings / Legal / Data [Required v1]
**Purpose:** manage reminders, review safety copy, and clear local data.
**Visual elements:** reminder toggles and times, notification permission help, safety/legal copy, delete-all-data button, optional export entry if Screen 9 is implemented.
**User actions:** toggle reminders, change reminder times, open iOS settings, delete all data.
**State changes:** edits update `ProtocolRun` reminder fields and reschedule local notifications; delete clears `AppState.active_protocol_run_id`, sets `AppState.has_completed_onboarding = false`, and removes `ProtocolRun`, `ProtocolNight`, and `ReportSnapshot`.
**Acceptance criteria:**
- [ ] Updating reminder times on Screen 8 persists the new values to `ProtocolRun.night_reminder_minutes` and `ProtocolRun.morning_reminder_minutes`.
- [ ] Disabling a reminder on Screen 8 persists `night_reminder_enabled = false` or `morning_reminder_enabled = false` and removes the scheduled local notification.
- [ ] If `AppState.notification_permission = denied`, Screen 8 shows permission-help copy and does not silently re-enable reminder fields.
- [ ] Confirming delete-all-data removes all `ProtocolRun`, `ProtocolNight`, and `ReportSnapshot` records and sets `AppState.active_protocol_run_id = null`.
- [ ] After delete-all-data, the next app launch shows Screen 1 because `AppState.has_completed_onboarding = false`.
**Conditional states:** loading while reading settings; delete confirmation modal; error if delete fails.

### Screen 9: Report Export / Share [Optional polish]
**Purpose:** let the user copy or export the final report after completion.
**Visual elements:** report date range, copy text action, share text action, PDF export action if implemented.
**User actions:** copy summary text, open native share sheet, generate PDF.
**State changes:** no required persisted changes; PDF generation may write a temporary local file only.
**Acceptance criteria:**
- [ ] Screen 9 is reachable only when `ReportSnapshot.status = ready`.
- [ ] Copy action on Screen 9 copies `ReportSnapshot.summary_text` exactly.
- [ ] Share action on Screen 9 passes `ReportSnapshot.summary_text` to the native share sheet.
- [ ] If PDF export is implemented, the PDF content matches `ReportSnapshot.summary_text` and the visible signal sections from Screen 7.
- [ ] If Screen 9 is not implemented, no dead navigation target or button to Screen 9 appears anywhere in the app.
**Conditional states:** loading while fetching report snapshot; PDF error with retry.

## 10. External Integrations
- **Service:** Apple `UserNotifications`
  - **Purpose:** schedule nightly and morning local reminders
  - **Specifics:** request permission with `UNUserNotificationCenter.requestAuthorization`; schedule repeating calendar notifications keyed by `ProtocolRun.id`; known gotchas: denied permission path must still allow protocol completion, time-zone changes require notification refresh on app open, and repeating schedules should use local calendar components rather than UTC timestamps.
- **Service:** Apple share sheet (`ShareLink` / `UIActivityViewController`)
  - **Purpose:** share the final plain-text report
  - **Specifics:** no auth or API keys; only available when `ReportSnapshot.status = ready`; known gotcha: share targets vary by installed apps, so success is handing off the content, not confirming downstream delivery.
- **Service:** Apple PDF generation (`UIGraphicsPDFRenderer` / `PDFKit`)
  - **Purpose:** optional PDF export of the final report
  - **Specifics:** no auth or network dependency; generate locally from `ReportSnapshot.summary_text`; known gotcha: PDF export should stay optional because formatting polish can expand scope quickly.

## 11. Open Risks & Assumptions to Verify Before Build
- **Risk/Assumption:** Users will complete both the night log and morning check-in for 7 nights — **What to verify:** prototype the flow with 5-10 test users and confirm at least half complete 5 of 7 mornings without staff intervention.
- **Risk/Assumption:** Reminder behavior will be reliable enough without a backend — **What to verify:** test local notification scheduling across permission-denied, app-killed, and timezone-change scenarios on current iOS versions.
- **Risk/Assumption:** The report logic will feel honest rather than fake-smart — **What to verify:** run the exact `weak signal` and `moderate signal` thresholds against sample data and confirm the output is understandable and never implies diagnosis or causality.
- **Risk/Assumption:** The fixed trigger taxonomy plus one free-text note is enough for v1 — **What to verify:** interview users with suspected reflux and check whether missing-trigger frustration appears before night 3.
- **Risk/Assumption:** Export is worth the complexity — **What to verify:** test whether target users actually want to copy/share the report before spending time polishing PDF output.

## 12. Visual Reference
- Live prototype (static mock — visual layout reference only): https://jakebuild.github.io/idea-factory/57-reflux-receipt-is-a-health-app/
- Screen files on GitHub: https://github.com/jakebuild/idea-factory/tree/gh-pages/57-reflux-receipt-is-a-health-app
- Source idea: https://github.com/jakebuild/idea-factory/blob/main/57-reflux-receipt-is-a-health-app/idea-v3.md
- Source critique: https://github.com/jakebuild/idea-factory/blob/main/57-reflux-receipt-is-a-health-app/critique-v3.md
- Adopt from prototype: mobile visual direction, general card layout, airy spacing, progress-forward hierarchy, and the presence of screens corresponding to this PRD's Screen 1, Screen 2, Screen 3, Screen 4, Screen 5, Screen 6, Screen 7, Screen 8, and Screen 9.
- Reject from prototype: standalone `Findings Preview`, bottom-nav `Findings` tab, predictive or medicalized copy such as `clinical`, `algorithm`, `synthesis`, `forecast`, `encrypted`, or similar authority-signaling language, and any trigger choices not in the source taxonomy.
- Production requirement override: when the prototype conflicts with the source idea or this PRD, this PRD wins. The prototype is a static layout reference only, not an interaction or content spec.

## 13. Suggested Folder Structure
```text
RefluxReceipt/
├── RefluxReceiptApp.swift
├── App/
│   ├── AppRouter.swift
│   ├── AppStateStore.swift
│   └── DependencyContainer.swift
├── Models/
│   ├── AppState.swift
│   ├── ProtocolRun.swift
│   ├── ProtocolNight.swift
│   ├── ReportSnapshot.swift
│   └── Enums.swift
├── Features/
│   ├── Welcome/
│   │   ├── WelcomeView.swift
│   │   └── WelcomeViewModel.swift
│   ├── Setup/
│   │   ├── SetupView.swift
│   │   └── SetupViewModel.swift
│   ├── Home/
│   │   ├── HomeView.swift
│   │   └── HomeViewModel.swift
│   ├── NightLog/
│   │   ├── NightLogView.swift
│   │   └── NightLogViewModel.swift
│   ├── NightConfirmation/
│   │   └── NightConfirmationView.swift
│   ├── MorningCheckIn/
│   │   ├── MorningCheckInView.swift
│   │   └── MorningCheckInViewModel.swift
│   ├── Report/
│   │   ├── FinalReportView.swift
│   │   ├── FinalReportViewModel.swift
│   │   ├── ReportFormatter.swift
│   │   └── ReportRuleEngine.swift
│   ├── Settings/
│   │   ├── SettingsView.swift
│   │   └── SettingsViewModel.swift
│   └── Export/
│       ├── ReportExportView.swift
│       └── PDFReportExporter.swift
├── Services/
│   ├── Persistence/
│   │   ├── SwiftDataStack.swift
│   │   └── Repositories.swift
│   ├── Notifications/
│   │   └── ReminderScheduler.swift
│   └── Sharing/
│       └── ShareService.swift
├── DesignSystem/
│   ├── Colors.swift
│   ├── Typography.swift
│   └── Components/
│       ├── PrimaryButton.swift
│       ├── CardView.swift
│       ├── ProgressRing.swift
│       └── TagChip.swift
└── Tests/
    ├── ReportRuleEngineTests.swift
    ├── SetupViewModelTests.swift
    ├── NightLogViewModelTests.swift
    └── MorningCheckInViewModelTests.swift
```
