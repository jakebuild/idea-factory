# PRD: Lunch Lab

## 1. Overview

Lunch Lab is a mobile app that helps people find out which lunches wreck their afternoons. The user taps one of 8 meal type tags at lunch, receives a push notification 90 minutes later asking them to rate their energy 1–5, and after 8 completed check-ins sees a ranked summary of which meal types correlate with their best and worst energy. The app is explicitly a bounded two-week self-experiment — not a general wellness tracker. It earns credibility by showing the user their own history, not population heuristics.

---

## 2. Target User

Office workers who suspect their lunch is behind their afternoon slump but have never tracked it. They are mildly self-quantifying — willing to run a two-week experiment but not willing to log macros or sync wearables. Existing solutions (calorie trackers, food journals) fail them because they are general-purpose and require too much daily effort without delivering a personal, time-specific insight.

---

## 3. Tech Stack

**Platform:** Cross-platform iOS and Android (single codebase)

**Language + framework:** TypeScript with React Native (Expo SDK 51+)

**Key libraries:**
- `expo-sqlite` — local structured storage; SQL aggregation queries needed for pattern summary
- `expo-notifications` — push notification scheduling, cancellation, and deep-link handling
- `expo-image-picker` — optional photo attachment from camera roll (Optional polish only)
- `expo-file-system` — local photo storage (Optional polish only)
- `react-navigation` (native stack + bottom tabs) — navigation

**Hosting / deployment:** No backend. All data is local on device. Distribution via EAS Build → TestFlight (iOS) + internal track (Android) for MVP.

**Why this stack:** Expo eliminates native build configuration for a solo dev. `expo-sqlite` handles the aggregation queries the pattern summary requires. `expo-notifications` handles the scheduling, cancellation-on-complete, and deep-link flow that is the app's core mechanic. No server, no auth, no API costs. Buildable in 3 weeks.

---

## 4. V1 Scope

- **Required v1** — Onboarding (2 screens): lunch window picker, notification toggle
- **Required v1** — Meal tag picker: 8 fixed tags, single-select, tap to log
- **Required v1** — Pre-log history flag: show prior average energy for tapped tag (if data exists)
- **Required v1** — Logged Confirmation screen with scheduled check-in time displayed
- **Required v1** — 90-minute post-log push notification scheduling; cancel on check-in completion
- **Required v1** — Check-in Prompt: energy rating 1–5, "Remind me in 15 min" option
- **Required v1** — Log History: reverse-chronological feed with energy badge or Pending state
- **Required v1** — Log Detail: read-only view per log
- **Required v1** — Patterns screen, locked state: N/8 progress bar, no data preview
- **Required v1** — Patterns screen, unlocked state: ranked meal types, confidence note, one next-step suggestion; unlocks automatically at 8 completed check-ins
- **Required v1** — Missed check-in state: logs older than 4 hours with no rating are marked Missed
- **Optional polish** — Optional photo attachment (camera roll only, no API, stored locally)
- **Optional polish** — Optional pre-log and post-check-in free-text note fields
- **Optional polish** — "Remind me in 15 min" secondary action on Check-in Prompt

> **Decision on photo attachment:** The critique recommends cutting photos from v1 because they add implementation surface (permissions, file storage, UI) without strengthening the core insight loop. This PRD marks photos **Optional polish** — implement only after the core notification + check-in loop is proven stable. Do not spec photo UI in acceptance criteria.

> **Decision on free-text notes:** Same rationale — Optional polish. The note field must not block the "Log lunch" CTA, which requires only a meal tag.

---

## 5. Out of Scope

- Camera-based meal classification via any vision API
- Population-level crash heuristics or Crash Scores of any kind
- Exact slump window prediction
- Step targets
- HealthKit / Google Fit / wearable integration
- Breakfast and dinner tracking
- Pattern summary before 8 completed check-ins (no exceptions, no preview)
- Any language claiming the app "predicts" outcomes — framing is retrospective correlation only
- User accounts, cloud sync, or backend
- Social sharing
- Export / data download
- In-app purchases or paywall

