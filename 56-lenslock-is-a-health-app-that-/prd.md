# PRD: LensLock

## 1. Overview
LensLock is an iPhone app for monthly reusable soft contact lens wearers who sometimes fall asleep in their lenses. The app runs a finite 14-night challenge: each night it creates one unresolved closeout task and asks the user to confirm `Lenses out`, `Still in`, or `Skip tonight`, then records whether that night ended `cleared`, `missed`, or `skipped` in a local scorecard.

## 2. Target User
This user wears monthly reusable soft lenses, knows they should remove them before sleep, and still occasionally fails at the bedtime moment. Generic alarms and reminders fail because they do not preserve a purpose-built unresolved nightly state, do not offer a one-tap bedtime decision flow, and do not maintain a 14-night compliance record in one place.

## 3. Tech Stack
- Platform: iOS native
- Language + framework: Swift 6 + SwiftUI
- Key libraries: SwiftData for local persistence, UserNotifications for local notifications and deep links into the closeout flow, AppStorage for lightweight app flags
- Hosting / deployment: No backend; distribute through TestFlight first, then App Store if the pilot passes
- Why this stack fits the solo dev + 2–4 week constraint: SwiftUI + SwiftData keeps the app fully local, removes backend/auth complexity, matches the notification-heavy product shape, and is the fastest path for a solo developer shipping one Apple-platform validation app

## 4. V1 Scope
- **Required v1** Single-device local app with no account system and no network dependency for core behavior
- **Required v1** First-run intro that explains the 14-night challenge and routes into setup
- **Required v1** Setup flow for one target bedtime time, one reminder interval, and notification permission request
- **Required v1** Nightly closeout record creation for each challenge day from day 1 through day 14
- **Required v1** Bedtime notification that opens Screen 4 with `Lenses out`, `Still in`, and `Skip tonight`
- **Required v1** Repeated local reminders after `Still in` until the night is cleared, skipped, paused, or marked missed
- **Required v1** Home screen showing current challenge day, tonight status, next reminder timing, and compact 14-night progress row
- **Required v1** History / scorecard screen showing all 14 nights and aggregate completion rate
- **Required v1** Settings to change bedtime, change reminder interval, pause/resume the challenge, and reset all local data
- **Optional polish** Subtle local notification actions for `Lenses out` and `Still in` directly from the notification without opening the app
- **Optional polish** Secondary explanatory trust copy on onboarding and settings, as long as it stays non-medical and does not change the core flow

## 5. Out of Scope
- NFC or accessory-based check-in
- Wear-time timers
- Morning start logs
- Missed-log reconstruction
- Weekly summaries
- Replacement tracking
- Biweekly lens support
- Extended-wear lens support
- Medical scoring or clinical risk percentages
- Analytics dashboards
- Widgets
- Apple Health
- Social features
- Payments
- Cross-device sync
- Account creation or sign-in

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Welcome / 14-Night Challenge Intro | Required v1 |
| 2 | Onboarding / Bedtime Setup | Required v1 |
| 3 | Home / Tonight Status | Required v1 |
| 4 | Bedtime Closeout | Required v1 |
| 5 | Reminder Escalation / Still In | Required v1 |
| 6 | Challenge History / Scorecard | Required v1 |
| 7 | Settings | Required v1 |

## 7. State Model

### Global app states
- **Unauthenticated:** Always active in v1 because the app has no account system. The user can use the full app without sign-in.
- **Authenticated:** Not applicable in v1. No authenticated UI is implemented.
- **Initial local load:** Triggered on app launch while `AppState.hasCompletedSetup` and the active `ChallengeRun` are loaded from disk. Shows a blocking launch/loading view for up to one local read cycle.
- **Persistence error:** Triggered if SwiftData read/write fails. Shows an inline error message with `Retry` and does not silently discard state.
- **Notification permission unknown:** Triggered when `AppState.notificationPermissionStatus = not_determined`. Shows the setup CTA to request permission.
- **Notification permission denied:** Triggered when `AppState.notificationPermissionStatus = denied`. Shows a settings guidance card with a deep link to iOS Settings and disables reminder scheduling.

### Screen 1: Welcome / 14-Night Challenge Intro
- **Default intro:** Triggered when `AppState.hasCompletedSetup = false`. Shows product name, challenge summary, who it is for, and a primary CTA to continue to Screen 2.
- **Prototype mismatch decision:** The live mock shows a secondary `View Schedule` CTA. Reject this in v1. Screen 1 has one required primary CTA only.

