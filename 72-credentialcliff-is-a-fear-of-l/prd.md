# PRD: CredentialCliff

## 1. Overview
CredentialCliff is a web app for a single compliance coordinator at a small Texas nursing staffing agency who needs to stop RN and LVN licenses from silently expiring. In v1, the app imports a roster from CSV, normalizes and validates license records, then turns the weekly review into an exceptions queue with explicit buckets for `45 Days`, `21 Days`, `7 Days`, `Expired`, and `Stalled`, plus notes, follow-up dates, reminder emails, and activity history.

## 2. Target User
The target user is one compliance coordinator managing 10 to 100 clinicians at a Texas nursing staffing agency. Their pain is not storing every credential artifact; it is rebuilding spreadsheet filters every week, chasing down expiring licenses, and losing trust after bad data or missed follow-up. Existing spreadsheets fail because they do not make exception states, next actions, and reminder history obvious enough to support a repeatable Monday review.

## 3. Tech Stack
- Platform: Web
- Language + framework: TypeScript + Next.js 15 (App Router) + React 19
- Key libraries: `@supabase/supabase-js` for auth and Postgres access, `drizzle-orm` for schema and queries, `papaparse` for CSV parsing, `react-hook-form` + `zod` for forms and validation, `@tanstack/react-table` for the exceptions table, `tailwindcss` + `shadcn/ui` for UI primitives, `resend` for outbound email
- Hosting / deployment: Vercel for the app and cron routes, Supabase for auth and database
- Why this stack fits the solo dev + 2–4 week constraint: one TypeScript codebase covers UI, server actions, scheduled reminder jobs, and auth; Supabase removes custom backend plumbing; Vercel Cron plus Resend covers the digest/reminder wedge without building queue infrastructure.

## 4. V1 Scope
- **Required v1** Email/password sign-in for one coordinator account per workspace
- **Required v1** Single-workspace setup with agency name, coordinator name, and timezone
- **Required v1** CSV import with column mapping, validation summary, duplicate detection, and fix-before-import review
- **Required v1** Manual add and manual edit for Texas `RN` and `LVN` license records only
- **Required v1** Exceptions dashboard with exact buckets `45 Days`, `21 Days`, `7 Days`, `Expired`, and `Stalled`
- **Required v1** Quick update flow to change status, set `lastContactDate`, set `nextFollowUpDate`, and add a note
- **Required v1** Clinician detail view with full license metadata, notes timeline, Texas BON renewal link, and activity history
- **Required v1** Weekly digest email to one recipient email
- **Required v1** Optional reminder emails for the `21 Days` and `7 Days` buckets
- **Required v1** Activity history for imports, field changes, status changes, note additions, and email sends
- **Optional polish** Weekly digest preview screen
- **Optional polish** Test email action in reminder settings

## 5. Out of Scope
- Dedicated password reset screen
- Proof upload or document storage
- BLS or any non-`RN`/`LVN` credential
- OCR or document parsing
- Maintained renewal checklists
- Multi-user collaboration
- Recruiter, manager, or owner portals
- Staffing-risk integrations
- Mobile app
- Payments

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Sign In | Required v1 |
| 2 | Create Workspace / Initial Setup | Required v1 |
| 3 | CSV Import Wizard | Required v1 |
| 4 | Import Error Review | Required v1 |
| 5 | Exceptions Dashboard | Required v1 |
| 6 | Clinician Record | Required v1 |
| 7 | Quick Update Drawer / Modal | Required v1 |
| 8 | Reminder Settings | Required v1 |
| 9 | Activity History | Required v1 |
| 10 | Weekly Digest Preview | Optional polish |

## 7. State Model

### Global
- **State name:** Unauthenticated session
  - Trigger: no active Supabase session
  - Shows: Screen 1 only
  - Actions: sign in, request password-reset email from Screen 1
- **State name:** Authenticated but workspace not created
  - Trigger: authenticated `User.id` exists and no `Workspace.id` exists for `User.id`
  - Shows: Screen 2
  - Actions: create workspace, sign out
- **State name:** Authenticated and workspace ready
  - Trigger: authenticated `User.id` exists and `Workspace.onboardingStatus = "Ready"`
  - Shows: Screens 3 to 10
  - Actions: navigate across app
- **State name:** App bootstrap loading
  - Trigger: session check or initial workspace fetch in progress
  - Shows: blocking loading shell
  - Actions: none
- **State name:** App bootstrap error
  - Trigger: session check or initial workspace fetch fails
  - Shows: blocking error with retry
  - Actions: retry bootstrap

### Screen 1. Sign In
- **State name:** Ready to sign in
  - Trigger: no active session and no request in progress
  - Shows: email field, password field, sign-in button, forgot-password link
  - Actions: submit credentials, request password-reset email
- **State name:** Signing in
  - Trigger: sign-in request in progress
  - Shows: disabled form and loading indicator
  - Actions: none
