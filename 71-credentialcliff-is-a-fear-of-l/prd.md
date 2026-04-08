# PRD: CredentialCliff

## 1. Overview
CredentialCliff is a mobile-first COI readiness app for solo handymen who subcontract for small property managers and need to resend a current Certificate of Insurance quickly. In v1, the product stores exactly one current COI, lets the user confirm its expiration date, save one renewal contact, export reminder events to a calendar flow, and resend the latest file from a dedicated status-driven home screen.

## 2. Target User
The target user is a solo handyman who gets repeated requests from small property managers to provide a current COI before vendor setup, site access, or a job start. Existing solutions fail because phone files, email search, and cloud storage do not make expiration status obvious, do not keep the latest document clearly separated from stale ones, and do not connect the current file, the renewal contact, and the reminder setup in one place.

## 3. Tech Stack
- Platform: Cross-platform mobile
- Language + framework: TypeScript + Expo + React Native
- Key libraries: `expo-router` for navigation, `@supabase/supabase-js` for anonymous auth + Postgres + Storage, `zustand` for app state, `react-hook-form` + `zod` for forms and validation, `expo-document-picker` and `expo-image-picker` for file input, `expo-file-system` for local temp files, `expo-sharing` for resend/share, `expo-linking` for broker contact actions, `ics` for calendar export file generation
- Hosting / deployment: Expo Application Services for builds, Supabase for backend
- Why this stack fits the solo dev + 2–4 week constraint: one TypeScript codebase covers iOS and Android, Expo gives fast iteration for mobile file and sharing flows, and Supabase removes the need to build a custom backend while still handling secure file storage and simple persistence.

## 4. V1 Scope
- **Required v1** Anonymous device-backed account bootstrap with no visible sign-in screen
- **Required v1** Landing page with wedge-specific copy and sample status card
- **Required v1** Upload one current COI as PDF, JPG, or PNG
- **Required v1** Manual confirmation of expiration date and optional insurer name
- **Required v1** Save one renewal contact with name plus phone, email, or website
- **Required v1** Calendar reminder export flow for the current COI
- **Required v1** Home status card with exact statuses `Active`, `Expiring Soon`, and `Expired`
- **Required v1** Dedicated resend/share flow for the latest COI file
- **Required v1** Replace-current-COI flow that keeps only one current COI in the product state
- **Required v1** Settings/support screen with data export, data deletion, support link, and plain-language product-boundary copy
- **Optional polish** Persist the last selected reminder offsets when the user replaces the COI
- **Optional polish** Show the last successful share timestamp on the resend screen
- **Optional polish** Use the mock's bottom-tab shell for `Home`, `Send`, and `Settings`
- **Optional polish** Match the mock's industrial visual tone, icon treatment, and bold card hierarchy without inheriting extra features

## 5. Out of Scope
- OCR or automatic date extraction
- Manual setup without uploading a COI file
- Multiple COIs or multiple credential types
- Workers comp, auto, umbrella, or coverage matrices
- Archive/history screens
- Share links
- SMS sending
- QR code or site-entry scanning
- Audit trail activity feeds
- Push notifications
- Email reminder delivery
- Compliance-rule databases
- Renewal-step generation
- Team accounts
- Payments

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Landing / Wedge-Specific Pitch | Required v1 |
| 2 | Add Current COI | Required v1 |
| 3 | Confirm COI Details | Required v1 |
| 4 | Save Renewal Contact | Required v1 |
| 5 | Reminder Setup | Required v1 |
| 6 | Home / Status Card | Required v1 |
| 7 | Resend Current COI | Required v1 |
| 8 | COI Detail / Edit | Required v1 |
| 9 | Replace Current COI | Required v1 |
| 10 | Settings / Support | Required v1 |

## 7. State Model

### Global
- **State name:** Unauthenticated bootstrap
  - Trigger: app launch before `AppSession.userId` is created through anonymous auth
  - Shows: splash/loading shell only
  - Actions: retry bootstrap