### Screen 2: Onboarding / Bedtime Setup
- **Editable setup:** Triggered before first save. Shows target bedtime picker, reminder interval selector, and notification permission CTA.
- **Permission requested:** Triggered after tapping `Enable notifications`. Shows current OS permission result from `AppState.notificationPermissionStatus`.
- **Ready to start:** Triggered when a bedtime and reminder interval are selected. Shows primary CTA to save and create the challenge run.
- **Permission denied condition:** Shows inline warning that reminders will not fire until permission is enabled in iOS Settings, but allows setup completion so the user can still view the app.

### Screen 3: Home / Tonight Status
- **Pending tonight:** Triggered when the active `NightRecord.status = pending` and current time is before `NightRecord.missedAt`. Shows current day, pending badge, next reminder time, open-closeout CTA, and progress row.
- **Cleared tonight:** Triggered when `NightRecord.status = cleared`. Shows success state and disables reminder scheduling for that night.
- **Skipped tonight:** Triggered when `NightRecord.status = skipped`. Shows skipped state and disables reminder scheduling for that night.
- **Missed tonight:** Triggered when `NightRecord.status = missed`. Shows missed state, no closeout CTA for that night, and keeps the record in history.
- **Paused challenge:** Triggered when `ChallengeRun.isPaused = true`. Shows pause banner, hides next reminder time, and offers `Resume challenge`.
- **Challenge complete:** Triggered when `ChallengeRun.completedAt` is set after day 14. Shows final completion summary and CTA to Screen 6 to restart.
- **No active challenge condition:** Triggered after reset and before setup. Routes to Screen 1.

### Screen 4: Bedtime Closeout
- **Default decision state:** Triggered from Screen 3 or a notification deep link while `NightRecord.status = pending`. Shows prompt and three actions: `Lenses out`, `Still in`, `Skip tonight`.
- **Prototype mismatch decision:** Keep the prototype’s button hierarchy and secondary placement of `Skip tonight`, but reject any copy implying medical advice or guaranteed clinical outcomes.
- **Resolved state:** After `Lenses out`, immediately closes back to Screen 3 with a cleared status.
- **Snoozed handoff:** After `Still in`, routes to Screen 5 and persists the next reminder time.
- **Skipped handoff:** After `Skip tonight`, returns to Screen 3 with skipped status.

### Screen 5: Reminder Escalation / Still In
- **Active snooze:** Triggered when the user chose `Still in` and `NightRecord.status = pending` with `NightRecord.lastUserAction = still_in`. Shows unresolved state, next reminder time, `Clear now`, and `Snooze again`.
- **Max reminder window reached:** Triggered when current time is at or after `NightRecord.missedAt` and the night is still pending. The next foreground refresh converts the night to `missed`.
- **Prototype mismatch decision:** Reject the mock’s `Hypoxia Risk`, percentage claims, and medically loaded copy. Screen 5 may only state that the night is still unresolved and another reminder is scheduled.

### Screen 6: Challenge History / Scorecard
- **In-progress run:** Triggered when a run exists and `ChallengeRun.completedAt` is null. Shows all 14 day slots, completed-day count, and current completion rate.
- **Completed run:** Triggered when `ChallengeRun.completedAt` is set. Shows final completion rate and `Restart challenge`.
- **No results yet condition:** Triggered when only future `NightRecord` entries remain unresolved. Shows 14 slots with future days visually inactive but still present.

### Screen 7: Settings
- **Default settings:** Shows bedtime, reminder interval, notification status, pause/resume control, and reset data control.
- **Paused state:** Triggered when `ChallengeRun.isPaused = true`. Shows `Resume challenge` instead of `Pause challenge`.
- **Reset confirmation:** Triggered after tapping reset. Shows destructive confirmation before deleting `ChallengeRun`, `NightRecord`, and `AppState.hasCompletedSetup`.
- **Permission denied condition:** Shows CTA to iOS Settings when reminders are unavailable.

## 8. Data Model

### Entity: AppState
- **Fields:**
  - `id: UUID` — singleton app state identifier
  - `hasCompletedSetup: Bool` — whether Screen 1 and Screen 2 are complete
  - `notificationPermissionStatus: String` — enum: `not_determined` | `authorized` | `denied`
  - `lastOpenedAt: Date` — last foreground timestamp used to reconcile missed nights
- **Relationships:** has one active `ChallengeRun` or none

