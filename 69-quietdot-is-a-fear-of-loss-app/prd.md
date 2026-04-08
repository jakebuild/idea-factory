# PRD: QuietDot

## 1. Overview
QuietDot is a mobile-first web app plus SMS workflow for one adult child managing recurring check-ins with one older parent. The child sets a schedule, QuietDot sends the SMS automatically, the parent replies through a one-tap mobile web page with no app install, and the child sees only factual daily states that reduce repetitive manual texts without implying emergency monitoring.

## 2. Target User
The target user is an adult child who already sends routine "just checking in" texts to one parent and wants a calmer, repeatable workflow instead of ad hoc follow-ups. Existing solutions fail because emergency and monitoring products overclaim, while normal messaging apps do not automate the recurring ritual or keep a clean status log for one relationship.

## 3. Tech Stack
- Platform: Web
- Language + framework: TypeScript with Next.js 16 App Router
- Key libraries: Supabase Auth (email OTP), Supabase Postgres, Tailwind CSS, shadcn/ui, React Hook Form, Zod, date-fns + date-fns-tz, Twilio Node SDK
- Hosting / deployment: Vercel for the web app and cron routes; Supabase for database and auth
- Why this stack fits the solo dev + 2–4 week constraint: one deployable codebase covers the child dashboard and parent reply page, Supabase removes custom auth/back-end scaffolding, and Twilio + Vercel cron is enough for scheduled SMS, reminder timing, and tokenized reply links without building native apps or separate workers in v1.

## 4. V1 Scope
- **Required v1** Child landing page with honest positioning and `not emergency monitoring` copy
- **Required v1** Email OTP sign-in for the child
- **Required v1** Single-plan setup for exactly one parent and one recurring schedule
- **Required v1** Editable check-in SMS and reminder SMS using 2-3 presets
- **Required v1** First-send confirmation that sends the initial SMS immediately
- **Required v1** Parent mobile web reply page with one primary action: `I'm okay`
- **Required v1** One follow-up reminder per scheduled check-in
- **Required v1** Child dashboard showing only `Checked in`, `Reminder sent`, `Paused`, and `No response yet`
- **Required v1** Manual `Send reminder`, `Call now`, and `Pause schedule` actions from the dashboard
- **Required v1** Recent activity list on the dashboard for the latest check-ins and reminders
- **Optional polish** Full history screen with 7-day and 30-day views filtered by the four allowed statuses
- **Optional polish** Separate settings/help screen for editing the existing plan outside the setup flow

## 5. Out of Scope
- Native parent app
- Native child app
- Widget
- Passive monitoring
- `Phone offline` or device-status detection
- Location or health data
- Backup contacts
- Escalation ladders
- `Please call me` parent distress button
- Multi-parent households
- Streaks or gamification
- AI summaries
- Payments and subscriptions
- Social sharing

## 6. Screen Inventory
| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Landing / Product Promise | Required v1 |
| 2 | Child Setup Wizard | Required v1 |
| 3 | Message Template Editor | Required v1 |
| 4 | First Send Confirmation | Required v1 |
| 5 | Parent Check-in Page | Required v1 |
| 6 | Parent Confirmation Page | Required v1 |
| 7 | Child Dashboard | Required v1 |
| 8 | Pause Schedule | Required v1 |
| 9 | Check-in History | Optional polish |
| 10 | Settings / Trust & Help | Optional polish |

## 7. State Model
### Screen 1: Landing / Product Promise
- **Unauthenticated:** default state; shows product promise, `not emergency monitoring` notice, and CTA to sign in.
- **Auth loading:** shown after CTA submit while email OTP request is in progress; CTA disabled.
- **Authenticated with incomplete setup:** redirect to Screen 2 when `care_plans.onboarding_state = setup_incomplete`.
- **Authenticated with completed setup:** redirect to Screen 7 when `care_plans.onboarding_state = active`.
- **Auth error:** inline error when OTP request fails.