---

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Onboarding — Step 1 | Required v1 |
| 2 | Onboarding — Step 2 | Required v1 |
| 3 | Home | Required v1 |
| 4 | Log Meal | Required v1 |
| 5 | Logged Confirmation | Required v1 |
| 6 | Check-in Prompt | Required v1 |
| 7 | Log Detail | Required v1 |
| 8 | Log History | Required v1 |
| 9 | Patterns — Locked | Required v1 |
| 10 | Patterns — Unlocked | Required v1 |

---

## 7. State Model

### Screen 1 — Onboarding Step 1
- **Default:** shown only on first launch when `settings.onboarding_complete = false`. Single CTA "Get started" navigates to Screen 2. "Skip" link navigates directly to Screen 3 and sets `onboarding_complete = true`.

### Screen 2 — Onboarding Step 2
- **Default:** shown after Screen 1. Lunch window time pickers pre-filled with 12:00–13:00. Notification toggle defaulting to off. "Done" saves settings and navigates to Screen 3.
- **Notification permission denied:** if OS permission is denied after toggle-on, toggle reverts to off; inline copy reads "Enable notifications in Settings to get check-in reminders."

### Screen 3 — Home
- **Empty state (0 logs):** "Log your first lunch to get started" prompt replaces the log feed. Patterns icon shows lock badge with "0/8".
- **Active state:** "Log lunch" button always visible and tappable. Recent log feed (last 5 logs) shown below. Patterns icon shows N/8 badge if `completed_check_in_count < 8`, or no badge with unlocked icon if `>= 8`.
- **Pending log visible:** a log with `status = 'pending'` shows a "Pending" badge in the feed and is tappable to navigate to Screen 6.

### Screen 4 — Log Meal
- **No tag selected:** "Log lunch" CTA is disabled (grayed, non-interactive).
- **Tag selected, no prior history:** tag highlighted, CTA enabled. No history callout shown.
- **Tag selected, prior history exists (`prior_avg` query returns count >= 1):** history callout appears below the grid immediately on tap.
- **Tag selected, exactly 1 prior check-in:** callout reads "Last time: [X]/5 — only 1 data point, not a pattern yet."
- **Tag selected, 2+ prior check-ins:** callout reads "Your last [Tag label] lunches: [X.X]/5 on average — [N] logs."

### Screen 5 — Logged Confirmation
- **Default:** shown immediately after "Log lunch" CTA on Screen 4. Shows meal tag label and scheduled check-in time. No actions except "Done" → Home.

### Screen 6 — Check-in Prompt
- **Default (notification deep-link or Pending tap):** shows meal tag from the matching log, energy rating row (1–5), "Save" CTA disabled until rating tapped.
- **Rating tapped:** "Save" CTA enables.
- **Saved:** log's `energy_rating` set, `status` → `checked-in`, notification cancelled. Navigate to Home.
- **"Remind me in 15 min" tapped:** current notification cancelled, new notification scheduled for now + 15 minutes. Screen dismisses to Home.
- **Log already checked-in (stale deep-link):** screen shows "You already rated this meal." with a "View log" link to Screen 7.

### Screen 7 — Log Detail
- **Default:** read-only. Shows all fields. If `energy_rating` is null and `status = 'missed'`: shows "No check-in — missed." If `status = 'pending'`: shows "Pending — tap to rate" link that navigates to Screen 6.

### Screen 8 — Log History
- **Empty state:** "No logs yet. Tap Log lunch on the home screen." No feed rows.
- **Active state:** reverse-chronological list. Each row shows meal tag, energy badge or "Pending" (amber) or "Missed" (gray), date.

### Screen 9 — Patterns — Locked
- **Condition:** `completed_check_in_count < 8`. Progress bar shows N/8. No data, no teaser. Copy: "Keep logging to unlock your lunch patterns."
- **Transition to unlocked:** when `completed_check_in_count` reaches 8, navigation to the Patterns tab automatically renders Screen 10 without user action.

### Screen 10 — Patterns — Unlocked
- **Condition:** `completed_check_in_count >= 8`. Shows ranked list of meal types with average energy rating. Confidence note. One suggestion block.
- **Tapping a meal type row:** navigates to a filtered view of Screen 8 showing only logs for that meal type (filter applied via nav param, not a new screen).