- **State name:** Sign-in error
  - Trigger: invalid credentials or auth request failure
  - Shows: inline error above the form
  - Actions: retry sign in
- **State name:** Password reset requested
  - Trigger: forgot-password action succeeds
  - Shows: confirmation banner that reset email was sent by auth provider
  - Actions: return to sign-in form

### Screen 2. Create Workspace / Initial Setup
- **State name:** Empty setup form
  - Trigger: first authenticated visit with no `Workspace`
  - Shows: agency name, coordinator name, timezone selector, sample CSV download link, import CTA
  - Actions: enter values, save workspace
- **State name:** Validation error
  - Trigger: submit with missing required fields
  - Shows: inline field errors
  - Actions: correct fields and resubmit
- **State name:** Saving workspace
  - Trigger: workspace create request in progress
  - Shows: disabled form and loading state
  - Actions: none
- **State name:** Setup error
  - Trigger: workspace create request fails
  - Shows: blocking inline error
  - Actions: retry save

### Screen 3. CSV Import Wizard
- **State name:** No file selected
  - Trigger: no active `ImportJob`
  - Shows: upload control, supported fields list, sample CSV link
  - Actions: choose CSV file
- **State name:** Parsing and mapping
  - Trigger: CSV uploaded and parser succeeded
  - Shows: column-mapping UI, preview table, validation summary seed counts
  - Actions: map columns, remap columns, continue to validation
- **State name:** Validation ready
  - Trigger: required columns mapped and validation run completed
  - Shows: counts for valid rows, duplicate rows, and error rows
  - Actions: continue to Screen 4 if any duplicate or error rows exist, confirm import if all rows valid
- **State name:** Parse or upload error
  - Trigger: file parse fails or file is not CSV
  - Shows: inline error and file picker
  - Actions: choose different CSV

### Screen 4. Import Error Review
- **State name:** Needs row review
  - Trigger: active `ImportJob.status = "Needs Review"`
  - Shows: error rows, duplicate rows, row-level fixes, skip/import toggles, remaining unresolved count
  - Actions: edit normalized values, mark row `Import`, mark row `Skip`, return to Screen 3
- **State name:** All rows resolved
  - Trigger: every `ImportRow.resolution` is `Import` or `Skip`
  - Shows: import-ready summary and confirm import CTA
  - Actions: confirm import
- **State name:** Importing resolved rows
  - Trigger: final import persistence in progress
  - Shows: blocking loading state
  - Actions: none
- **State name:** Import error
  - Trigger: row persistence or dedupe merge fails
  - Shows: blocking error with no partial-success claim
  - Actions: retry final import

### Screen 5. Exceptions Dashboard
- **State name:** Loading queue
  - Trigger: dashboard query in progress
  - Shows: summary cards and table skeletons
  - Actions: none
- **State name:** Populated queue
  - Trigger: at least one `LicenseRecord` exists
  - Shows: bucket summary cards, search, bucket filter, license-type filter, exceptions table
  - Actions: switch bucket, search by clinician name, open Screen 7, open Screen 6, start Screen 3, manually add record
- **State name:** No license records yet
  - Trigger: zero `LicenseRecord` rows in workspace
  - Shows: empty-state message inside dashboard container and CTA to import roster
  - Actions: go to Screen 3, manually add first record
- **State name:** No results for current filters
  - Trigger: `LicenseRecord` rows exist but current search/filter returns zero rows
  - Shows: empty results message and clear-filters action
  - Actions: clear filters
- **State name:** Dashboard error
  - Trigger: queue query fails
  - Shows: blocking error with retry
  - Actions: retry load

### Screen 6. Clinician Record
- **State name:** Create mode
  - Trigger: user chooses manual add from Screen 5
  - Shows: empty clinician and license form using the same layout as the detail screen
  - Actions: create clinician, create license record, cancel back to Screen 5
- **State name:** Loading record
  - Trigger: record route opened and data fetch in progress
  - Shows: detail skeleton
  - Actions: none
- **State name:** Record loaded
  - Trigger: matching `Clinician.id` and `LicenseRecord.id` exist
  - Shows: clinician header, license card, status chip, date fields, notes timeline, activity list, BON link
  - Actions: edit fields, add note, open Screen 7, open BON renewal page, return to Screen 5
- **State name:** No notes yet
  - Trigger: `LicenseRecord` exists and zero `Note` rows exist
  - Shows: notes empty state inside detail page
  - Actions: add first note, open Screen 7
- **State name:** Record not found
  - Trigger: invalid or deleted record id
  - Shows: not-found state inside app shell
  - Actions: return to Screen 5
- **State name:** Save error
  - Trigger: update or note create fails
  - Shows: inline error and unchanged last saved values
  - Actions: retry save

### Screen 7. Quick Update Drawer / Modal
- **State name:** Closed
  - Trigger: drawer not open
  - Shows: none
  - Actions: open from Screen 5 or Screen 6