### Screen 2: Child Setup Wizard
- **Empty form:** first-time setup with blank or auto-filled timezone.
- **Editing existing plan:** same screen prefilled from `care_plans` when launched from Screen 7 or Screen 10.
- **Saving:** primary CTA disabled while plan draft persists.
- **Validation error:** field-level errors for invalid phone, missing day selection, or missing send time.
- **Save error:** inline banner if draft save fails.

### Screen 3: Message Template Editor
- **Preset selected:** one of the predefined templates populates both message fields.
- **Custom edited:** child edits message bodies directly; character counts update live.
- **Saving:** save CTA disabled while `message_templates` updates persist.
- **Validation error:** shown when body exceeds the v1 character limit or becomes empty.

### Screen 4: First Send Confirmation
- **Ready to send:** shows rendered first SMS preview with appended reply-link placeholder and reminder preview.
- **Sending:** primary CTA disabled while initial `check_in_occurrences` row is created and Twilio send is requested.
- **Sent:** shows delivery state from `check_in_occurrences.delivery_status`.
- **Send failed:** inline error with retry CTA when Twilio send fails.

### Screen 5: Parent Check-in Page
- **Valid token / pending response:** shows a single large `I'm okay` button and note that the child will see today's reply.
- **Submitting response:** button disabled during write.
- **Already responded:** if `check_in_occurrences.responded_at` exists for the token, redirect to Screen 6 with duplicate-response copy.
- **Expired or invalid token:** error copy with instruction to return to the original SMS; no alternative action.

### Screen 6: Parent Confirmation Page
- **Recorded:** shows success message and timestamp from `check_in_occurrences.responded_at`.
- **Duplicate visit:** shows that today's reply was already recorded and repeats the recorded timestamp.
- **Lookup error:** generic failure state if the token cannot be resolved after response.

### Screen 7: Child Dashboard
- **Loading:** skeleton for status card and activity list.
- **No plan yet:** if no `care_plans` row exists, CTA routes to Screen 2.
- **Active / Checked in:** shows status label `Checked in`, last reply time, and disabled `Send reminder`.
- **Active / Reminder sent:** shows status label `Reminder sent`, reminder timestamp, and disabled `Send reminder`.
- **Active / No response yet:** shows status label `No response yet`, enables `Send reminder`, and enables `Call now`.
- **Paused:** shows status label `Paused`, pause date range, and CTA to edit or end pause.
- **Dashboard error:** retry banner if latest status query fails.
- **Opted out:** warning banner when `care_plans.sms_opt_out_at` is not null; all send actions disabled.

### Screen 8: Pause Schedule
- **Create pause:** blank start/end date fields defaulting to today.
- **Edit pause:** existing pause values loaded from `pause_windows`.
- **Saving:** CTA disabled while pause persists.
- **Validation error:** shown when end date is before start date.
- **Save error:** inline banner on failure.

### Screen 9: Check-in History
- **Loading:** skeleton list.
- **Default list:** latest occurrences for last 7 days.
- **Filtered list:** same list filtered by one of the four status values.
- **Empty history:** shown when no occurrences exist in the selected window.
- **Query error:** inline retry banner.

### Screen 10: Settings / Trust & Help
- **Default:** shows editable plan details, reminder delay, legal/help links, and repeated `not emergency monitoring` copy.
- **Saving:** CTA disabled while updates persist.
- **Save success:** inline confirmation after update.
- **Save error:** inline error if update fails.

## 8. Data Model
### Entity: users
- **Fields:**
  - `id: uuid` — primary key; matches Supabase Auth user id
  - `email: text` — child login email
  - `full_name: text | null` — child display name used in SMS copy
  - `created_at: timestamptz` — creation timestamp
- **Relationships:** has one `care_plans`