---

## 8. Data Model

### Entity: Log

| Field | Type | Description |
|-------|------|-------------|
| `id` | TEXT (UUID) | Primary key |
| `meal_tag` | TEXT (enum) | One of: `carb-heavy`, `protein-bowl`, `salad`, `fast-food`, `soup`, `light-snack`, `mixed-plate`, `fried-heavy` |
| `logged_at` | INTEGER (Unix ms) | Timestamp of meal log creation |
| `check_in_scheduled_at` | INTEGER (Unix ms) | `logged_at + 90 * 60 * 1000` |
| `energy_rating` | INTEGER (1–5) \| NULL | NULL until check-in completed |
| `status` | TEXT (enum) | `pending` \| `checked-in` \| `missed` |
| `notification_id` | TEXT \| NULL | Expo notification identifier; used to cancel on check-in |
| `note_pre` | TEXT \| NULL | Optional note at log time (Optional polish) |
| `note_post` | TEXT \| NULL | Optional note at check-in time (Optional polish) |
| `photo_uri` | TEXT \| NULL | Local file path to photo (Optional polish) |

**Enum values for `meal_tag` are the canonical set.** These exact values are used in DB queries, UI labels (via display map), and pattern aggregation. Display labels:

| Value | Display Label |
|-------|---------------|
| `carb-heavy` | Carb-heavy |
| `protein-bowl` | Protein bowl |
| `salad` | Salad |
| `fast-food` | Fast food |
| `soup` | Soup |
| `light-snack` | Light snack |
| `mixed-plate` | Mixed plate |
| `fried-heavy` | Fried/heavy |

**Status transition rules:**
- On creation: `status = 'pending'`
- On check-in saved: `status = 'checked-in'`, `energy_rating` set to 1–5
- On missed: `status = 'missed'` when `Date.now() > check_in_scheduled_at + 4 * 60 * 60 * 1000` AND `energy_rating IS NULL`. This is evaluated lazily on Home and Log History render; no background job required.

### Entity: Settings (single-row table or AsyncStorage key)

| Field | Type | Description |
|-------|------|-------------|
| `onboarding_complete` | BOOLEAN | False until user completes or skips onboarding |
| `lunch_window_start` | TEXT (HH:MM) | Default `'12:00'` |
| `lunch_window_end` | TEXT (HH:MM) | Default `'13:00'` |
| `notifications_enabled` | BOOLEAN | Reflects OS permission state + user toggle |

### Derived values (not stored, always computed):

- `completed_check_in_count`: `SELECT COUNT(*) FROM logs WHERE energy_rating IS NOT NULL`
- `prior_avg_for_tag(tag)`: `SELECT AVG(energy_rating), COUNT(*) FROM logs WHERE meal_tag = ? AND energy_rating IS NOT NULL`
- `pattern_summary`: `SELECT meal_tag, AVG(energy_rating) AS avg_rating, COUNT(*) AS log_count FROM logs WHERE energy_rating IS NOT NULL GROUP BY meal_tag ORDER BY avg_rating DESC`

---

## 9. Screens & Acceptance Criteria

---

### Screen 1: Onboarding — Step 1 [Required v1]

**Purpose:** Introduce the app's framing and set user expectations before any data collection.

**Visual elements:**
- Single illustration or icon (centered, upper half)
- Headline: "Find out which lunches tank your 3pm."
- Subtext: "Log your lunch. Rate your energy 90 min later. After 8 logs, see your pattern."
- Lunch-only disclaimer: "This app tracks lunch only. No breakfast, no dinner."
- Primary CTA button: "Get started"
- Secondary link: "Skip"

**User actions:**
- Tap "Get started" → navigate to Screen 2
- Tap "Skip" → set `settings.onboarding_complete = true`, navigate to Screen 3

**State changes:** None until CTA tapped.

**Acceptance criteria:**
- [ ] Screen 1 is shown only when `settings.onboarding_complete = false`
- [ ] Screen 1 is never shown again after "Get started" or "Skip" is tapped
- [ ] Tapping "Skip" sets `onboarding_complete = true` and navigates to Screen 3
- [ ] Tapping "Get started" navigates to Screen 2 without modifying any settings fields