- **State name:** Authenticated session active
  - Trigger: `AppSession.userId` exists
  - Shows: app screens
  - Actions: navigate normally
- **State name:** Bootstrap error
  - Trigger: anonymous auth or initial data fetch fails
  - Shows: blocking error with retry
  - Actions: retry bootstrap

### Screen 1. Landing / Wedge-Specific Pitch
- **State name:** First-time visitor
  - Trigger: `UserProfile.onboardingStatus = "Not Started"`
  - Shows: headline, proof-of-pain copy, sample status card, CTA to start upload
  - Actions: go to Screen 2
- **State name:** Returning visitor
  - Trigger: `UserProfile.onboardingStatus = "Complete"`
  - Shows: same hero plus CTA to go to Screen 6
  - Actions: go to Screen 6, go to Screen 10

### Screen 2. Add Current COI
- **State name:** Empty upload
  - Trigger: `CurrentCoi.id` does not exist
  - Shows: upload area, accepted file types, privacy copy
  - Actions: pick PDF, pick image, capture image, continue after upload
- **State name:** Uploading
  - Trigger: file selected and upload in progress
  - Shows: progress/loading state
  - Actions: cancel upload
- **State name:** Upload complete
  - Trigger: `CurrentCoi.fileStatus = "Ready"`
  - Shows: file name, file type, replace selection action, continue CTA
  - Actions: continue to Screen 3, choose different file
- **State name:** Upload error
  - Trigger: file validation or storage upload fails
  - Shows: inline error
  - Actions: retry upload, choose different file

### Screen 3. Confirm COI Details
- **State name:** Ready to confirm
  - Trigger: `CurrentCoi.fileStatus = "Ready"` and `CurrentCoi.confirmedExpirationDate` is empty
  - Shows: file summary, expiration date field, optional insurer name, label field
  - Actions: enter fields, continue
- **State name:** Validation error
  - Trigger: submit with empty expiration date or invalid past-format date
  - Shows: inline field errors
  - Actions: correct fields, resubmit
- **State name:** Saved
  - Trigger: valid details saved
  - Shows: status preview using saved date
  - Actions: continue to Screen 4
- **State name:** Save error
  - Trigger: persistence fails
  - Shows: blocking inline error
  - Actions: retry save

### Screen 4. Save Renewal Contact
- **State name:** Empty form
  - Trigger: `RenewalContact.id` does not exist
  - Shows: contact name, phone, email, website, default-action copy
  - Actions: enter fields, save
- **State name:** Validation error
  - Trigger: submit with empty `name` or with no phone/email/website
  - Shows: inline field errors
  - Actions: correct fields, resubmit
- **State name:** Saved
  - Trigger: contact saved successfully
  - Shows: saved values and continue CTA
  - Actions: continue to Screen 5
- **State name:** Save error
  - Trigger: persistence fails
  - Shows: inline error
  - Actions: retry save

### Screen 5. Reminder Setup
- **State name:** Not exported
  - Trigger: `ReminderPlan.exportStatus = "Not Started"`
  - Shows: default reminder offsets of 30, 14, and 7 days before expiration, explanation of calendar export limits, export CTA, skip CTA
  - Actions: export reminders, skip setup
- **State name:** Exporting
  - Trigger: ICS generation or share/open flow in progress
  - Shows: loading state
  - Actions: cancel
- **State name:** Exported
  - Trigger: `ReminderPlan.exportStatus = "Exported"`
  - Shows: exported confirmation and completion CTA
  - Actions: go to Screen 6
- **State name:** Skipped
  - Trigger: `ReminderPlan.exportStatus = "Skipped"`
  - Shows: skipped state with reminder warning copy
  - Actions: export later, go to Screen 6
- **State name:** Export error
  - Trigger: ICS generation fails or share/open flow errors
  - Shows: inline error
  - Actions: retry export, skip

### Screen 6. Home / Status Card
- **State name:** No current COI
  - Trigger: `CurrentCoi.id` does not exist
  - Shows: empty status card with add-COI CTA
  - Actions: go to Screen 2