### Entity: care_plans
- **Fields:**
  - `id: uuid` — primary key
  - `user_id: uuid` — belongs to `users`
  - `parent_name: text` — parent name shown in UI and message previews
  - `parent_phone_e164: text` — parent phone number in E.164 format
  - `relationship_label: text` — child-entered label such as `Mom` or `Dad`
  - `timezone_iana: text` — plan timezone used for schedule calculation
  - `schedule_days: text[]` — exact day names shown in the UI, e.g. `Monday`
  - `scheduled_time_local: text` — local send time in `HH:mm`
  - `reminder_delay_minutes: integer` — delay before one reminder can be sent
  - `onboarding_state: text` — `setup_incomplete` or `active`
  - `is_paused: boolean` — current pause flag for dashboard routing
  - `sms_opt_out_at: timestamptz | null` — when carrier/user opt-out was received
  - `not_emergency_copy_acknowledged_at: timestamptz` — when the child completed the trust disclaimer
  - `created_at: timestamptz` — creation timestamp
  - `updated_at: timestamptz` — last update timestamp
- **Relationships:** belongs to `users`; has many `message_templates`, `check_in_occurrences`, and `pause_windows`

### Entity: message_templates
- **Fields:**
  - `id: uuid` — primary key
  - `care_plan_id: uuid` — belongs to `care_plans`
  - `template_type: text` — exact values `check-in` or `reminder`
  - `preset_key: text | null` — selected preset identifier if using a preset
  - `body_text: text` — editable SMS body before the reply link is appended
  - `character_count: integer` — persisted count displayed in the editor
  - `updated_at: timestamptz` — last update timestamp
- **Relationships:** belongs to `care_plans`

### Entity: check_in_occurrences
- **Fields:**
  - `id: uuid` — primary key
  - `care_plan_id: uuid` — belongs to `care_plans`
  - `local_date: date` — scheduled date in the plan timezone
  - `scheduled_for_at: timestamptz` — exact UTC send time
  - `status: text` — exact values `Checked in`, `Reminder sent`, `Paused`, or `No response yet`
  - `check_in_sent_at: timestamptz | null` — when the first SMS send was accepted
  - `reminder_sent_at: timestamptz | null` — when the one allowed reminder was sent
  - `responded_at: timestamptz | null` — when the parent tapped `I'm okay`
  - `delivery_status: text` — Twilio delivery state shown on Screen 4, e.g. `queued`, `sent`, `delivered`, `failed`
  - `twilio_check_in_sid: text | null` — first SMS message SID
  - `twilio_reminder_sid: text | null` — reminder SMS message SID
  - `response_token_hash: text` — hashed token embedded in reply URL
  - `response_token_expires_at: timestamptz` — token expiry timestamp
  - `created_by: text` — `system` or `child_manual_send`
  - `created_at: timestamptz` — creation timestamp
- **Relationships:** belongs to `care_plans`; has many `activity_events`

### Entity: pause_windows
- **Fields:**
  - `id: uuid` — primary key
  - `care_plan_id: uuid` — belongs to `care_plans`
  - `start_date: date` — local start date of pause
  - `end_date: date` — local end date of pause
  - `reason_note: text | null` — child-only note
  - `is_active: boolean` — whether the current date is within the pause window
  - `created_at: timestamptz` — creation timestamp
  - `updated_at: timestamptz` — last update timestamp
- **Relationships:** belongs to `care_plans`

### Entity: activity_events
- **Fields:**
  - `id: uuid` — primary key
  - `check_in_occurrence_id: uuid` — belongs to `check_in_occurrences`
  - `event_type: text` — exact values `Check-in sent`, `Reminder sent`, `Checked in`, `Paused`, or `No response yet`
  - `occurred_at: timestamptz` — event timestamp
  - `metadata_json: jsonb` — small structured payload for delivery status or source
- **Relationships:** belongs to `check_in_occurrences`