### Entity: ChallengeRun
- **Fields:**
  - `id: UUID` — challenge run identifier
  - `startedAt: Date` — timestamp when the 14-night run begins
  - `completedAt: Date?` — set when all 14 nightly records are final or the run is manually finished after day 14
  - `isPaused: Bool` — whether reminder scheduling is paused
  - `pausedAt: Date?` — when pause began
  - `targetBedtime: DateComponents` — local daily bedtime time
  - `reminderIntervalMinutes: Int` — enum value: `15` | `30` | `60`
  - `notificationDeepLinkRoute: String` — fixed route value used for bedtime notification opens, `screen4_closeout`
- **Relationships:** has many `NightRecord`

### Entity: NightRecord
- **Fields:**
  - `id: UUID` — nightly record identifier
  - `challengeRunId: UUID` — parent run reference
  - `dayNumber: Int` — 1 through 14
  - `nightDate: Date` — calendar date the closeout belongs to
  - `status: String` — enum: `pending` | `cleared` | `missed` | `skipped`
  - `lastUserAction: String?` — enum: `lenses_out` | `still_in` | `skip_tonight`
  - `createdAt: Date` — when the nightly record was generated
  - `resolvedAt: Date?` — when the user cleared or skipped the night
  - `missedAt: Date` — local timestamp after which the night becomes `missed` if still pending
  - `nextReminderAt: Date?` — next scheduled reminder time while unresolved
  - `snoozeCount: Int` — number of `Still in` actions for the night
  - `notificationRequestIds: [String]` — local notification identifiers tied to this night for cancellation/rescheduling
- **Relationships:** belongs to `ChallengeRun`

### Persistence mapping decisions
- The UI label `Pending` maps to `NightRecord.status = pending`.
- The UI label `Cleared` maps to `NightRecord.status = cleared`.
- The UI label `Missed` maps to `NightRecord.status = missed`.
- The UI label `Skipped` maps to `NightRecord.status = skipped`.
- The button `Lenses out` persists `NightRecord.lastUserAction = lenses_out` and `NightRecord.status = cleared`.
- The button `Still in` persists `NightRecord.lastUserAction = still_in`, increments `NightRecord.snoozeCount`, and updates `NightRecord.nextReminderAt`.
- The button `Skip tonight` persists `NightRecord.lastUserAction = skip_tonight` and `NightRecord.status = skipped`.

## 9. Screens & Acceptance Criteria

### Screen 1: Welcome / 14-Night Challenge Intro [Required v1]
**Purpose:** Introduce the product promise and start setup.
**Visual elements:** Light-theme editorial hero, LensLock product name, one-sentence challenge explanation, “who it is for” copy block, primary CTA.
**User actions:** Tap primary CTA.
**State changes:** Navigates to Screen 2. No persistent data changes.
**Acceptance criteria:**
- [ ] Screen 1 displays the product name `LensLock`; no screen title or HTML title uses `Ocularis`.
- [ ] Tapping the primary CTA on Screen 1 opens Screen 2 without creating `AppState.hasCompletedSetup` or `ChallengeRun` records yet.
- [ ] Screen 1 does not expose a required secondary navigation path; the prototype’s `View Schedule` CTA is not implemented in v1.
**Conditional states:** Loading shows only if initial local state has not loaded. Error shows retry if `AppState` cannot be read.

### Screen 2: Onboarding / Bedtime Setup [Required v1]
**Purpose:** Capture the minimum setup needed to run nightly reminders.
**Visual elements:** Bedtime picker, reminder interval segmented control with values `15m`, `30m`, `60m`, notification permission card, save/start CTA.
**User actions:** Edit bedtime, choose reminder interval, request notification permission, save setup.
**State changes:** Writes `ChallengeRun.targetBedtime`, `ChallengeRun.reminderIntervalMinutes`, `AppState.notificationPermissionStatus`, `AppState.hasCompletedSetup`, creates one `ChallengeRun`, and creates `NightRecord` rows for day numbers 1 through 14.
**Acceptance criteria:**
- [ ] Saving Screen 2 creates exactly one `ChallengeRun` and exactly 14 `NightRecord` rows linked by `NightRecord.challengeRunId`.
- [ ] The only allowed persisted reminder interval values are `15`, `30`, and `60`, and Screen 2 shows the same values as `15m`, `30m`, and `60m`.
- [ ] If iOS returns denied permission, Screen 2 stores `AppState.notificationPermissionStatus = denied` and still allows the user to complete setup.
- [ ] After successful setup, `AppState.hasCompletedSetup = true` and navigation lands on Screen 3.
**Conditional states:** Permission denied shows inline warning and Settings deep link CTA. Persistence error shows retry and does not create a partial run.