---

### Screen 2: Onboarding — Step 2 [Required v1]

**Purpose:** Collect the lunch window and notification preference before first use.

**Visual elements:**
- Headline: "When do you usually eat lunch?"
- Two time pickers: "From" (default 12:00) and "To" (default 13:00)
- Notification toggle with label: "Remind me to check in after I log"
- Explanatory copy below toggle: "We'll send one notification ~90 min after you log a meal."
- Primary CTA: "Done"

**User actions:**
- Adjust time pickers → updates `lunch_window_start` / `lunch_window_end` in memory
- Toggle notifications on → triggers OS notification permission request
- Tap "Done" → saves all settings fields, navigates to Screen 3

**State changes:**
- If OS permission denied after toggle-on: toggle reverts to off; `notifications_enabled = false` saved; inline message shown
- "Done" always navigates to Screen 3 regardless of notification permission state

**Acceptance criteria:**
- [ ] Default lunch window values are 12:00 and 13:00 on first launch
- [ ] Tapping "Done" persists `lunch_window_start`, `lunch_window_end`, `notifications_enabled`, and `onboarding_complete = true`
- [ ] If notification permission is denied, `notifications_enabled` is saved as `false` and the toggle visually reflects off state
- [ ] User can proceed past Screen 2 with notifications off; the app functions without them (Pending logs accessible from Home feed)
- [ ] Screen 2 is only accessible from onboarding; it is not navigable from main app settings in v1

---

### Screen 3: Home [Required v1]

**Purpose:** Primary entry point — log a meal, view recent history, and access patterns.

**Visual elements:**
- Top bar: app name left, Patterns icon button top-right
- Patterns icon badge: shows "[N]/8" when `completed_check_in_count < 8`; shows unlocked icon with no count badge when `>= 8`
- Large centered "Log lunch" primary button
- Recent log feed below button (last 5 logs, reverse-chronological)
- Each feed row: meal tag display label, energy badge (colored 1–5, or "Pending" in amber, or "Missed" in gray), relative date (e.g., "Today 12:34")

**Conditional states:**
- **Empty (0 logs):** feed replaced with copy "Log your first lunch to get started." Patterns icon badge shows "0/8".
- **Pending log in feed:** row shows "Pending" badge in amber and is tappable → Screen 6
- **Missed log in feed:** row shows "Missed" badge in gray and is tappable → Screen 7

**User actions:**
- Tap "Log lunch" → Screen 4
- Tap Patterns icon → Screen 9 or Screen 10 (based on `completed_check_in_count`)
- Tap any feed row with Pending status → Screen 6 for that log
- Tap any other feed row → Screen 7 for that log

**Acceptance criteria:**
- [ ] "Log lunch" button is visible and tappable at all times on Screen 3
- [ ] Feed shows a maximum of 5 most recent logs; does not paginate (full list is Screen 8)
- [ ] Patterns badge count reflects `completed_check_in_count` accurately after each new check-in
- [ ] Patterns badge disappears (not "0/8") once `completed_check_in_count >= 8`
- [ ] A log row with `status = 'pending'` navigates to Screen 6 on tap; passes the log's `id`
- [ ] Missed state is evaluated on render: any log where `Date.now() > check_in_scheduled_at + 14400000` AND `energy_rating IS NULL` is displayed as "Missed"
- [ ] Navigating back from Screen 4, 6, or 7 returns the user to Screen 3 (not onboarding)

---

### Screen 4: Log Meal [Required v1]

**Purpose:** Select meal type and optionally add context; create the log record and schedule the check-in notification.

**Visual elements:**
- Headline: "What are you having?"
- 2×4 grid of 8 meal tag buttons, each showing an icon and the display label
- Tag buttons: unselected (default), selected (highlighted border/fill, single selection)
- History callout: appears below the grid when a tag with prior data is selected (see State Model)
- Primary CTA: "Log lunch" (disabled until a tag is selected)
- Optional polish only: "Add a note" text field, "Add a photo" link — only implement after core loop is stable