## 9. Screens & Acceptance Criteria
### Screen 1: Landing / Product Promise [Required v1]
**Purpose:** Explain the product honestly and start child sign-in.
**Visual elements:** Mobile-first hero matching the prototype's calm single-column layout, product promise copy, one primary CTA, and a visible `not emergency monitoring` notice. Keep the serene visual style from the mock. Reject the mock's `14-day free trial`, `View Demo`, and `Join 2,000+ mindful families` claims.
**User actions:** Enter email, request OTP, continue after auth.
**State changes:** Creates or resumes Supabase auth session for `users.id`; redirects based on `care_plans.onboarding_state`.
**Acceptance criteria:**
- [ ] Screen 1 shows the primary CTA, core value proposition, and visible `not emergency monitoring` copy before any auth action.
- [ ] Submitting a valid email on Screen 1 creates or resumes a session for `users.id` and shows a loading state until Supabase responds.
- [ ] If no `care_plans` row exists or `care_plans.onboarding_state = setup_incomplete`, Screen 1 routes to Screen 2 after auth.
- [ ] If `care_plans.onboarding_state = active`, Screen 1 routes to Screen 7 after auth.
- [ ] Screen 1 does not display pricing, trial, social proof, or emergency-service claims rejected from the mock.
**Conditional states:** loading while OTP request is pending; auth error banner on failure; authenticated redirect without rendering extra CTA choices.

### Screen 2: Child Setup Wizard [Required v1]
**Purpose:** Collect the parent and schedule data needed to run one recurring plan.
**Visual elements:** Large labeled inputs for parent name, parent phone, relationship label, day selector, time picker, timezone display, and explanatory copy. Match the prototype's mobile card layout. Reject the mock's `Escalation Policy` block.
**User actions:** Input or edit plan fields, save draft, continue to Screen 3.
**State changes:** Creates or updates one `care_plans` row and sets `care_plans.onboarding_state = setup_incomplete` until Screen 4 succeeds.
**Acceptance criteria:**
- [ ] Screen 2 persists `care_plans.parent_name`, `care_plans.parent_phone_e164`, `care_plans.relationship_label`, `care_plans.schedule_days`, `care_plans.scheduled_time_local`, and `care_plans.timezone_iana`.
- [ ] Screen 2 blocks continue until all required fields are valid and `care_plans.parent_phone_e164` is stored in E.164 format.
- [ ] Reopening Screen 2 from Screen 7 or Screen 10 loads the current `care_plans` values for editing.
- [ ] Screen 2 contains no escalation, backup-contact, or emergency-response controls.
**Conditional states:** field validation errors for invalid or incomplete inputs; save error banner on persistence failure; loading state when existing plan values are fetched.

### Screen 3: Message Template Editor [Required v1]
**Purpose:** Let the child choose or edit the two SMS bodies used in v1.
**Visual elements:** Preset chips, editable textareas for check-in and reminder messages, live SMS preview, character counts, and note that a unique reply link is appended automatically.
**User actions:** Select preset, edit either message body, save, continue to Screen 4.
**State changes:** Creates or updates two `message_templates` rows: one with `template_type = check-in`, one with `template_type = reminder`.
**Acceptance criteria:**
- [ ] Screen 3 saves exactly one `message_templates` row for `template_type = check-in` and one row for `template_type = reminder` per `care_plans.id`.
- [ ] Editing either body updates `message_templates.body_text` and `message_templates.character_count`.
- [ ] The live preview on Screen 3 reflects the saved `message_templates.body_text` plus a visible reply-link placeholder.
- [ ] Screen 3 rejects empty message bodies and any body that exceeds the defined v1 character limit.
**Conditional states:** loading while templates load; validation errors under each textarea; save error banner if either template write fails.