### Screen 3: Home / Tonight Status [Required v1]
**Purpose:** Show the current challenge day and the status of tonight’s unresolved closeout.
**Visual elements:** Circular day progress, status card, next reminder/countdown card, compact 14-night score row, primary CTA to open Screen 4, bottom navigation with `Home`, `Challenge`, `Settings`.
**User actions:** Open closeout, navigate to Screen 6, navigate to Screen 7, resume challenge if paused.
**State changes:** Opening closeout navigates to Screen 4. Resuming pause sets `ChallengeRun.isPaused = false`, clears `ChallengeRun.pausedAt`, and reschedules `NightRecord.nextReminderAt` if the current night is still pending.
**Acceptance criteria:**
- [ ] Screen 3 shows the current day from `NightRecord.dayNumber` for the active pending or most recent finalized night.
- [ ] If `NightRecord.status = pending`, Screen 3 shows the label `Pending` and a CTA that opens Screen 4.
- [ ] If the active night was still pending and current time is at or after `NightRecord.missedAt`, opening Screen 3 reconciles that record to `NightRecord.status = missed` before rendering the status badge.
- [ ] Tapping back from Screen 4 or Screen 5 returns to Screen 3 and preserves the previously viewed day and bottom-nav selection.
- [ ] If `ChallengeRun.isPaused = true`, Screen 3 hides next reminder time and shows `Resume challenge` instead of the normal reminder timing card.
**Conditional states:** No active challenge routes to Screen 1. Loading blocks while reconciling local state. Error shows retry if the active run cannot be loaded.

### Screen 4: Bedtime Closeout [Required v1]
**Purpose:** Collect the nightly bedtime decision.
**Visual elements:** Closeout prompt, supporting non-medical copy, primary action buttons `Lenses out` and `Still in`, secondary text action `Skip tonight`.
**User actions:** Tap `Lenses out`, tap `Still in`, tap `Skip tonight`, navigate back.
**State changes:** `Lenses out` sets `NightRecord.lastUserAction = lenses_out`, `NightRecord.status = cleared`, `NightRecord.resolvedAt = now`, clears `NightRecord.nextReminderAt`, and cancels `NightRecord.notificationRequestIds`. `Still in` sets `NightRecord.lastUserAction = still_in`, increments `NightRecord.snoozeCount`, computes and saves `NightRecord.nextReminderAt`, reschedules notifications, and routes to Screen 5. `Skip tonight` sets `NightRecord.lastUserAction = skip_tonight`, `NightRecord.status = skipped`, `NightRecord.resolvedAt = now`, clears reminders, and returns to Screen 3.
**Acceptance criteria:**
- [ ] Screen 4 always shows exactly three user choices: `Lenses out`, `Still in`, and `Skip tonight`.
- [ ] Tapping `Lenses out` updates `NightRecord.status` to `cleared` and Screen 3 reflects `Cleared` immediately after navigation.
- [ ] Tapping `Still in` does not finalize the night; `NightRecord.status` remains `pending`, `NightRecord.lastUserAction = still_in`, and Screen 5 opens.
- [ ] Tapping `Skip tonight` updates `NightRecord.status` to `skipped` and cancels all pending notification IDs in `NightRecord.notificationRequestIds`.
- [ ] Screen 4 copy contains no medical percentages, no `hypoxia` claim, and no clinical risk score language.
**Conditional states:** If the night is already `cleared`, `missed`, or `skipped`, Screen 4 redirects back to Screen 3. Error shows retry if the night record fails to save.

### Screen 5: Reminder Escalation / Still In [Required v1]
**Purpose:** Keep the nightly closeout unresolved and visible after the user says lenses are still in.
**Visual elements:** Unresolved status header, next reminder time, primary `Clear now` CTA, secondary `Snooze again` CTA, brief explanation of what will happen next.
**User actions:** Tap `Clear now`, tap `Snooze again`, navigate back.
**State changes:** `Clear now` applies the same update path as Screen 4 `Lenses out`. `Snooze again` increments `NightRecord.snoozeCount`, sets `NightRecord.lastUserAction = still_in`, updates `NightRecord.nextReminderAt = now + ChallengeRun.reminderIntervalMinutes`, replaces `NightRecord.notificationRequestIds`, and stays on Screen 5.
**Acceptance criteria:**
- [ ] Screen 5 displays the next reminder time from `NightRecord.nextReminderAt`.
- [ ] Tapping `Snooze again` increases `NightRecord.snoozeCount` by 1 and schedules the next reminder exactly `ChallengeRun.reminderIntervalMinutes` after the tap time.
- [ ] Tapping `Clear now` sets `NightRecord.status = cleared`, cancels pending notification requests, and returns to Screen 3.
- [ ] Screen 5 contains no `Hypoxia Risk`, no oxygen-percentage claim, and no medical-liability language from the prototype.
- [ ] If current time is at or after `NightRecord.missedAt`, the next foreground render converts the night to `missed` and Screen 5 routes to Screen 3.
**Conditional states:** If notification permission is denied, Screen 5 still records snooze intent in `NightRecord.nextReminderAt` but shows that no OS reminder will fire. Error shows retry if reminder rescheduling fails.