- **State name:** Ready to edit
  - Trigger: drawer opens with a valid `LicenseRecord`
  - Shows: status selector, `lastContactDate`, `nextFollowUpDate`, note field, recent activity, save button
  - Actions: edit fields, save, cancel
- **State name:** Validation error
  - Trigger: save with empty note and no other field changes, or invalid date order
  - Shows: inline field errors
  - Actions: correct values and resubmit
- **State name:** Saving
  - Trigger: mutation in progress
  - Shows: disabled inputs and loading state
  - Actions: none
- **State name:** Save error
  - Trigger: mutation fails
  - Shows: inline error in drawer
  - Actions: retry save

### Screen 8. Reminder Settings
- **State name:** Loading settings
  - Trigger: settings query in progress
  - Shows: form skeleton
  - Actions: none
- **State name:** Settings loaded
  - Trigger: `NotificationSettings` exists
  - Shows: digest day selector, recipient email, `21 Days` toggle, `7 Days` toggle, last send status, optional test-email action
  - Actions: edit settings, save settings, send test email if enabled
- **State name:** Validation error
  - Trigger: submit with invalid recipient email or no digest day
  - Shows: inline field errors
  - Actions: correct fields and resubmit
- **State name:** Saving settings
  - Trigger: settings mutation in progress
  - Shows: disabled form and loading state
  - Actions: none
- **State name:** Settings error
  - Trigger: load or save request fails
  - Shows: inline error with unchanged saved values
  - Actions: retry

### Screen 9. Activity History
- **State name:** Loading history
  - Trigger: history query in progress
  - Shows: feed skeleton
  - Actions: none
- **State name:** History loaded
  - Trigger: at least one `ActivityEvent` exists
  - Shows: chronological feed, clinician filter, event-type filter
  - Actions: filter by clinician, filter by event type, open related clinician record
- **State name:** No activity yet
  - Trigger: zero `ActivityEvent` rows exist
  - Shows: empty feed state inside page
  - Actions: return to Screen 5
- **State name:** Filtered no results
  - Trigger: activity exists but current filters return zero rows
  - Shows: no-results message and clear-filters action
  - Actions: clear filters
- **State name:** History error
  - Trigger: query fails
  - Shows: blocking error with retry
  - Actions: retry load

### Screen 10. Weekly Digest Preview
- **State name:** Preview loaded
  - Trigger: digest preview query succeeds
  - Shows: grouped lists for `45 Days`, `21 Days`, `7 Days`, `Expired`, and `Stalled`, plus counts
  - Actions: open related clinician record, return to Screen 8
- **State name:** No current exceptions
  - Trigger: no records match any digest bucket
  - Shows: zero-state preview with all counts at `0`
  - Actions: return to Screen 8
- **State name:** Preview error
  - Trigger: preview query fails
  - Shows: inline error and retry
  - Actions: retry load

## 8. Data Model

### Entity: User
- **Fields:**
  - `id: uuid` — auth user id
  - `email: string` — sign-in email
  - `createdAt: timestamp` — user creation timestamp
- **Relationships:** owns one `Workspace`

### Entity: Workspace
- **Fields:**
  - `id: uuid` — workspace id
  - `ownerUserId: uuid` — foreign key to `User.id`
  - `agencyName: string` — agency name from Screen 2
  - `coordinatorName: string` — displayed coordinator name from Screen 2
  - `timezone: string` — IANA timezone string used for digest scheduling
  - `onboardingStatus: "Needs Setup" | "Ready"` — gates Screen 2 vs app screens
  - `createdAt: timestamp` — creation timestamp
  - `updatedAt: timestamp` — last update timestamp
- **Relationships:** belongs to `User`; has many `Clinician`, `ImportJob`, `ActivityEvent`, `EmailDelivery`, and one `NotificationSettings`

### Entity: NotificationSettings
- **Fields:**
  - `id: uuid` — settings id
  - `workspaceId: uuid` — foreign key to `Workspace.id`
  - `digestDay: "Monday" | "Tuesday" | "Wednesday" | "Thursday" | "Friday" | "Saturday" | "Sunday"` — exact weekly digest selector value
  - `recipientEmail: string` — single destination email for digest and reminders
  - `send21DayReminder: boolean` — toggle for `21 Days` reminder emails
  - `send7DayReminder: boolean` — toggle for `7 Days` reminder emails
  - `createdAt: timestamp` — creation timestamp
  - `updatedAt: timestamp` — last update timestamp
- **Relationships:** belongs to `Workspace`

### Entity: Clinician
- **Fields:**
  - `id: uuid` — clinician id
  - `workspaceId: uuid` — foreign key to `Workspace.id`
  - `fullName: string` — clinician name shown in queue and detail
  - `sourceExternalId: string | null` — optional external roster id from CSV
  - `createdAt: timestamp` — creation timestamp
  - `updatedAt: timestamp` — last update timestamp