**User actions:**
- Tap a meal tag → selects it (deselects previous if any); triggers history callout query
- Tap selected tag again → no deselect; selection is sticky
- Tap "Log lunch" → creates Log record, schedules notification, navigates to Screen 5

**State changes on "Log lunch" tap:**
1. Insert new row into `logs`: `id` = new UUID, `meal_tag` = selected tag, `logged_at` = `Date.now()`, `check_in_scheduled_at` = `logged_at + 5400000`, `status = 'pending'`, `energy_rating = NULL`
2. If `notifications_enabled = true`: schedule push notification via `expo-notifications` for `check_in_scheduled_at`; store returned identifier in `logs.notification_id`
3. Navigate to Screen 5, passing the new log's `id` and `check_in_scheduled_at`

**History callout copy:**
- 1 prior check-in: "Last time: [X]/5 — only 1 data point, not a pattern yet."
- 2+ prior check-ins: "Your last [Display Label] lunches: [X.X]/5 on average — [N] logs."
- 0 prior check-ins: no callout shown

**Acceptance criteria:**
- [ ] "Log lunch" CTA is disabled (non-interactive, visually grayed) when no tag is selected
- [ ] "Log lunch" CTA enables immediately when any tag is tapped
- [ ] Only one tag can be selected at a time; tapping a second tag deselects the first
- [ ] History callout appears within the same render cycle as tag selection (synchronous local DB query)
- [ ] History callout for 1 prior check-in uses singular copy distinguishing it from a pattern
- [ ] History callout for 0 prior check-ins shows nothing (no placeholder, no empty div)
- [ ] Tapping "Log lunch" inserts exactly one new row in `logs` with `status = 'pending'`
- [ ] Notification is scheduled only if `notifications_enabled = true`; no crash or error if false
- [ ] `notification_id` is stored in the newly created log row when a notification is scheduled

---

### Screen 5: Logged Confirmation [Required v1]

**Purpose:** Confirm the log was saved and set expectation for the check-in time.

**Visual elements:**
- Meal tag display label (large, centered)
- Confirmation message: "Logged. Check back in at [HH:MM]." (time derived from `check_in_scheduled_at`)
- Primary CTA: "Done"

**User actions:**
- Tap "Done" → navigate to Screen 3

**Acceptance criteria:**
- [ ] The displayed check-in time matches `check_in_scheduled_at` (logged_at + 90 minutes) formatted as local time HH:MM
- [ ] Navigating back (hardware back / swipe) from Screen 5 goes to Screen 3, not Screen 4 (Screen 4 is popped from stack on log creation)
- [ ] No prediction copy, no heuristic copy, no energy estimate of any kind appears on this screen

---

### Screen 6: Check-in Prompt [Required v1]

**Purpose:** Capture the post-meal energy rating for a specific pending log.

**Entry points:**
- Push notification tap → deep-links to this screen with the log `id`
- Tapping a "Pending" row on Screen 3 or Screen 8

**Visual elements:**
- Meal tag display label of the logged meal (from log record)
- Question: "How's your energy right now?"
- Energy rating row: 5 tappable items labeled 1–5 with labels: 1 = "Wrecked", 2 = "Low", 3 = "Okay", 4 = "Good", 5 = "Energized"
- "Save" CTA (disabled until a rating is tapped)
- Secondary link: "Remind me in 15 min" (Optional polish)

**Conditional state — already checked-in:**
- If `logs.energy_rating IS NOT NULL` for the passed `id`: show "You already rated this meal." with a "View log" link to Screen 7. No rating UI.

**User actions:**
- Tap a rating → selects it, enables "Save"
- Tap "Save" → updates log, navigates to Screen 3
- Tap "Remind me in 15 min" (Optional polish) → reschedules notification, dismisses to Screen 3

**State changes on "Save" tap:**
1. Update `logs` row: `energy_rating = [selected]`, `status = 'checked-in'`
2. Cancel notification: call `expo-notifications.cancelScheduledNotificationAsync(logs.notification_id)` if `notification_id` is not null
3. Navigate to Screen 3