### Screen 6: Challenge History / Scorecard [Required v1]
**Purpose:** Show the 14-night run as a finite scorecard with restart after completion.
**Visual elements:** Summary card, 14 day cells, completion-rate text, status legend for `Cleared`, `Missed`, `Skipped`, restart CTA after completion.
**User actions:** View past nights, restart challenge when current run is complete.
**State changes:** Restart creates a new `ChallengeRun`, archives the old run by keeping its `completedAt`, creates 14 new `NightRecord` rows, and navigates to Screen 3.
**Acceptance criteria:**
- [ ] Screen 6 renders exactly 14 day slots derived from `NightRecord.dayNumber`.
- [ ] Completion rate equals `(count of NightRecord.status = cleared) / 14`, rounded to the nearest whole percent for display.
- [ ] Future days with no final outcome remain visible but inactive; they are not separate screens or separate entities.
- [ ] `Restart challenge` is hidden until `ChallengeRun.completedAt` is non-null or all 14 `NightRecord.status` values are final.
- [ ] Restarting the challenge creates a new `ChallengeRun.id` and a fresh set of 14 linked `NightRecord` rows without mutating the previous run’s records.
**Conditional states:** In-progress state shows partial completion. Completed state shows restart CTA. Error shows retry if history cannot load.

### Screen 7: Settings [Required v1]
**Purpose:** Let the user manage timing, pause state, permission access, and data reset.
**Visual elements:** Bedtime summary card, reminder interval control, notification-permission status, `Pause challenge` or `Resume challenge`, `Reset data` destructive section, non-medical trust copy.
**User actions:** Change bedtime, change reminder interval, open Screen 2-style editor if needed, pause/resume challenge, open iOS Settings, confirm reset.
**State changes:** Editing bedtime updates `ChallengeRun.targetBedtime` and recalculates `NightRecord.missedAt` and `NightRecord.nextReminderAt` for the current pending night only. Editing reminder interval updates `ChallengeRun.reminderIntervalMinutes` and the current pending night’s next reminder schedule. Pause sets `ChallengeRun.isPaused = true`, `ChallengeRun.pausedAt = now`, and cancels all pending notification request IDs for pending nights. Reset deletes `ChallengeRun`, `NightRecord`, and resets `AppState.hasCompletedSetup = false`.
**Acceptance criteria:**
- [ ] Screen 7 shows the same interval choices as Screen 2: `15m`, `30m`, `60m`, backed by `ChallengeRun.reminderIntervalMinutes = 15 | 30 | 60`.
- [ ] Tapping `Pause challenge` sets `ChallengeRun.isPaused = true` and cancels all IDs in pending `NightRecord.notificationRequestIds`.
- [ ] Tapping `Resume challenge` sets `ChallengeRun.isPaused = false` and reschedules only the active pending night based on `ChallengeRun.targetBedtime`, current time, and `ChallengeRun.reminderIntervalMinutes`.
- [ ] Tapping `Reset data` requires confirmation and, after confirmation, deletes all `ChallengeRun` and `NightRecord` records and routes the user to Screen 1.
- [ ] Screen 7 trust copy does not present medical percentages, diagnosis language, or risk scores.
**Conditional states:** Permission denied shows `Open iOS Settings`. Loading blocks while settings save. Error shows retry if updates fail.

## 10. External Integrations
- **Service:** iOS UserNotifications framework
  - **Purpose:** Request notification permission, schedule bedtime reminders, schedule snooze reminders, and deep link notification taps into Screen 4
  - **Specifics:** Use `UNUserNotificationCenter.requestAuthorization`, `UNCalendarNotificationTrigger` for nightly bedtime reminders, `UNTimeIntervalNotificationTrigger` for snooze reminders, category actions only if Optional polish is implemented, and store each request identifier in `NightRecord.notificationRequestIds`; known gotcha is that notification delivery timing and lock-screen behavior are OS-controlled, so the app must reconcile missed state on foreground instead of assuming background execution