- **Relationships:** belongs to `Workspace`; has many `LicenseRecord`

### Entity: LicenseRecord
- **Fields:**
  - `id: uuid` — license record id
  - `workspaceId: uuid` — foreign key to `Workspace.id`
  - `clinicianId: uuid` — foreign key to `Clinician.id`
  - `licenseType: "RN" | "LVN"` — exact user-facing license type
  - `licenseNumber: string | null` — optional Texas license number
  - `expirationDate: date` — license expiration date
  - `trackingStatus: "Needs Review" | "Outreach Sent" | "Awaiting Renewal" | "Cleared" | "Expired"` — exact status selector values
  - `lastContactDate: date | null` — date of last outreach
  - `nextFollowUpDate: date | null` — date when the coordinator plans the next follow-up
  - `source: "CSV Import" | "Manual"` — how the record entered the app
  - `clearedAt: timestamp | null` — set when `trackingStatus = "Cleared"`
  - `createdAt: timestamp` — creation timestamp
  - `updatedAt: timestamp` — last update timestamp
- **Relationships:** belongs to `Workspace` and `Clinician`; has many `Note`, `ActivityEvent`, and `EmailDelivery`; optionally belongs to one `ImportRow`

### Entity: Note
- **Fields:**
  - `id: uuid` — note id
  - `licenseRecordId: uuid` — foreign key to `LicenseRecord.id`
  - `workspaceId: uuid` — foreign key to `Workspace.id`
  - `body: string` — coordinator note content shown in timeline and used for latest note snippet
  - `createdByUserId: uuid` — author user id
  - `createdAt: timestamp` — note creation timestamp
- **Relationships:** belongs to `LicenseRecord`, `Workspace`, and `User`

### Entity: ImportJob
- **Fields:**
  - `id: uuid` — import job id
  - `workspaceId: uuid` — foreign key to `Workspace.id`
  - `fileName: string` — uploaded CSV file name
  - `status: "Uploaded" | "Needs Review" | "Imported" | "Failed"` — drives Screen 3 and Screen 4 states
  - `columnMappingJson: json` — persisted source-column to target-field mapping
  - `totalRowCount: integer` — parsed row count
  - `validRowCount: integer` — rows ready to import
  - `errorRowCount: integer` — rows with blocking validation issues
  - `duplicateRowCount: integer` — rows flagged as duplicates
  - `createdAt: timestamp` — import creation timestamp
  - `completedAt: timestamp | null` — import completion timestamp
- **Relationships:** belongs to `Workspace`; has many `ImportRow`

### Entity: ImportRow
- **Fields:**
  - `id: uuid` — import row id
  - `importJobId: uuid` — foreign key to `ImportJob.id`
  - `workspaceId: uuid` — foreign key to `Workspace.id`
  - `rowNumber: integer` — original CSV row number
  - `clinicianNameRaw: string | null` — raw CSV value
  - `licenseTypeRaw: string | null` — raw CSV value
  - `licenseNumberRaw: string | null` — raw CSV value
  - `expirationDateRaw: string | null` — raw CSV value
  - `normalizedFullName: string | null` — corrected value used for import
  - `normalizedLicenseType: "RN" | "LVN" | null` — corrected exact value used for import
  - `normalizedLicenseNumber: string | null` — corrected value used for import
  - `normalizedExpirationDate: date | null` — corrected value used for import
  - `parseStatus: "Valid" | "Error" | "Duplicate"` — row validation result
  - `errorSummary: string | null` — human-readable validation issue summary
  - `resolution: "Pending" | "Import" | "Skip"` — row decision in Screen 4
  - `matchedLicenseRecordId: uuid | null` — existing record matched during duplicate detection
- **Relationships:** belongs to `ImportJob` and `Workspace`; may map to one `LicenseRecord`

### Entity: ActivityEvent
- **Fields:**
  - `id: uuid` — activity event id
  - `workspaceId: uuid` — foreign key to `Workspace.id`
  - `licenseRecordId: uuid | null` — affected record when applicable
  - `importJobId: uuid | null` — related import when applicable
  - `eventType: "Import" | "Field Update" | "Status Change" | "Note Added" | "Reminder Email" | "Digest Email" | "Test Email"` — exact filter values on Screen 9
  - `fieldName: string | null` — changed field for field updates
  - `oldValue: string | null` — previous value for audit display
  - `newValue: string | null` — new value for audit display
  - `noteId: uuid | null` — related note when `eventType = "Note Added"`
  - `createdByUserId: uuid | null` — actor user id, nullable for scheduled jobs
  - `createdAt: timestamp` — event timestamp
- **Relationships:** belongs to `Workspace`; optionally belongs to `LicenseRecord`, `ImportJob`, `Note`, and `User`