- **State name:** Active
  - Trigger: `CurrentCoi.status = "Active"`
  - Shows: green card, expiration summary, renewal contact shortcut, resend CTA, replace CTA
  - Actions: go to Screen 7, call/email/open contact action, go to Screen 8, go to Screen 9
- **State name:** Expiring Soon
  - Trigger: `CurrentCoi.status = "Expiring Soon"`
  - Shows: yellow card with days remaining and same actions
  - Actions: go to Screen 7, go to Screen 8, go to Screen 9, use renewal contact action
- **State name:** Expired
  - Trigger: `CurrentCoi.status = "Expired"`
  - Shows: red card, expired warning, replace CTA, renewal contact shortcut, resend CTA with expired warning
  - Actions: go to Screen 7, go to Screen 9, use renewal contact action
- **State name:** Load error
  - Trigger: current record fetch fails
  - Shows: blocking error with retry
  - Actions: retry load

### Screen 7. Resend Current COI
- **State name:** Ready to share
  - Trigger: `CurrentCoi.fileStatus = "Ready"` and `CurrentCoi.status` is `Active` or `Expiring Soon`
  - Shows: file preview, expiration summary, share CTA, download/open CTA
  - Actions: open native share sheet, open file preview
- **State name:** Expired warning
  - Trigger: `CurrentCoi.status = "Expired"`
  - Shows: same layout with persistent expired warning
  - Actions: open native share sheet anyway, go to Screen 9
- **State name:** Share error
  - Trigger: native share sheet or file open fails
  - Shows: inline error
  - Actions: retry share, return to Screen 6

### Screen 8. COI Detail / Edit
- **State name:** View details
  - Trigger: current COI exists
  - Shows: file summary, confirmed metadata, renewal contact summary, reminder summary, edit actions
  - Actions: edit metadata, edit renewal contact, edit reminder plan, replace COI, delete all data via Screen 10
- **State name:** Edit metadata
  - Trigger: user taps edit metadata
  - Shows: editable label, expiration date, insurer name
  - Actions: save changes, cancel
- **State name:** Save error
  - Trigger: update fails
  - Shows: inline error
  - Actions: retry save

### Screen 9. Replace Current COI
- **State name:** Ready to replace
  - Trigger: current COI exists
  - Shows: current file summary, new upload control, new expiration date field, save action
  - Actions: select replacement file, enter new date, save replacement
- **State name:** Replacing
  - Trigger: new file upload or record update in progress
  - Shows: loading/progress state
  - Actions: none
- **State name:** Validation error
  - Trigger: submit without replacement file or without new expiration date
  - Shows: inline field errors
  - Actions: correct fields, resubmit
- **State name:** Replace success
  - Trigger: replacement save succeeds
  - Shows: updated status preview and CTA back to Screen 6
  - Actions: go to Screen 6
- **State name:** Replace error
  - Trigger: upload, file delete, or record update fails
  - Shows: inline error with no partial-success claim
  - Actions: retry replacement

### Screen 10. Settings / Support
- **State name:** Standard
  - Trigger: screen opens normally
  - Shows: support link, export data action, delete data action, privacy copy, product-boundary copy
  - Actions: export data, delete data, contact support
- **State name:** Exporting data
  - Trigger: data export generation in progress
  - Shows: loading state
  - Actions: wait
- **State name:** Delete confirmation
  - Trigger: user taps delete data
  - Shows: destructive confirmation
  - Actions: confirm delete, cancel
- **State name:** Delete success
  - Trigger: delete completes
  - Shows: success state and CTA back to Screen 1
  - Actions: go to Screen 1
- **State name:** Error
  - Trigger: export or delete fails
  - Shows: inline error
  - Actions: retry

## 8. Data Model

### Entity: AppSession
- **Fields:**
  - `userId: string` — anonymous auth id used across storage and database rows
  - `sessionStatus: "Bootstrapping" | "Active" | "Error"` — global session state shown on app load
  - `createdAt: string` — ISO timestamp for first bootstrap
  - `lastOpenedAt: string` — ISO timestamp for last successful app open