### Screen 4: First Send Confirmation [Required v1]
**Purpose:** Confirm the first SMS content and send the initial check-in.
**Visual elements:** Read-only preview of the first check-in SMS, reminder preview, explanation that the parent does not install an app, primary `Send first check-in` CTA, and delivery status area.
**User actions:** Send first check-in, retry if failed, move to dashboard after success.
**State changes:** Creates `check_in_occurrences` for the current `local_date`, requests Twilio send, stores `response_token_hash`, and sets `care_plans.onboarding_state = active` on success.
**Acceptance criteria:**
- [ ] Tapping `Send first check-in` on Screen 4 creates one `check_in_occurrences` row with `created_by = child_manual_send`.
- [ ] The new `check_in_occurrences` row stores `response_token_hash`, `response_token_expires_at`, and `delivery_status`.
- [ ] On successful send, Screen 4 stores `care_plans.onboarding_state = active` and routes to Screen 7.
- [ ] If Twilio send fails, Screen 4 keeps `care_plans.onboarding_state = setup_incomplete`, shows an error state, and allows retry without creating duplicate active plans.
**Conditional states:** sending spinner with disabled CTA; send failed error banner; delivery state updates when webhook data is received.

### Screen 5: Parent Check-in Page [Required v1]
**Purpose:** Let the parent record today's reply in one tap from the SMS link.
**Visual elements:** Mobile web card, parent-facing reassurance copy, one large `I'm okay` button, and note that the child will only see today's reply status. Match the visual simplicity of the mock. Reject the mock's optional `Please call me` button.
**User actions:** Tap `I'm okay`.
**State changes:** Sets `check_in_occurrences.responded_at`, updates `check_in_occurrences.status = Checked in`, and appends an `activity_events` row with `event_type = Checked in`.
**Acceptance criteria:**
- [ ] A valid token on Screen 5 resolves exactly one `check_in_occurrences` row using `check_in_occurrences.response_token_hash`.
- [ ] Tapping `I'm okay` writes `check_in_occurrences.responded_at` and sets `check_in_occurrences.status = Checked in`.
- [ ] Screen 5 does not expose any distress, note-entry, or multi-button response path.
- [ ] After a successful write, Screen 5 routes to Screen 6.
**Conditional states:** expired or invalid token message when token lookup fails or `response_token_expires_at` has passed; duplicate-response handling when `responded_at` already exists; submit error banner on write failure.

### Screen 6: Parent Confirmation Page [Required v1]
**Purpose:** Confirm for the parent that today's reply was recorded.
**Visual elements:** Success state with timestamp, calm confirmation copy, and a short note that the page can be closed.
**User actions:** Close browser; no further required interaction.
**State changes:** None beyond reading `check_in_occurrences.responded_at`.
**Acceptance criteria:**
- [ ] Screen 6 displays the timestamp from `check_in_occurrences.responded_at` after a successful response.
- [ ] Revisiting Screen 6 for the same token shows the previously stored `check_in_occurrences.responded_at` value instead of creating another response.
- [ ] Screen 6 does not display any next-step escalation instructions.
**Conditional states:** duplicate visit copy for already-recorded replies; generic lookup error if the occurrence cannot be loaded.

### Screen 7: Child Dashboard [Required v1]
**Purpose:** Show the child today's factual status and the few allowed actions.
**Visual elements:** Status card, last reply time, today's schedule summary, `Send reminder`, `Call now`, `Pause schedule`, and recent activity list. Preserve the prototype's calm dashboard feel, but do not merge settings/history into a single overloaded screen.
**User actions:** Send reminder, call the parent, open pause flow, reopen setup for edits, optionally open history/settings if implemented.
**State changes:** `Send reminder` sets `check_in_occurrences.reminder_sent_at` and `check_in_occurrences.status = Reminder sent`; `Call now` initiates a `tel:` link only; opening pause routes to Screen 8.
**Acceptance criteria:**
- [ ] Screen 7 derives its primary status label directly from `check_in_occurrences.status` and only displays one of `Checked in`, `Reminder sent`, `Paused`, or `No response yet`.
- [ ] If `check_in_occurrences.status = No response yet`, Screen 7 enables `Send reminder` and `Call now`.
- [ ] Triggering `Send reminder` writes `check_in_occurrences.reminder_sent_at`, sets `check_in_occurrences.status = Reminder sent`, and creates an `activity_events` row with `event_type = Reminder sent`.
- [ ] If `check_in_occurrences.status = Checked in` or `Reminder sent`, Screen 7 disables `Send reminder`.
- [ ] If `care_plans.sms_opt_out_at` is not null, Screen 7 disables `Send reminder`, shows an opt-out warning, and keeps `Call now` available.
- [ ] Navigating back to Screen 7 from Screen 8, Screen 9, or Screen 10 returns the user to the same `local_date` status card they were viewing before navigation.
**Conditional states:** loading skeleton while latest occurrence resolves; empty state routing to Screen 2 if no plan exists; error banner if the status query fails; paused state when an active `pause_windows` row covers today's date.