### Entity: EmailDelivery
- **Fields:**
  - `id: uuid` — delivery id
  - `workspaceId: uuid` — foreign key to `Workspace.id`
  - `licenseRecordId: uuid | null` — nullable for weekly digest emails
  - `emailType: "Weekly Digest" | "21-Day Reminder" | "7-Day Reminder" | "Test Email"` — exact email categories
  - `recipientEmail: string` — destination address used for the send
  - `deliveryStatus: "Queued" | "Sent" | "Failed"` — drives last-send status UI
  - `providerMessageId: string | null` — message id returned by Resend
  - `errorMessage: string | null` — failure text when `deliveryStatus = "Failed"`
  - `sentAt: timestamp | null` — successful send timestamp
  - `createdAt: timestamp` — delivery attempt creation timestamp
- **Relationships:** belongs to `Workspace`; optionally belongs to `LicenseRecord`

## 9. Screens & Acceptance Criteria

### Screen 1: Sign In [Required v1]
**Purpose:** Let the coordinator access the single-user workspace.
**Visual elements:** Centered sign-in card, app name, email field, password field, primary sign-in button, forgot-password link, inline error/success banners.
**User actions:** Enter email, enter password, submit sign in, request password-reset email.
**State changes:** Successful sign in creates an authenticated session for `User.id`; forgot-password sends a provider email but does not create any new app data.
**Acceptance criteria:**
- [ ] Screen 1 rejects empty email or password before network submission.
- [ ] Submitting valid credentials for an existing `User.email` creates an authenticated session and routes to Screen 2 when no `Workspace.id` exists or Screen 5 when `Workspace.onboardingStatus = "Ready"`.
- [ ] Invalid credentials keep the user on Screen 1 and render an inline error without clearing the entered `email`.
- [ ] Clicking the forgot-password link triggers the auth provider reset-email flow and shows a confirmation state on Screen 1; no separate password-reset screen exists in v1.
**Conditional states:** Loading disables the form during auth requests. Error shows an inline message for auth failure. Success after forgot-password shows confirmation in place on the same screen.

### Screen 2: Create Workspace / Initial Setup [Required v1]
**Purpose:** Capture the one workspace required before any roster data can be imported.
**Visual elements:** Setup form, agency name input, coordinator name input, timezone selector, sample CSV download link, primary save-and-import button.
**User actions:** Enter workspace details, download sample CSV, save workspace.
**State changes:** Saving creates `Workspace` with `agencyName`, `coordinatorName`, `timezone`, and `onboardingStatus = "Ready"` plus a default `NotificationSettings` row.
**Acceptance criteria:**
- [ ] Screen 2 requires non-empty `Workspace.agencyName`, `Workspace.coordinatorName`, and `Workspace.timezone` before save.
- [ ] Saving Screen 2 creates exactly one `Workspace` for `User.id` and one `NotificationSettings` row linked by `workspaceId`.
- [ ] After successful save, the user is routed to Screen 3 without needing to re-authenticate.
- [ ] The sample CSV link downloads a static template and does not create or mutate any database record.
**Conditional states:** Loading disables the form during save. Validation error shows field-level messages. Save error shows a retryable inline error and leaves form values intact.

### Screen 3: CSV Import Wizard [Required v1]
**Purpose:** Upload a roster CSV, map columns, and validate rows before import.
**Visual elements:** File upload zone, supported-field list, column-mapping grid, validation summary counts, row preview table, continue/import CTA.
**User actions:** Upload CSV, map source columns, review preview, continue to error review, confirm import when all rows are valid.
**State changes:** Upload creates `ImportJob`; mapping updates `ImportJob.columnMappingJson`; validation persists `ImportRow` records with raw and normalized values plus `parseStatus`.
**Acceptance criteria:**
- [ ] Uploading a CSV on Screen 3 creates an `ImportJob` with `status = "Uploaded"` and `fileName` equal to the selected filename.
- [ ] Screen 3 cannot continue to validation until columns for clinician name, license type, and expiration date are mapped into `ImportJob.columnMappingJson`.
- [ ] Running validation creates one `ImportRow` per parsed CSV row with `rowNumber`, raw values, normalized values when possible, and a `parseStatus` of `Valid`, `Error`, or `Duplicate`.
- [ ] If `ImportJob.errorRowCount > 0` or `ImportJob.duplicateRowCount > 0`, Screen 3 routes to Screen 4 instead of importing directly.
- [ ] If every row is valid, confirming import sets `ImportJob.status = "Imported"` and routes to Screen 5.
**Conditional states:** Empty state shows upload guidance before any file is selected. Parse error shows inline file-format feedback. Loading shows parsing or validation progress and disables continue actions.