- **Relationships:** belongs to one `UserProfile`

### Entity: UserProfile
- **Fields:**
  - `userId: string` — primary key; matches `AppSession.userId`
  - `onboardingStatus: "Not Started" | "In Progress" | "Complete"` — drives Screen 1 and onboarding return behavior
  - `supportEmail: string` — static support destination rendered on Screen 10
  - `boundaryCopyVersion: string` — rendered disclaimer version currently accepted by the app build
  - `createdAt: string` — ISO timestamp
  - `updatedAt: string` — ISO timestamp
- **Relationships:** has one `CurrentCoi`, has one `RenewalContact`, has one `ReminderPlan`

### Entity: CurrentCoi
- **Fields:**
  - `id: string` — primary key
  - `userId: string` — owner id
  - `label: string` — editable document label shown on Screens 3, 7, and 8
  - `insurerName: string | null` — optional insurer/carrier name
  - `originalFilename: string` — uploaded file name shown in UI
  - `mimeType: "application/pdf" | "image/jpeg" | "image/png"` — exact accepted file types
  - `fileSizeBytes: number` — uploaded size shown in file summary
  - `storagePath: string` — Supabase Storage path for the current file
  - `fileStatus: "Uploading" | "Ready" | "Error"` — persisted file readiness state used by Screens 2, 6, and 7
  - `confirmedExpirationDate: string` — ISO date manually confirmed on Screen 3 or Screen 9
  - `status: "Active" | "Expiring Soon" | "Expired"` — exact status card label shown in UI; computed as `Expired` when `confirmedExpirationDate` is before the device local date, `Expiring Soon` when the date is today through 30 days ahead, and `Active` when more than 30 days ahead
  - `statusComputedAt: string` — ISO timestamp of last status recompute
  - `uploadedAt: string` — ISO timestamp of current file upload
  - `updatedAt: string` — ISO timestamp of last metadata or file update
- **Relationships:** belongs to `UserProfile`

### Entity: RenewalContact
- **Fields:**
  - `id: string` — primary key
  - `userId: string` — owner id
  - `name: string` — broker or insurer contact name
  - `phone: string | null` — phone action target
  - `email: string | null` — email action target
  - `website: string | null` — website action target
  - `defaultAction: "Phone" | "Email" | "Website"` — default renewal shortcut shown on Screen 6
  - `updatedAt: string` — ISO timestamp
- **Relationships:** belongs to `UserProfile`

### Entity: ReminderPlan
- **Fields:**
  - `id: string` — primary key
  - `userId: string` — owner id
  - `offsetDays: number[]` — array of reminder offsets used to generate the ICS export; v1 default is `[30, 14, 7]`
  - `exportStatus: "Not Started" | "Exported" | "Skipped" | "Error"` — persisted reminder setup state shown on Screens 5 and 8
  - `exportedAt: string | null` — ISO timestamp of the last successful export action
  - `lastExportFilename: string | null` — generated ICS filename for user reference
  - `updatedAt: string` — ISO timestamp
- **Relationships:** belongs to `UserProfile`

### Entity: ShareEvent
- **Fields:**
  - `id: string` — primary key
  - `userId: string` — owner id
  - `coiId: string` — source `CurrentCoi.id`
  - `sharedAt: string` — ISO timestamp of successful native share-sheet open
  - `method: "Native Share"` — exact v1 share method
- **Relationships:** belongs to `UserProfile`, references `CurrentCoi`

## 9. Screens & Acceptance Criteria