## 11. Open Risks & Assumptions to Verify Before Build
- **Risk/Assumption:** The dedicated unresolved nightly flow beats a generic reminder enough to justify the app — **What to verify:** Run 5 to 10 user interviews or pilot installs and compare whether testers complete at least 10 of 14 nights more often than with a plain recurring reminder.
- **Risk/Assumption:** iOS notification behavior is reliable enough for the product’s core loop — **What to verify:** Build a spike that covers permission denied, bedtime reminder delivery, snooze reminder delivery, notification tap deep linking, and reminder cancellation on clear/skip.
- **Risk/Assumption:** `Still in` creates follow-through rather than endless snoozing — **What to verify:** In pilot testing, measure `NightRecord.snoozeCount` and the percentage of nights that move from `lastUserAction = still_in` to `status = cleared`.
- **Risk/Assumption:** `Skip tonight` is honest without collapsing compliance — **What to verify:** In pilot testing, compare share of `status = skipped` nights against `status = cleared` nights and decide whether the control should remain visible or be demoted in a later iteration.
- **Risk/Assumption:** Health-adjacent copy can stay credible without sounding clinical or fake — **What to verify:** Review all user-facing strings before coding and remove medical percentages, diagnosis language, or unsupported claims.

## 12. Visual Reference
- Live prototype (static mock — visual layout reference only): https://jakebuild.github.io/idea-factory/56-lenslock-is-a-health-app-that-/
- Screen files on GitHub: https://github.com/jakebuild/idea-factory/tree/gh-pages/56-lenslock-is-a-health-app-that-
- Source idea: https://github.com/jakebuild/idea-factory/blob/main/56-lenslock-is-a-health-app-that-/idea-v3.md
- Source critique: https://github.com/jakebuild/idea-factory/blob/main/56-lenslock-is-a-health-app-that-/critique-v3.md

Visual-source-of-truth decisions for implementation:
- Adopt the prototype’s light visual style, editorial imagery, circular day progress, and bottom navigation structure.
- Adopt the seven-screen structure from the source idea and published prototype.
- Reject the prototype’s stray `Ocularis` naming; the product name is `LensLock` everywhere.
- Reject the prototype’s medical-risk copy such as `Hypoxia Risk`, oxygen percentages, or clinical-seeming claims.
- Reject the prototype’s secondary `View Schedule` CTA on Screen 1.
- Adopt `Skip tonight` as a required v1 action because it appears in the source idea and data model, even though it remains a product risk to validate.
- Adopt a single target bedtime time in implementation and reject a two-ended bedtime window for v1; this matches the published mock and keeps scheduling testable.

## 13. Suggested Folder Structure
```text
LensLock/
├── LensLockApp.swift
├── App/
│   ├── AppCoordinator.swift
│   ├── AppStateStore.swift
│   └── NotificationRouter.swift
├── Models/
│   ├── AppState.swift
│   ├── ChallengeRun.swift
│   └── NightRecord.swift
├── Features/
│   ├── Welcome/
│   │   ├── WelcomeView.swift
│   │   └── WelcomeViewModel.swift
│   ├── Onboarding/
│   │   ├── BedtimeSetupView.swift
│   │   └── BedtimeSetupViewModel.swift
│   ├── Home/
│   │   ├── HomeView.swift
│   │   └── HomeViewModel.swift
│   ├── Closeout/
│   │   ├── BedtimeCloseoutView.swift
│   │   ├── StillInView.swift
│   │   └── CloseoutViewModel.swift
│   ├── History/
│   │   ├── HistoryView.swift
│   │   └── HistoryViewModel.swift
│   └── Settings/
│       ├── SettingsView.swift
│       └── SettingsViewModel.swift
├── Services/
│   ├── ChallengeScheduler.swift
│   ├── NotificationService.swift
│   ├── NightReconciliationService.swift
│   └── PersistenceService.swift
├── Components/
│   ├── ProgressRing.swift
│   ├── ScoreRow.swift
│   ├── StatusBadge.swift
│   └── PrimaryButton.swift
├── Resources/
│   ├── Assets.xcassets
│   └── Copy.strings
└── Tests/
    ├── ChallengeSchedulerTests.swift
    ├── NightReconciliationServiceTests.swift
    └── NotificationServiceTests.swift
```