**Acceptance criteria:**
- [ ] Screen 6 is accessible from a notification deep-link by log `id`
- [ ] Screen 6 is accessible by tapping any "Pending" row on Screen 3 or Screen 8
- [ ] "Save" CTA is disabled until a rating (1–5) is tapped
- [ ] Tapping "Save" sets `logs.energy_rating` and `logs.status = 'checked-in'` for the correct log `id`
- [ ] The scheduled notification for the log is cancelled after a successful save
- [ ] If the deep-link log `id` has `energy_rating IS NOT NULL`, the rating UI is replaced with the already-checked-in state
- [ ] The meal tag label shown on Screen 6 matches `logs.meal_tag` display label for the passed log `id`
- [ ] `completed_check_in_count` (derived) increments by 1 after save; Screen 3 Patterns badge reflects this on next render

---

### Screen 7: Log Detail [Required v1]

**Purpose:** Read-only view of a single log entry.

**Visual elements:**
- Meal tag display label
- Date and time logged
- Energy rating (numeric + label) if `energy_rating IS NOT NULL`
- Status badge ("Checked in" / "Pending" / "Missed")
- Photo thumbnail if `photo_uri IS NOT NULL` (Optional polish)
- Pre-log note if `note_pre IS NOT NULL` (Optional polish)
- Post-check-in note if `note_post IS NOT NULL` (Optional polish)

**Conditional states:**
- `status = 'pending'`: shows "Pending — tap to rate" CTA link that navigates to Screen 6
- `status = 'missed'`: shows "No check-in — missed." No rating or link.
- `status = 'checked-in'`: shows energy rating with label

**User actions:**
- Tap back → returns to the screen that navigated here (Screen 3 or Screen 8)
- Tap "Pending — tap to rate" (if pending) → Screen 6

**Acceptance criteria:**
- [ ] Screen 7 is read-only; no editable fields exist
- [ ] Energy rating is not shown when `energy_rating IS NULL`
- [ ] "Pending — tap to rate" link only appears when `status = 'pending'`
- [ ] Navigating back from Screen 7 returns to the previously visited screen (Screen 3 or Screen 8), not Screen 4

---

### Screen 8: Log History [Required v1]

**Purpose:** Full reverse-chronological list of all logs.

**Visual elements:**
- List of all logs, reverse-chronological
- Each row: meal tag icon + display label, energy badge (color-coded 1–5) or "Pending" (amber) or "Missed" (gray), date string

**Conditional states:**
- **Empty:** "No logs yet. Tap Log lunch on the home screen."
- **Filtered (from Screen 10 tap):** heading shows "[Display Label] logs" and only rows for that `meal_tag` are shown

**User actions:**
- Tap any row → Screen 7
- Tap back → Screen 3 or Screen 10 (depending on entry point)

**Acceptance criteria:**
- [ ] All logs appear; Screen 3 feed shows only the last 5 while Screen 8 shows all
- [ ] Rows are ordered most-recent first by `logged_at`
- [ ] Missed state is evaluated using the same rule as Screen 3: `Date.now() > check_in_scheduled_at + 14400000 AND energy_rating IS NULL`
- [ ] When navigated from Screen 10 with a `meal_tag` filter param, only logs matching that `meal_tag` are shown
- [ ] Empty state appears when `SELECT COUNT(*) FROM logs` = 0

---

### Screen 9: Patterns — Locked [Required v1]

**Purpose:** Show check-in progress and hold the user's attention while the pattern is building.

**Condition:** rendered when `completed_check_in_count < 8`

**Visual elements:**
- Headline: "Your pattern is building"
- Progress bar and counter: "[N] of 8 check-ins complete"
- Body copy: "Keep logging to unlock your lunch patterns."
- No data preview, no partial chart, no teaser

**User actions:**
- Tap back → Screen 3

**Acceptance criteria:**
- [ ] Screen 9 is shown when `completed_check_in_count < 8`; Screen 10 is shown when `>= 8`
- [ ] The progress counter N reflects `completed_check_in_count` accurately on every render
- [ ] No meal-type averages, partial rankings, or energy data of any kind are shown on Screen 9
- [ ] When `completed_check_in_count` reaches 8 (after a save on Screen 6), navigating to the Patterns tab renders Screen 10 without requiring an app restart

---

### Screen 10: Patterns — Unlocked [Required v1]