### Screen 1: Landing / Wedge-Specific Pitch [Required v1]
**Purpose:** explain the narrow use case and start the setup flow.
**Visual elements:** mobile hero, short wedge-specific copy, sample status card, primary CTA, optional secondary CTA for returning users.
**User actions:** start setup, continue to home when onboarding is complete, open settings.
**State changes:** tapping start creates or updates `UserProfile.onboardingStatus` to `In Progress`; tapping continue navigates without mutating data.
**Acceptance criteria:**
- [ ] Screen 1 shows first-time landing content when `UserProfile.onboardingStatus = "Not Started"` and hides the continue-to-home CTA.
- [ ] Tapping Screen 1 primary CTA sets `UserProfile.onboardingStatus = "In Progress"` and opens Screen 2.
- [ ] When `UserProfile.onboardingStatus = "Complete"`, Screen 1 shows a continue CTA that opens Screen 6.
- [ ] Screen 1 matches the mock's mobile card hierarchy and industrial tone but does not show mock-only features such as multi-policy matrices, secure links, or audit trails.
**Conditional states:** loading while `AppSession.sessionStatus = "Bootstrapping"`; blocking error when `AppSession.sessionStatus = "Error"`.

### Screen 2: Add Current COI [Required v1]
**Purpose:** collect the current COI file.
**Visual elements:** upload zone, accepted-type guidance, privacy copy, file summary after upload, continue CTA.
**User actions:** choose file from device, take photo, retry after error, continue.
**State changes:** successful upload writes `CurrentCoi.originalFilename`, `mimeType`, `fileSizeBytes`, `storagePath`, `fileStatus`, `uploadedAt`, and `updatedAt`.
**Acceptance criteria:**
- [ ] Screen 2 accepts only `application/pdf`, `image/jpeg`, and `image/png` and rejects all other file types before upload.
- [ ] A successful upload sets `CurrentCoi.fileStatus = "Ready"` and persists a non-empty `CurrentCoi.storagePath`.
- [ ] Screen 2 does not allow setup completion without a stored `CurrentCoi.storagePath`; the mock/source "skip to manual data entry" path is explicitly rejected in v1.
- [ ] If upload fails, `CurrentCoi.fileStatus = "Error"` and Screen 2 shows a retry path without navigating forward.
**Conditional states:** empty before selection; uploading while file transfer is in progress; error when upload fails.

### Screen 3: Confirm COI Details [Required v1]
**Purpose:** confirm stable metadata for the current file.
**Visual elements:** file summary, date input, label input, optional insurer input, status preview card, continue CTA.
**User actions:** edit fields, save, continue.
**State changes:** saving writes `CurrentCoi.label`, `CurrentCoi.insurerName`, `CurrentCoi.confirmedExpirationDate`, `CurrentCoi.status`, `CurrentCoi.statusComputedAt`, and `CurrentCoi.updatedAt`.
**Acceptance criteria:**
- [ ] Screen 3 cannot save until `CurrentCoi.confirmedExpirationDate` contains a valid ISO date.
- [ ] Saving Screen 3 recomputes `CurrentCoi.status` using the exact enum values `Active`, `Expiring Soon`, or `Expired`, where `Expired` means the expiration date is before the device local date, `Expiring Soon` means today through 30 days ahead, and `Active` means more than 30 days ahead.
- [ ] The status preview on Screen 3 reflects the saved `CurrentCoi.status` value immediately after save.
- [ ] Navigating back to Screen 3 shows the persisted `CurrentCoi.label`, `CurrentCoi.insurerName`, and `CurrentCoi.confirmedExpirationDate` values.
**Conditional states:** validation error on missing or invalid date; save error on persistence failure.

### Screen 4: Save Renewal Contact [Required v1]
**Purpose:** store the default renewal action path.
**Visual elements:** contact name field, phone/email/website fields, explanatory copy, save CTA.
**User actions:** enter or edit contact details, save, continue.
**State changes:** save writes all `RenewalContact` fields and sets `updatedAt`.
**Acceptance criteria:**
- [ ] Screen 4 requires `RenewalContact.name` and at least one of `RenewalContact.phone`, `RenewalContact.email`, or `RenewalContact.website`.
- [ ] Saving Screen 4 computes `RenewalContact.defaultAction` to `Phone`, `Email`, or `Website` based on the first populated field in priority order phone, then email, then website.
- [ ] After save, Screen 6 uses `RenewalContact.defaultAction` for the primary renewal shortcut.
- [ ] Reopening Screen 4 shows the persisted `RenewalContact` values.
**Conditional states:** validation error on incomplete form; save error on persistence failure.