### Screen 4: Import Error Review [Required v1]
**Purpose:** Force cleanup of invalid or duplicate import rows before records enter the live queue.
**Visual elements:** Error summary, duplicate summary, editable row table, skip/import controls, unresolved count, confirm import button.
**User actions:** Edit normalized values, mark rows to import, skip rows, confirm import.
**State changes:** Row edits update `ImportRow.normalized*` fields; row decisions update `ImportRow.resolution`; final import creates or updates `Clinician` and `LicenseRecord` rows and marks `ImportJob.status = "Imported"`.
**Acceptance criteria:**
- [ ] Screen 4 shows every `ImportRow` where `parseStatus != "Valid"` or `resolution = "Pending"` before final import is allowed.
- [ ] A row cannot be marked `Import` unless `normalizedFullName`, `normalizedLicenseType`, and `normalizedExpirationDate` are all present.
- [ ] Confirm import remains disabled until every unresolved row has `resolution = "Import"` or `resolution = "Skip"`.
- [ ] Final import creates `Clinician` and `LicenseRecord` rows only for `ImportRow.resolution = "Import"` rows and leaves `Skip` rows out of the live roster.
- [ ] After final import, Screen 4 writes at least one `ActivityEvent` with `eventType = "Import"` and routes to Screen 5.
**Conditional states:** Loading blocks the UI during final persistence. Error shows a retryable blocking message with no success claim. If all rows are resolved, Screen 4 switches to an import-ready summary state.

### Screen 5: Exceptions Dashboard [Required v1]
**Purpose:** Show the weekly action queue and let the coordinator process exceptions faster than a spreadsheet.
**Visual elements:** Dark desktop admin shell matching the reference, left navigation, top summary cards for `45 Days`, `21 Days`, `7 Days`, `Expired`, and `Stalled`, search input, license-type filter, table with clinician name, license type, expiration date, days remaining, last contact date, next follow-up date, latest note snippet, and quick-action trigger.
**User actions:** Switch bucket, search, filter by license type, open Screen 7, open Screen 6, launch new import, manually add a record.
**State changes:** Filters update local UI state only; opening quick update or detail does not change persisted data until a save action occurs.
**Acceptance criteria:**
- [ ] Screen 5 computes bucket counts using `LicenseRecord.expirationDate`, `LicenseRecord.nextFollowUpDate`, `LicenseRecord.trackingStatus`, and note existence, with `Stalled` defined as a non-`Cleared` record that has `nextFollowUpDate IS NULL` or no related `Note` rows.
- [ ] Clicking a summary card on Screen 5 filters the table to that exact bucket and leaves the user on Screen 5.
- [ ] The table on Screen 5 displays values from `Clinician.fullName`, `LicenseRecord.licenseType`, `LicenseRecord.expirationDate`, `LicenseRecord.lastContactDate`, `LicenseRecord.nextFollowUpDate`, and the newest related `Note.body` snippet when a note exists.
- [ ] Opening Screen 6 from a row and navigating back returns the user to Screen 5 with the same bucket and search filter still active in memory.
- [ ] When no `LicenseRecord` rows exist, Screen 5 shows an in-place empty state with a CTA to Screen 3 rather than a separate empty-state screen.
**Conditional states:** Loading shows dashboard skeletons. Empty state appears when the workspace has no license records. No-results state appears when filters return zero matches. Error shows a blocking retry state.

### Screen 6: Clinician Record [Required v1]
**Purpose:** Provide full context and edit access for one clinician’s RN or LVN record.
**Visual elements:** Clinician header, status chip, metadata card, BON renewal link, notes timeline, activity timeline, quick-update CTA.
**User actions:** Create a new record, edit license metadata, add a note, open quick update, open BON renewal page, return to dashboard.
**State changes:** Create mode inserts `Clinician` and `LicenseRecord`; editing updates `LicenseRecord` fields; adding a note creates `Note` and `ActivityEvent`; outbound BON link does not mutate app data.
**Acceptance criteria:**
- [ ] Choosing manual add from Screen 5 opens Screen 6 in create mode and saving creates one `Clinician` row plus one linked `LicenseRecord` row with `source = "Manual"`.
- [ ] Screen 6 loads a single `Clinician` plus its linked `LicenseRecord`, related `Note` rows ordered newest first, and related `ActivityEvent` rows ordered newest first.
- [ ] Editing fields on Screen 6 persists changes to the matching `LicenseRecord` row and writes an `ActivityEvent` with `eventType = "Field Update"` or `eventType = "Status Change"` as appropriate.
- [ ] Adding a note on Screen 6 creates one `Note` row with `body`, `licenseRecordId`, and `createdByUserId`, and also creates one `ActivityEvent` with `eventType = "Note Added"` and `noteId`.
- [ ] The BON renewal action on Screen 6 opens the official Texas BON renewal URL in a new browser tab and does not change any database row.
- [ ] If the record id is invalid, Screen 6 renders an in-app not-found state and provides a route back to Screen 5.
**Conditional states:** Loading shows a detail skeleton. Empty notes state shows inside the notes panel when no notes exist. Error keeps the last saved values visible and shows an inline retry path.