### Screen 8: Pause Schedule [Required v1]
**Purpose:** Let the child intentionally suppress scheduled sends for a date range.
**Visual elements:** Start date, end date, optional private reason note, and preview text explaining that the dashboard will show `Paused`.
**User actions:** Create pause, edit pause, end pause early, save.
**State changes:** Creates or updates `pause_windows`; sets `care_plans.is_paused`; creates or updates today's `check_in_occurrences.status = Paused` when the current date falls in the pause window.
**Acceptance criteria:**
- [ ] Saving Screen 8 writes `pause_windows.start_date`, `pause_windows.end_date`, and optional `pause_windows.reason_note`.
- [ ] If today's date is within the saved pause range, Screen 8 causes `care_plans.is_paused = true` and `check_in_occurrences.status = Paused` for today's occurrence.
- [ ] Ending a pause early updates `pause_windows.is_active = false` and restores future occurrence generation to normal scheduling.
- [ ] Screen 8 blocks save when `pause_windows.end_date` is earlier than `pause_windows.start_date`.
**Conditional states:** edit mode with existing values; validation error for invalid date range; save error banner on write failure.

### Screen 9: Check-in History [Optional polish]
**Purpose:** Show a broader audit trail than the dashboard's recent activity list.
**Visual elements:** List or table of occurrences, status filter chips, and window switcher for 7 days or 30 days.
**User actions:** Change date window, filter by status, open previous entries.
**State changes:** Read-only query against `check_in_occurrences.status` and `activity_events`.
**Acceptance criteria:**
- [ ] Screen 9 only offers filters whose labels exactly match `check_in_occurrences.status`.
- [ ] Switching to the 7-day or 30-day view changes the query window without mutating stored plan data.
- [ ] If no rows match the current filter, Screen 9 shows an empty-history state instead of a blank list.
**Conditional states:** loading skeleton; empty-history state; query error banner.

### Screen 10: Settings / Trust & Help [Optional polish]
**Purpose:** Provide a separate place to edit plan details and repeat trust-setting copy.
**Visual elements:** Plan edit shortcuts, reminder delay control, legal/help links, and visible `not emergency monitoring` copy. Match the mock's tone and card layout. Reject the mock's multiple verified numbers and `every 12 hours` timing model.
**User actions:** Edit plan details, update reminder delay, open legal/help links.
**State changes:** Updates `care_plans.parent_phone_e164`, `care_plans.timezone_iana`, `care_plans.scheduled_time_local`, `care_plans.reminder_delay_minutes`, and `care_plans.updated_at`.
**Acceptance criteria:**
- [ ] Screen 10 edits the existing `care_plans` row rather than creating a second plan.
- [ ] Updating the reminder delay persists to `care_plans.reminder_delay_minutes`.
- [ ] Screen 10 repeats `not emergency monitoring` copy and includes links for legal/help content.
- [ ] Screen 10 does not expose multiple parent numbers, escalation settings, or additional monitoring modes.
**Conditional states:** loading while current values fetch; save success message after update; save error banner on failure.

## 10. External Integrations
### Service: Supabase Auth
- **Purpose:** Child sign-in and session management
- **Specifics:** Email OTP / magic link auth; Next.js server helpers for session reads; handle expired links and session refresh on protected routes

### Service: Supabase Postgres
- **Purpose:** Persist plans, templates, occurrences, pauses, and activity history
- **Specifics:** Row-level security by `users.id`; server-side writes for token generation and status changes; store `response_token_hash`, never raw tokens