### Screen 5: Reminder Setup [Required v1]
**Purpose:** let the user export reminder events for the current COI.
**Visual elements:** reminder offsets, explanation that v1 exports calendar events rather than sending push notifications, export CTA, skip CTA.
**User actions:** export reminders, skip, retry after failure.
**State changes:** export writes `ReminderPlan.offsetDays`, `ReminderPlan.exportStatus`, `ReminderPlan.exportedAt`, `ReminderPlan.lastExportFilename`, and `ReminderPlan.updatedAt`; skip writes `ReminderPlan.exportStatus = "Skipped"`.
**Acceptance criteria:**
- [ ] Screen 5 initializes `ReminderPlan.offsetDays` to `[30, 14, 7]` and uses that exact array to generate the exported ICS file.
- [ ] A successful export sets `ReminderPlan.exportStatus = "Exported"` and stores a non-null `ReminderPlan.exportedAt`.
- [ ] Tapping skip sets `ReminderPlan.exportStatus = "Skipped"` and still allows navigation to Screen 6.
- [ ] Screen 5 states explicitly that reminder delivery is not guaranteed by the app after export and does not claim native push notification support.
**Conditional states:** exporting while ICS file is being generated or handed off; export error when the file cannot be generated or opened.

### Screen 6: Home / Status Card [Required v1]
**Purpose:** show current readiness and the fastest next actions.
**Visual elements:** status card, expiration copy, resend CTA, renewal contact shortcut, replace CTA, bottom-nav shell if polish is enabled.
**User actions:** resend current COI, contact broker/insurer, open details, replace COI.
**State changes:** opening renewal shortcuts does not mutate data; successful share from Screen 7 can append `ShareEvent`; replace flow updates `CurrentCoi`.
**Acceptance criteria:**
- [ ] Screen 6 renders an empty state when no `CurrentCoi.id` exists instead of treating empty state as a separate screen.
- [ ] Screen 6 displays the exact label from `CurrentCoi.status` and the exact `CurrentCoi.confirmedExpirationDate`.
- [ ] Tapping resend on Screen 6 opens Screen 7 with the same `CurrentCoi.id` the home card is displaying.
- [ ] Tapping replace on Screen 6 opens Screen 9 and, after a successful replacement, returning to Screen 6 shows the new `CurrentCoi.originalFilename` and `CurrentCoi.confirmedExpirationDate`.
- [ ] Screen 6 does not show mock-only content such as recent activity feeds, QR site-entry actions, or other coverage cards.
**Conditional states:** empty when no COI exists; load error when fetch fails; red/yellow/green card variants based on `CurrentCoi.status`.

### Screen 7: Resend Current COI [Required v1]
**Purpose:** make the latest COI fast to resend.
**Visual elements:** file preview, document label, expiration summary, expired warning when needed, native share CTA.
**User actions:** open native share sheet, preview file, go to replace flow when expired.
**State changes:** a successful share writes one `ShareEvent` row with `method = "Native Share"` and `sharedAt`.
**Acceptance criteria:**
- [ ] Screen 7 always reads the file referenced by `CurrentCoi.storagePath` and never surfaces a stale or previous file.
- [ ] When `CurrentCoi.status = "Expired"`, Screen 7 shows a persistent expired warning before the share CTA.
- [ ] A successful share action inserts a `ShareEvent` row with the current `ShareEvent.coiId = CurrentCoi.id`.
- [ ] Screen 7 supports only the native share sheet in v1 and does not expose secure-link or SMS-specific actions from the mock.
**Conditional states:** share error when native share is unavailable; expired warning when `CurrentCoi.status = "Expired"`.