### Screen 7: Quick Update Drawer / Modal [Required v1]
**Purpose:** Let the coordinator update a record without leaving the queue.
**Visual elements:** Drawer or modal over the current screen, status selector, `lastContactDate` picker, `nextFollowUpDate` picker, note textarea, recent activity summary, save/cancel controls.
**User actions:** Change status, set dates, add note, save, cancel.
**State changes:** Save mutates `LicenseRecord.trackingStatus`, `lastContactDate`, `nextFollowUpDate`, and optionally creates a `Note`; it also writes matching `ActivityEvent` rows.
**Acceptance criteria:**
- [ ] Screen 7 preloads the current `LicenseRecord.trackingStatus`, `lastContactDate`, and `nextFollowUpDate` when opened from Screen 5 or Screen 6.
- [ ] Screen 7 rejects a save when `nextFollowUpDate` is earlier than `lastContactDate`.
- [ ] Saving Screen 7 updates only the changed `LicenseRecord` fields and preserves untouched values.
- [ ] If the note field is non-empty, saving Screen 7 creates a new `Note` row and a matching `ActivityEvent` with `eventType = "Note Added"`.
- [ ] If `trackingStatus` changes to `Cleared`, Screen 7 sets `LicenseRecord.clearedAt` to the save timestamp; if it changes away from `Cleared`, Screen 7 clears `clearedAt`.
**Conditional states:** Validation errors stay inside the drawer. Saving disables controls. Cancel closes Screen 7 without mutating persisted data.

### Screen 8: Reminder Settings [Required v1]
**Purpose:** Control digest cadence and deadline reminder behavior for the workspace.
**Visual elements:** Settings form, digest-day selector, recipient email input, `21 Days` toggle, `7 Days` toggle, last send status summary, optional test-email button.
**User actions:** Edit settings, save settings, send test email if the optional polish action is implemented.
**State changes:** Save updates `NotificationSettings`; sending a test email creates `EmailDelivery` and `ActivityEvent` rows when enabled.
**Acceptance criteria:**
- [ ] Screen 8 loads the saved `NotificationSettings.digestDay`, `recipientEmail`, `send21DayReminder`, and `send7DayReminder` values for the active `workspaceId`.
- [ ] Screen 8 rejects invalid `recipientEmail` format before saving.
- [ ] Saving Screen 8 updates exactly one `NotificationSettings` row for the workspace and does not create duplicate settings rows.
- [ ] Screen 8 displays the most recent send result using the newest related `EmailDelivery` rows for `Weekly Digest`, `21-Day Reminder`, and `7-Day Reminder`.
- [ ] If the optional test-email action is implemented, clicking it creates an `EmailDelivery` row with `emailType = "Test Email"` and a matching `ActivityEvent` with `eventType = "Test Email"`.
**Conditional states:** Loading shows skeletons. Validation errors remain inline. Save errors do not clear the last saved values. If optional test email is omitted, the rest of Screen 8 remains required v1 behavior.

### Screen 9: Activity History [Required v1]
**Purpose:** Provide auditability for imports, updates, notes, and email sends.
**Visual elements:** Chronological event feed, clinician filter, event-type filter, timestamps, before/after value snippets, linked record actions.
**User actions:** Filter by clinician, filter by event type, open a related clinician record.
**State changes:** Filtering changes query state only; opening a related record navigates to Screen 6 with no data mutation.
**Acceptance criteria:**
- [ ] Screen 9 lists `ActivityEvent` rows newest first for the active `workspaceId`.
- [ ] The event-type filter on Screen 9 uses only the exact values `Import`, `Field Update`, `Status Change`, `Note Added`, `Reminder Email`, `Digest Email`, and `Test Email`.
- [ ] Filtering Screen 9 by clinician returns only `ActivityEvent` rows whose `licenseRecordId` belongs to the selected `Clinician.id`.
- [ ] An imported roster, a field change, a status change, a note creation, and a reminder send each generate distinct `ActivityEvent` rows that are visible on Screen 9.
- [ ] If no activity exists yet, Screen 9 shows an in-place empty state rather than creating a separate screen entry.
**Conditional states:** Loading shows feed skeletons. Empty and filtered-no-results states render inside the same screen. Error shows retryable load failure.

### Screen 10: Weekly Digest Preview [Optional polish]
**Purpose:** Show the current contents of the next digest email before it is sent.
**Visual elements:** Counts by bucket, grouped exception lists, clinician links, return action to settings.
**User actions:** Review grouped exceptions, open a clinician record, return to reminder settings.
**State changes:** This screen is read-only; it queries current queue data and does not persist changes.
**Acceptance criteria:**
- [ ] If implemented, Screen 10 uses the same bucket logic as Screen 5 and the weekly digest job.
- [ ] If implemented, each clinician row on Screen 10 links to the matching Screen 6 record without mutating data.
- [ ] If implemented, Screen 10 shows zero-state content when no current exceptions qualify for the digest.
**Conditional states:** Loading and error states are contained within the same screen. If this screen is not implemented in v1, it is omitted entirely from the build.