**Purpose:** Show the user's personal lunch energy rankings and one concrete next action.

**Condition:** rendered when `completed_check_in_count >= 8`

**Visual elements:**
- Headline: "Your lunch patterns"
- Confidence note: "Based on [N] check-ins — the more you log, the clearer the picture."
- Ranked list of meal types the user has logged (only tags with at least 1 completed check-in appear); sorted by `avg_rating` descending
- Each row: tag display label, average rating (e.g., "3.9/5"), log count, mini horizontal bar scaled to 1–5
- Highlighted suggestion block (visually distinct card): "Try this tomorrow: [best meal type display label]" (best = highest `avg_rating`)
- Rows are tappable → filtered Screen 8

**User actions:**
- Tap a meal type row → Screen 8 filtered to that `meal_tag`
- Tap back → Screen 3

**Acceptance criteria:**
- [ ] Screen 10 only renders when `completed_check_in_count >= 8`
- [ ] Meal types with zero completed check-ins do not appear in the ranked list
- [ ] Average ratings are computed from `energy_rating` values only where `energy_rating IS NOT NULL`
- [ ] Suggestion block shows the `meal_tag` with the highest `avg_rating`; ties broken by highest `log_count`
- [ ] Confidence note shows the actual current `completed_check_in_count`, not a hardcoded value
- [ ] Tapping a meal type row navigates to Screen 8 with that `meal_tag` as a filter param
- [ ] The ranked list updates after each new check-in (Screen 10 re-queries on focus)

---

## 10. External Integrations

### expo-notifications
- **Purpose:** Schedule the 90-minute post-log check-in notification; cancel it when check-in is completed; reschedule +15 min on "Remind me"
- **Key calls:**
  - `scheduleNotificationAsync({ content: { title, body, data: { logId } }, trigger: { seconds: 5400 } })` → returns `notificationId`
  - `cancelScheduledNotificationAsync(notificationId)` → called on check-in save
  - `addNotificationResponseReceivedListener` → reads `data.logId` from notification response, navigates to Screen 6
- **Permission:** request via `requestPermissionsAsync()` on Screen 2 toggle-on. Handle `denied` gracefully — app must function without notifications.
- **Known gotchas:**
  - iOS requires explicit permission before scheduling; Android 13+ also requires `POST_NOTIFICATIONS` permission
  - OS focus modes and Do Not Disturb can suppress delivery silently — no programmatic detection
  - Pending notifications do not fire if app is force-quit on some Android OEMs; Pending log rows on Screen 3 serve as the fallback access path
  - Background notification handling requires `expo-notifications` in app.json `plugins` config

### expo-sqlite
- **Purpose:** Persistent local storage for all `logs` and `settings` data
- **Key calls:** `openDatabaseAsync`, `execAsync` (schema creation), `runAsync` (inserts/updates), `getFirstAsync` / `getAllAsync` (reads)
- **Known gotchas:** schema migrations must be handled manually; use a `schema_version` table or `PRAGMA user_version` to track migrations across app updates

---

## 11. Open Risks & Assumptions to Verify Before Build

- **Risk — Core behavior loop unverified:** Whether users complete 8 lunch check-ins over 2 weeks is the product's single most important assumption and it is untested. **Verify:** before writing any code, run a manual experiment — 5 people, Google Form, daily reminder, 10 days. If fewer than 3 complete 8 check-ins, the app has no viable engagement model.

- **Risk — Notification reliability on iOS/Android:** expo-notifications is not a guaranteed delivery system. OS throttling, focus modes, OEM battery optimization, and permission revocation will silently break the check-in flow for a non-trivial portion of users. **Verify:** test notification delivery in airplane mode, after force-quit, and with Do Not Disturb enabled on both platforms before declaring the feature stable. Confirm the Pending feed row on Screen 3 is a functional fallback.

- **Risk — Tag inconsistency degrades pattern quality:** users may classify the same meal as "carb-heavy" one day and "mixed-plate" the next. With only 8 check-ins required, one or two mis-tags can materially skew averages. **Verify:** in the manual experiment above, observe whether participants consistently classify the same meal type; if consistency is below ~80%, consider reducing the tag count or adding example copy to Screen 4.