### Screen 8: COI Detail / Edit [Required v1]
**Purpose:** view and edit current metadata in one place.
**Visual elements:** current file summary, editable metadata section, renewal contact summary, reminder setup summary, replace CTA.
**User actions:** edit metadata, edit contact, revisit reminder setup, replace current COI.
**State changes:** saving metadata updates `CurrentCoi`; editing contact updates `RenewalContact`; editing reminders updates `ReminderPlan`.
**Acceptance criteria:**
- [ ] Screen 8 displays the current values from `CurrentCoi`, `RenewalContact`, and `ReminderPlan` in one view.
- [ ] Saving metadata changes on Screen 8 updates `CurrentCoi.updatedAt`.
- [ ] Screen 8 shows the reminder status using `ReminderPlan.exportStatus` and not by inferring calendar success from device state.
- [ ] Screen 8 does not include mock-only sections for coverage limits, archived documents, or audit trails.
**Conditional states:** standard view when all entities exist; inline save error on update failure.

### Screen 9: Replace Current COI [Required v1]
**Purpose:** swap the current COI with a renewed file and new expiration date.
**Visual elements:** current file summary, replacement upload control, new expiration date field, updated status preview, save CTA.
**User actions:** select replacement file, enter new expiration date, save replacement, cancel.
**State changes:** successful replacement updates `CurrentCoi.originalFilename`, `mimeType`, `fileSizeBytes`, `storagePath`, `confirmedExpirationDate`, `status`, `statusComputedAt`, `uploadedAt`, and `updatedAt`.
**Acceptance criteria:**
- [ ] Screen 9 cannot save until a new file is uploaded and a new `CurrentCoi.confirmedExpirationDate` is entered.
- [ ] A successful replacement overwrites the current record referenced by `CurrentCoi.id` and Screen 7 subsequently shares the new `CurrentCoi.storagePath`.
- [ ] After successful replacement, Screen 6 shows the recomputed `CurrentCoi.status` immediately.
- [ ] V1 does not preserve user-visible replacement history; the mock's archive/audit behavior is explicitly rejected.
**Conditional states:** replacing/loading while upload is in progress; validation error on missing file or date; replace error on upload or save failure.

### Screen 10: Settings / Support [Required v1]
**Purpose:** provide privacy controls, support, and product-boundary copy.
**Visual elements:** support email/link, export data action, delete data action, privacy copy, disclaimer copy.
**User actions:** export data, confirm delete, contact support.
**State changes:** export creates a bundle from persisted entities; delete removes `CurrentCoi`, `RenewalContact`, `ReminderPlan`, `UserProfile`, `ShareEvent`, and the stored file at `CurrentCoi.storagePath`.
**Acceptance criteria:**
- [ ] Screen 10 renders product-boundary copy stating the app does not provide legal or compliance advice and ties that copy to `UserProfile.boundaryCopyVersion`.
- [ ] Exporting data includes persisted records from `UserProfile`, `CurrentCoi`, `RenewalContact`, `ReminderPlan`, and `ShareEvent`.
- [ ] Confirming delete removes the stored file referenced by `CurrentCoi.storagePath` and deletes all persisted rows for the current `AppSession.userId`.
- [ ] After successful delete, the next bootstrap recreates a fresh `UserProfile` with `onboardingStatus = "Not Started"` and returns the app to Screen 1.
**Conditional states:** exporting data; delete confirmation; delete success; error on export or delete failure.

## 10. External Integrations

### Service: Supabase Auth
- **Purpose:** create a durable anonymous user id without a visible sign-in flow
- **Specifics:** use anonymous auth from `@supabase/supabase-js`; persist the returned user id locally; retry bootstrap on expired or missing session; if anonymous auth is unavailable, block the app at bootstrap instead of silently falling back to local-only unsynced storage

### Service: Supabase Storage
- **Purpose:** store the current COI file securely
- **Specifics:** upload the selected file to a per-user path such as `coi/{userId}/current`; validate MIME type and max size on client before upload; delete the previous object only after the replacement upload succeeds; use signed URLs only for transient preview/download access

### Service: Supabase Postgres
- **Purpose:** persist app records for `UserProfile`, `CurrentCoi`, `RenewalContact`, `ReminderPlan`, and `ShareEvent`
- **Specifics:** use one row per entity per anonymous user where appropriate; compute `CurrentCoi.status` in app logic and persist the enum value; require row-level security keyed to the anonymous auth user id