## 10. External Integrations

### Service: Supabase Auth
- **Purpose:** Email/password authentication for the single coordinator account
- **Specifics:** Uses Supabase email/password auth methods for sign-in and reset-email delivery; store only `User.id` and `User.email` in app tables; reset flow is provider-hosted, which is why there is no dedicated password-reset screen in v1.

### Service: Supabase Postgres
- **Purpose:** Persist workspace, roster, notes, imports, activity history, and email delivery records
- **Specifics:** Access through server-side queries and mutations; enforce one-workspace-per-user via unique constraint on `Workspace.ownerUserId`; add indexes on `LicenseRecord.workspaceId`, `LicenseRecord.expirationDate`, `ActivityEvent.createdAt`, and `ImportRow.importJobId`.

### Service: Resend
- **Purpose:** Send weekly digest, `21-Day`, `7-Day`, and optional test emails
- **Specifics:** Use API key auth from server routes only; create one send per `EmailDelivery` row; record returned provider id in `providerMessageId`; failure must persist `deliveryStatus = "Failed"` and `errorMessage` for operator visibility.

### Service: Vercel Cron
- **Purpose:** Trigger scheduled digest and reminder jobs
- **Specifics:** Run one protected cron route daily; job determines which workspaces should receive a digest based on `NotificationSettings.digestDay` and `Workspace.timezone`; dedupe reminder sends so the same `LicenseRecord.id` and `emailType` are not sent twice for the same `expirationDate`.

### Service: Texas Board of Nursing website
- **Purpose:** Provide the official outbound renewal context link from the clinician record
- **Specifics:** This is a plain external link, not an API integration; treat the official renewal page URL as an app configuration constant; no renewal rules or BON data are scraped or stored in v1.

## 11. Open Risks & Assumptions to Verify Before Build
- **Risk/Assumption:** Agencies will switch from spreadsheet review to the standalone exceptions queue — **What to verify:** run one real coordinator through a live Monday review and measure whether Screen 5 reduces weekly triage time enough to justify switching.
- **Risk/Assumption:** Import cleanup is still manageable inside a solo-built wizard — **What to verify:** test three real agency CSVs for duplicate names, missing expiration dates, stale clinicians, and inconsistent headers before finalizing `ImportRow` validation rules.
- **Risk/Assumption:** Users will trust the queue after inevitable bad data or missed updates — **What to verify:** confirm that `Stalled`, activity history, and import-review states make stale or questionable records obvious enough that the app does not over-claim certainty.
- **Risk/Assumption:** Reminder email is useful but not sufficient product value on its own — **What to verify:** ask the pilot user whether digest plus reminders without the dashboard would still solve the job; if yes, keep the spreadsheet-plus-automation fallback explicit.
- **Risk/Assumption:** Single-user scope survives first pilot pressure — **What to verify:** confirm whether shared output by email is enough for week-1 usage or whether read-only visibility is an immediate blocker.

## 12. Visual Reference
- Live prototype (static mock — visual layout reference only): https://jakebuild.github.io/idea-factory/72-credentialcliff-is-a-fear-of-l/
- Screen files on GitHub: https://github.com/jakebuild/idea-factory/tree/gh-pages/72-credentialcliff-is-a-fear-of-l
- Source idea: https://github.com/jakebuild/idea-factory/blob/main/72-credentialcliff-is-a-fear-of-l/idea-v3.md
- Source critique: https://github.com/jakebuild/idea-factory/blob/main/72-credentialcliff-is-a-fear-of-l/critique-v3.md

Adopted from the static mock: desktop web admin shell, dark visual treatment, KPI summary cards above the main table, dense queue layout, and a detailed secondary view for a selected record.

Explicitly rejected from the static mock: hospital or enterprise branding, `Staffing Exceptions` or `Reports` as v1 navigation items, generic total-license dashboards disconnected from the weekly exception workflow, and any screen not listed in Section 6. The published prototype currently exposes only two partial visual screens (`Compliance Overview` and `License Tracking`), so the source idea and critique remain the source of truth for screen scope and interaction behavior.

## 13. Suggested Folder Structure

```text
src/
  app/
    (auth)/
      sign-in/page.tsx
    (app)/
      setup/page.tsx
      import/page.tsx
      dashboard/page.tsx
      clinicians/[licenseRecordId]/page.tsx
      settings/reminders/page.tsx
      activity/page.tsx
      digest-preview/page.tsx
    api/
      cron/
        digest/route.ts
  components/
    dashboard/
    import/
    clinicians/
    reminders/
    activity/
  db/
    schema.ts
    queries/
    mutations/
  lib/
    auth/
    csv/
    email/
    queue/
    texas-bon/
    validators/
  styles/
  types/
```