### Service: Twilio Messaging
- **Purpose:** Send scheduled check-in SMS and one reminder SMS; receive delivery-status and opt-out webhooks
- **Specifics:** Use Twilio REST API `messages.create`; persist message SIDs; consume webhook callbacks for message status changes and STOP/START handling; avoid third-party shorteners because trust is a product risk

### Service: Vercel Cron
- **Purpose:** Trigger schedule generation and due-message sending
- **Specifics:** Run every 5 minutes; compute due sends from `care_plans.schedule_days`, `care_plans.scheduled_time_local`, `care_plans.timezone_iana`, and active `pause_windows`; must be idempotent so repeated cron runs do not create duplicate `check_in_occurrences`

## 11. Open Risks & Assumptions to Verify Before Build
- **Risk/Assumption:** Older parents will reliably tap a recurring SMS link for 10-14 days — **What to verify:** run a concierge pilot with at least 10 parent/child pairs and measure the day-14 link-tap response rate before polishing non-core screens.
- **Risk/Assumption:** SMS links will not be filtered or distrusted often enough to break the loop — **What to verify:** send Twilio messages across a small device/carrier matrix and confirm deliverability, link rendering, and spam perception.
- **Risk/Assumption:** The allowed false-negative rate is low enough that the product feels helpful, not more stressful — **What to verify:** define an acceptable missed-response threshold before coding and review pilot data against it.
- **Risk/Assumption:** Timezone, recurrence, and reminder timing can be implemented without duplicate or late sends — **What to verify:** write a pre-build test matrix covering DST changes, repeated cron execution, expired tokens, and manual reminder edge cases.
- **Risk/Assumption:** Consent and opt-out handling can stay simple in v1 — **What to verify:** confirm Twilio STOP/START behavior, confirm how future sends are suppressed via `care_plans.sms_opt_out_at`, and define the exact dashboard warning copy before implementation.

## 12. Visual Reference
- Live prototype (static mock — visual layout reference only): https://jakebuild.github.io/idea-factory/69-quietdot-is-a-fear-of-loss-app/
- Screen files on GitHub: https://github.com/jakebuild/idea-factory/tree/gh-pages/69-quietdot-is-a-fear-of-loss-app
- Source idea: https://github.com/jakebuild/idea-factory/blob/main/69-quietdot-is-a-fear-of-loss-app/idea-v3.md
- Source critique: https://github.com/jakebuild/idea-factory/blob/main/69-quietdot-is-a-fear-of-loss-app/critique-v3.md

Explicit visual adoption and rejection:
- Adopt the mock's calm, single-column mobile layout, large rounded cards, oversized primary CTA treatment, and minimal parent reply page.
- Adopt the mock's visible trust-setting copy pattern on landing and settings surfaces.
- Reject the mock's `14-day free trial`, `View Demo`, and `Join 2,000+ mindful families` marketing content.
- Reject the mock's `Escalation Policy` setup block.
- Reject the mock's implied multi-number settings and `every 12 hours` timing model.
- Reject any mock behavior that implies emergency monitoring, backup contacts, or additional parent response options beyond `I'm okay`.

## 13. Suggested Folder Structure
```text
quietdot/
  app/
    (marketing)/
      page.tsx
    (app)/
      setup/page.tsx
      messages/page.tsx
      first-send/page.tsx
      dashboard/page.tsx
      pause/page.tsx
      history/page.tsx
      settings/page.tsx
    p/[token]/
      page.tsx
      confirmed/page.tsx
    api/
      cron/send-due-checkins/route.ts
      twilio/status-webhook/route.ts
      twilio/inbound-webhook/route.ts
  components/
    marketing/
    setup/
    dashboard/
    parent-reply/
    ui/
  lib/
    auth/
    db/
    twilio/
    scheduling/
    tokens/
    validations/
  supabase/
    migrations/
  types/
    db.ts
    domain.ts
  tests/
    e2e/
    unit/
```