### Service: Native Share Sheet via `expo-sharing`
- **Purpose:** resend the latest COI from Screen 7
- **Specifics:** share the local cached copy of the file referenced by `CurrentCoi.storagePath`; treat successful share-sheet open as a share event; do not claim delivery confirmation because the OS share sheet does not provide it reliably

### Service: Device Linking via `expo-linking`
- **Purpose:** trigger the saved renewal contact action
- **Specifics:** open `tel:`, `mailto:`, or HTTPS URLs from `RenewalContact.defaultAction`; validate URL format before save; if the device cannot handle the target scheme, show an inline error instead of failing silently

### Service: ICS Export via `ics`
- **Purpose:** generate reminder events for external calendar import
- **Specifics:** create one ICS payload from `ReminderPlan.offsetDays` and `CurrentCoi.confirmedExpirationDate`; hand off the file through native share/open flow; do not mark reminders as guaranteed-delivered after export because the app cannot verify calendar import success

## 11. Open Risks & Assumptions to Verify Before Build
- **Risk/Assumption:** handymen for property managers are asked to resend COIs often enough to justify a dedicated resend screen — **What to verify:** interview at least 10 target users and measure how often they were asked for a COI in the prior 90 days.
- **Risk/Assumption:** the resend flow is materially faster than using email search or phone files — **What to verify:** run a timed comparison test with real users sending the same COI from their current workflow versus the prototype.
- **Risk/Assumption:** calendar export is acceptable despite weaker reliability than push — **What to verify:** test the export/import flow on current iPhone and Android devices and confirm users understand the failure boundary.
- **Risk/Assumption:** anonymous auth plus remote file storage is trusted enough for insurance documents — **What to verify:** confirm users understand where the file is stored and whether the privacy copy is sufficient to overcome trust objections.
- **Risk/Assumption:** file replacement on mobile can be made reliable across PDF and photo uploads — **What to verify:** test upload, preview, share, and replacement flows on both PDF and image files across iOS and Android before building polish.

## 12. Visual Reference
- Live prototype (static mock — visual layout reference only): https://jakebuild.github.io/idea-factory/71-credentialcliff-is-a-fear-of-l/
- Screen files on GitHub: https://github.com/jakebuild/idea-factory/tree/gh-pages/71-credentialcliff-is-a-fear-of-l
- Source idea: https://github.com/jakebuild/idea-factory/blob/main/71-credentialcliff-is-a-fear-of-l/idea-v3.md
- Source critique: https://github.com/jakebuild/idea-factory/blob/main/71-credentialcliff-is-a-fear-of-l/critique-v3.md
- Adopt explicitly from the mock: mobile-first single-column layout, bold red/yellow/green status-card hierarchy, industrial card styling, upload-confirm-contact-reminder onboarding sequence, and a simple bottom-nav shell if included.
- Reject explicitly from the mock: OCR/autoscanning claims, multiple coverage cards, policy matrices, secure-link sharing, SMS actions, QR site-entry actions, audit trail/history, recent activity feeds, coverage-limit sections, archived document references, additional alert email, and any language implying automated compliance monitoring.

## 13. Suggested Folder Structure
```text
credentialcliff/
  app/
    _layout.tsx
    index.tsx
    add-coi.tsx
    confirm-coi.tsx
    renewal-contact.tsx
    reminder-setup.tsx
    (tabs)/
      _layout.tsx
      home.tsx
      resend.tsx
      settings.tsx
    coi-detail.tsx
    replace-coi.tsx
  components/
    status-card.tsx
    file-upload-card.tsx
    renewal-contact-card.tsx
    reminder-summary-card.tsx
  lib/
    supabase.ts
    auth.ts
    storage.ts
    status.ts
    calendar-export.ts
    share-current-coi.ts
    renewal-linking.ts
  stores/
    session-store.ts
    coi-store.ts
  schemas/
    current-coi.ts
    renewal-contact.ts
    reminder-plan.ts
  types/
    data-model.ts
  supabase/
    migrations/
  assets/
  README.md
```