- **Risk — Pre-log history flag is not behavior-changing:** the app's main differentiator is unverified. If early users report the flag feels like stating the obvious, it has no product value. **Fallback specified in idea-v3:** make the flag opt-in ("show me my history before I log") if users report it as noise. Build the flag as a conditional component, not hardcoded into Screen 4 layout, so it can be toggled off without a redesign.

- **Risk — Notification deep-link to stale or missing log:** if a user receives a check-in notification for a log that was already checked in (e.g., they checked in manually from Screen 3), the deep-link must handle the already-checked-in state gracefully. **Verify:** the already-checked-in conditional state on Screen 6 must be implemented and tested before shipping notifications.

- **Assumption — 8 check-ins is the right unlock floor:** this is a judgment call. 8 is a minimum; the pattern summary at 8 check-ins may still feel weak if the user's logs are spread across all 8 tag types. The confidence note ("Based on N check-ins — keep logging") is the mitigation. If early users feel the summary is embarrassing at 8, consider raising to 10 — but do not increase the floor before validating the completion rate risk above first.

---

## 12. Visual Reference

- **Live prototype (static mock — visual layout reference only):** https://jakebuild.github.io/idea-factory/36-meal-scanner-that-predicts-you/
- **Screen files on GitHub:** https://github.com/jakebuild/idea-factory/tree/gh-pages/36-meal-scanner-that-predicts-you
- **Source idea (v3):** https://github.com/jakebuild/idea-factory/blob/main/36-meal-scanner-that-predicts-you/idea-v3.md
- **Source critique (v3):** https://github.com/jakebuild/idea-factory/blob/main/36-meal-scanner-that-predicts-you/critique-v3.md

**Notes on the prototype:**
- The prototype is a browser-based design preview. It is the visual reference for layout, color, and component structure — not a specification for interaction behavior. Where the prototype shows behavior that conflicts with this PRD, the PRD is authoritative.
- Color palette from prototype (treat as reference, not binding): background `#0a0f1a`, primary text `#f9fafb`, accent blue `#3b82f6`, energy badge colors: green `#16a34a` (high), amber `#d97706` (pending/low), gray (missed).

---

## 13. Suggested Folder Structure

```
lunch-lab/
├── app.json                        # Expo config — plugins: expo-notifications, expo-sqlite
├── App.tsx                         # Root: NavigationContainer + initial route logic
├── src/
│   ├── db/
│   │   ├── client.ts               # openDatabaseAsync, connection singleton
│   │   ├── schema.ts               # CREATE TABLE statements, PRAGMA user_version migration
│   │   └── queries.ts              # getLogs, insertLog, updateCheckIn, getPatternSummary, getPriorAvg
│   ├── notifications/
│   │   └── service.ts              # scheduleCheckIn, cancelCheckIn, registerDeepLinkHandler
│   ├── screens/
│   │   ├── Onboarding1Screen.tsx
│   │   ├── Onboarding2Screen.tsx
│   │   ├── HomeScreen.tsx
│   │   ├── LogMealScreen.tsx
│   │   ├── LoggedConfirmationScreen.tsx
│   │   ├── CheckInScreen.tsx
│   │   ├── LogDetailScreen.tsx
│   │   ├── LogHistoryScreen.tsx
│   │   └── PatternsScreen.tsx      # renders Locked or Unlocked based on completed_check_in_count
│   ├── components/
│   │   ├── MealTagGrid.tsx         # 2×4 grid, single-select, emits selected tag
│   │   ├── HistoryCallout.tsx      # pre-log flag component
│   │   ├── EnergyBadge.tsx         # 1–5 badge + Pending + Missed variants
│   │   ├── LogRow.tsx              # reusable row for Screen 3 and Screen 8
│   │   └── PatternRow.tsx          # ranked row for Screen 10
│   ├── constants/
│   │   └── mealTags.ts             # enum values + display labels + icons
│   ├── types/
│   │   └── index.ts                # Log, Settings, MealTag types
│   └── navigation/
│       └── index.tsx               # Stack and Tab navigators + deep-link config
└── assets/
    └── icons/                      # meal tag icons (8 files)
```
