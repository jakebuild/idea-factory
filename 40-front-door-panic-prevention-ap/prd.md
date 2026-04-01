# PRD: GotIt — Forgotten Items Watch List

## 1. Overview

GotIt is an iOS app that helps office commuters stop forgetting the same 1–3 items by building a personal watch list from real missed-item data. Instead of a generic pre-departure checklist, users log misses after the fact, and the app automatically surfaces targeted departure reminders only for items they have actually forgotten before. Items graduate off the watch list after 14 calendar days without a miss, so the list shrinks as users fix their weak spots. The product is entirely local — no backend, no account, no setup beyond a single departure time.

---

## 2. Target User

Office commuters with stable weekday routines who forget the same item 2–3 times per month (badge, charger, laptop). Their pain is the commute-back walk of shame or spending an entire workday without a charger. Apple Reminders and alarm labels require the user to anticipate the problem in advance; GotIt captures the failure after it happens and converts it into a forward-looking nudge for their specific weak spots, not a generic "did you grab everything?" prompt.

---

## 3. Tech Stack

- **Platform:** iOS 17+ native, iPhone only (no iPad layout required for v1)
- **Language + Framework:** Swift 5.9, SwiftUI
- **Storage:** SwiftData (iOS 17; simpler than Core Data, strong AI-tooling support)
- **Notifications:** UserNotifications framework (UNUserNotificationCenter, UNCalendarNotificationTrigger)
- **Widget:** WidgetKit + App Intents (iOS 17 interactive widget actions without opening the full app)
- **Deployment:** TestFlight → App Store; no server, no auth

**Why this stack:** Removing the backend eliminates auth, sync, and infra entirely. SwiftData + SwiftUI is the idiomatic iOS 17 stack that AI coding tools handle well. WidgetKit with App Intents (iOS 17) supports interactive widget button actions, which is the critical path for the miss-logger widget. No external packages required.

---

## 4. V1 Scope

**Required v1:**
- Miss Logger screen — free-text input + quick-pick chips for active watch-list items; "Log it" CTA
- Home screen — global "X days clean" counter, active watch-list item cards, mastered section, "I forgot something" button
- Pre-Departure Micro-Check screen — notification landing showing only active watch-list items with Yes/Skip per item
- Item Detail screen — miss history (dates only, no reasons), mastery progress bar (N of 14 clean days), edit name, remove
- Onboarding screen — single-screen departure time picker, "Done" CTA, notification permission request
- Settings screen — weekday departure time edit, notification toggle, reset mastery per item, about/feedback
- Local push notification — fires at weekday departure time, body lists active watch-list item names, deep-links to Pre-Departure Micro-Check
- Auto-add to watch list — item enters watch list when `missCount` reaches 2
- Mastery graduation — item marked mastered when 14 calendar days have elapsed since its last miss log
- Home screen widget — "X days clean" display + "I forgot…" button deep-linking to Miss Logger
- Item name deduplication — case-insensitive exact match against existing item names when logging

**Optional polish:**
- Weekend departure time — separate Sat/Sun time slot in Settings (distinct from weekday)
- Notification body personalisation — "Watch out — badge (2 misses). Got it today?" instead of a flat list

---

## 5. Out of Scope

- History screen / 30-day calendar heatmap
- NFC tag scanning or pairing
- WiFi disconnect / geofence departure detection
- Multiple named packs or item categories
- Level system, gamification badges, or streak mechanics
- Cloud backup, account creation, or device sync
- Android
- Missed-departure detection (not opening the notification does not log a miss)
- Miss reasons or notes attached to individual miss logs
- Social sharing or household shared lists
- In-app purchase or monetization

---

## 6. Screen Inventory

| # | Screen Name | Scope |
|---|---|---|
| 1 | Onboarding — Set Departure Time | Required v1 |
| 2 | Home | Required v1 |
| 3 | Miss Logger | Required v1 |
| 4 | Pre-Departure Micro-Check | Required v1 |
| 5 | Item Detail | Required v1 |
| 6 | Settings | Required v1 |

The Home Screen Widget is a WidgetKit extension (not a screen). It deep-links to Screen 3.

---

## 7. State Model

### Screen 1: Onboarding

- **First launch:** Shown when `AppSettings.onboardingComplete == false`. Time picker pre-set to 08:30 AM. "Done" always enabled.
- **Completed:** After "Done" — `AppSettings.onboardingComplete = true`, notification permission prompt fires, app navigates to Screen 2. Screen 1 not accessible again via back navigation.

### Screen 2: Home

- **Empty:** No items with `missCount >= 2`. Hero counter shows "–". Watch list section absent. Shows message: "Nothing to watch — log a miss when it happens."
- **Active items present:** At least one item with `isOnWatchList == true`. Shows global "X days clean" counter, watch-list cards (active section), optional mastered section below.
- **All mastered:** All items with `missCount >= 2` have `isMastered == true`. Watch list cards absent. Mastered section visible. Shows message: "Nothing to watch — you're done." Departure notification does not fire.
- **Global days clean counter:** `floor((now − max(Item.lastMissDate across all items)) / 86400)`. Shows "–" if no misses ever logged. Shows "0 days clean" if a miss was logged today.

### Screen 3: Miss Logger

- **Default:** Text field empty, autofocused. Quick-pick chips showing all items where `isOnWatchList == true`. "Log it" button disabled.
- **Text entered or chip tapped:** "Log it" enabled. If chip tapped, text field populated with chip label.
- **Logged:** Tap "Log it" → item created or updated, `MissLog` created, navigate back to Screen 2.
- **Deep-linked from widget:** Screen 3 opens directly with no prior Screen 2 shown.

### Screen 4: Pre-Departure Micro-Check

- **Default (opened from notification):** One card per item where `isOnWatchList == true`. Progress indicator shows "0 of N resolved."
- **Item resolved (Yes or Skip):** Card removed from session list (in-memory only). No `MissLog` created. Progress indicator updates.
- **All resolved:** "Good to go!" banner shown for 1.5 seconds, then auto-navigates to Screen 2.
- **No active items:** Notification does not fire; Screen 4 is unreachable.

### Screen 5: Item Detail

- **Default:** Item name, mastery progress bar (`cleanDays` of 14), days since last miss, miss history list (dates newest-first). Footer: "Edit name," "Remove from watch list."
- **Editing name:** Inline text field replacing the name label. "Save" / "Cancel" affordances.
- **Mastered:** Progress bar shows 14/14. Item card on Screen 2 is in the mastered section.
- **Removing:** Confirmation alert shown. On confirm: Item and all MissLogs deleted, navigate back to Screen 2.

### Screen 6: Settings

- **Default:** Weekday departure time row (with Edit), notifications toggle, items list with Reset mastery per item, About section.
- **Notification permission denied (system):** Inline banner in notifications section: "Notifications are off — enable in iOS Settings." Shown regardless of `AppSettings.notificationsEnabled` value.
- **No items:** Items section empty.

---

## 8. Data Model

### Entity: Item

| Field | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| name | String | User-provided label, stored as entered, matched case-insensitively |
| missCount | Int | Total number of associated MissLog records |
| lastMissDate | Date? | Timestamp of the most recent MissLog; nil if no misses |
| createdAt | Date | Timestamp of item creation |

**Derived (computed, not persisted):**
- `isOnWatchList`: `missCount >= 2 && !isMastered`
- `isMastered`: `lastMissDate != nil && Calendar.daysBetween(lastMissDate!, now) >= 14`
- `cleanDays`: `lastMissDate != nil ? min(Calendar.daysBetween(lastMissDate!, now), 14) : 0`

**Relationships:** Item has many MissLogs (cascade delete)

---

### Entity: MissLog

| Field | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| itemId | UUID | Foreign key to Item |
| loggedAt | Date | Timestamp when miss was logged |

---

### Entity: AppSettings (singleton, one record)

| Field | Type | Description |
|---|---|---|
| onboardingComplete | Bool | false until user taps "Done" on Screen 1 |
| weekdayDepartureHour | Int | Hour component of Mon–Fri departure time |
| weekdayDepartureMinute | Int | Minute component of Mon–Fri departure time |
| weekendDepartureHour | Int? | Hour component of Sat–Sun time; nil = no weekend notification (Optional polish) |
| weekendDepartureMinute | Int? | Minute component of Sat–Sun time; nil = no weekend notification (Optional polish) |
| notificationsEnabled | Bool | User-facing toggle; false suppresses all scheduling |

**Global derived (not persisted):**
- `globalDaysClean`: `floor((now − max(Item.lastMissDate across all items)) / 86400)`; nil if no items have ever been logged

---

## 9. Screens & Acceptance Criteria

### Screen 1: Onboarding — Set Departure Time [Required v1]

**Purpose:** Capture the user's departure time on first launch; zero other setup required.

**Visual elements:**
- Full-screen layout. Glassmorphic header (20px backdrop blur): title "Set Departure Time," subheading "We'll check in before you go."
- Scrollable time picker wheels (hour / minute / AM-PM). Default: 08:30 AM.
- Full-width teal "Done" button pinned to bottom.
- No back navigation.

**User actions:**
- Scroll time picker to select departure time.
- Tap "Done."

**State changes:**
- Stores selected hour and minute in `AppSettings.weekdayDepartureHour` / `weekdayDepartureMinute`.
- Sets `AppSettings.onboardingComplete = true`.
- Calls `UNUserNotificationCenter.requestAuthorization(options: [.alert, .sound])`.
- Navigates to Screen 2.

**Acceptance criteria:**
- [ ] Screen 1 is shown on first launch when `AppSettings.onboardingComplete == false`.
- [ ] Screen 1 is not shown on any subsequent launch when `AppSettings.onboardingComplete == true`.
- [ ] Tapping "Done" stores the selected hour and minute in `AppSettings.weekdayDepartureHour` and `AppSettings.weekdayDepartureMinute`.
- [ ] Tapping "Done" triggers the iOS notification permission system prompt.
- [ ] After "Done," the app navigates to Screen 2 and the back gesture does not return to Screen 1.
- [ ] Default time picker value is 08:30 AM on every fresh install.

**Conditional states:**
- No loading or error states. Notification permission denial is handled in Screen 6 (inline banner).

---

### Screen 2: Home [Required v1]

**Purpose:** Daily anchor showing global clean-day count, active watch-list items, and access to miss logging.

**Visual elements:**
- Glassmorphic header (20px backdrop blur).
- Hero metric: "X days clean" in large Manrope type at top.
- Watch list section: one card per item where `isOnWatchList == true`. Each card: item name, miss count label ("N misses"), days since last miss for that item.
- Mastered section (below watch list, visually muted teal): one row per item where `isMastered == true`. Item name + "Mastered" label.
- Large "I forgot something" button (teal, `error_outline` icon) pinned to bottom.
- Bottom navigation bar: Home tab (active), Settings tab.

**User actions:**
- Tap "I forgot something" → navigates to Screen 3.
- Tap any item card (active or mastered) → navigates to Screen 5 for that item.
- Tap Settings tab → navigates to Screen 6.

**State changes:** Read-only. No data mutations on Screen 2.

**Acceptance criteria:**
- [ ] The global "X days clean" hero metric shows `floor((now − max(Item.lastMissDate across all items)) / 86400)`.
- [ ] "–" is shown in the hero metric when no MissLog records exist.
- [ ] "0 days clean" is shown when a miss was logged on the current calendar day.
- [ ] Only items with `missCount >= 2 && isMastered == false` appear in the active watch list section.
- [ ] Items with `isMastered == true` appear in the mastered section with visually distinct (muted) styling.
- [ ] When no items have `missCount >= 2`, the empty state message "Nothing to watch — log a miss when it happens." is displayed and watch list section is absent.
- [ ] When all items with `missCount >= 2` have `isMastered == true`, the message "Nothing to watch — you're done." is displayed.
- [ ] Each active item card shows the item's `missCount` and `Calendar.daysBetween(item.lastMissDate, now)`.
- [ ] The "I forgot something" button is visible and tappable in all states (empty, active, all-mastered).
- [ ] Tapping an active item card navigates to Screen 5 for that item.
- [ ] Tapping a mastered item card navigates to Screen 5 for that item.

**Conditional states:**
- **Empty (no watch-list items):** Watch list section absent; empty state message shown; mastered section absent.
- **All mastered:** Active section absent; mastered section visible; "you're done" message shown.
- **Mixed:** Active cards in top section; mastered items in bottom section; hero metric shown.

---

### Screen 3: Miss Logger [Required v1]

**Purpose:** One-tap miss logging; primary entry point from the home screen widget and the "I forgot something" button.

**Visual elements:**
- Glassmorphic header: back arrow + "What did you forget?"
- Large text field, autofocused on open, placeholder: "e.g. badge, charger…"
- Quick-pick chip row (horizontal scroll): one chip per item where `isOnWatchList == true`. Tapping a chip populates the text field with the chip's item name.
- Supporting text: "Logging helps you build a smarter routine."
- Full-width teal "Log it" button (`add_circle` icon). Disabled when text field is empty.
- Bottom navigation bar: Home tab, Settings tab.

**User actions:**
- Type free-text item name in text field.
- Tap a quick-pick chip (populates text field; does not auto-submit).
- Tap "Log it."
- Tap back arrow to cancel without logging.

**State changes on "Log it":**
1. Trim whitespace from input. Apply case-insensitive exact match against all `Item.name` values.
2. If match found: use the existing Item.
3. If no match: create `Item(name: trimmedInput, missCount: 0, createdAt: now)`.
4. Create `MissLog(itemId: item.id, loggedAt: now)`.
5. Increment `item.missCount += 1`.
6. Set `item.lastMissDate = now`.
7. Call `WidgetCenter.shared.reloadAllTimelines()`.
8. Navigate back to Screen 2.

**Acceptance criteria:**
- [ ] Screen 3 opens within 2 seconds of tapping the widget "I forgot…" button on a physical device, with no authentication gate.
- [ ] The text field is autofocused (keyboard shown) on screen open.
- [ ] "Log it" is disabled when the text field is empty or contains only whitespace.
- [ ] "Log it" is enabled after tapping a quick-pick chip.
- [ ] Tapping a quick-pick chip populates the text field but does not submit the form.
- [ ] After "Log it," a MissLog record exists with `loggedAt` within 1 second of the tap timestamp.
- [ ] After "Log it," the corresponding item's `missCount` is incremented by exactly 1.
- [ ] After "Log it," the item's `lastMissDate` equals the `MissLog.loggedAt` timestamp.
- [ ] Logging "Badge" when an item named "badge" exists increments that item's `missCount`; no new Item is created.
- [ ] Logging a name with no case-insensitive match creates a new Item record.
- [ ] An item with `missCount >= 2` appears in Screen 2's active watch list immediately after navigation back.
- [ ] Quick-pick chips show only items where `isOnWatchList == true` (`missCount >= 2 && !isMastered`).

**Conditional states:**
- **No watch-list items:** Chip row absent; text field only.
- **Deep-linked from widget:** Screen 3 opens directly; navigating back goes to Screen 2.

---

### Screen 4: Pre-Departure Micro-Check [Required v1]

**Purpose:** Notification landing that surfaces only active watch-list items for a targeted "got it?" confirmation before leaving.

**Visual elements:**
- Header: reassuring copy — "Ready to go? Let's do a quick sweep."
- Progress indicator: "N of M resolved" where M = count of active watch-list items at screen open.
- One expandable card per active watch-list item: item name, "Got your [item]?" label, "Yes" button (`check_circle` icon, teal), "Skip" button (neutral).
- "Good to go!" confirmation banner on all items resolved (auto-dismisses after 1.5 seconds).
- Bottom navigation bar: Home tab, Settings tab.

**User actions:**
- Tap "Yes" on an item card → card removed from session list.
- Tap "Skip" on an item card → card removed from session list.
- Tap Home tab → exits to Screen 2 without completing the check.

**State changes:**
- "Yes" and "Skip" both remove the item from the in-session list (in-memory). No `MissLog` is created. No `Item` fields are mutated.
- When session list is empty: show "Good to go!" banner for 1.5 seconds → navigate to Screen 2.

**Acceptance criteria:**
- [ ] Screen 4 is reachable only by tapping the departure push notification; it is not directly navigable from Screen 2.
- [ ] Only items where `isOnWatchList == true` (`missCount >= 2 && isMastered == false`) appear as cards. Items not on the watch list are never shown.
- [ ] Tapping "Yes" removes the card and does not create a MissLog or modify `Item.lastMissDate`.
- [ ] Tapping "Skip" removes the card and does not create a MissLog or modify `Item.lastMissDate`.
- [ ] The progress indicator label updates after each "Yes" or "Skip" tap.
- [ ] When all cards are resolved, "Good to go!" is shown for 1.5 seconds (±200 ms) then the app navigates to Screen 2.
- [ ] When no items have `isOnWatchList == true`, no departure notification is scheduled (verifiable via `UNUserNotificationCenter.getPendingNotificationRequests` returning an empty array for the departure identifier).

**Conditional states:**
- **No active items:** Screen 4 unreachable; notification not scheduled.
- **Partially resolved:** Remaining cards visible; progress indicator updated.
- **All resolved:** "Good to go!" state → auto-dismiss to Screen 2.

---

### Screen 5: Item Detail [Required v1]

**Purpose:** Shows mastery progress and miss history for a single watch-list item; allows renaming and removal.

**Visual elements:**
- Glassmorphic header: item name + back arrow.
- Mastery progress bar: labeled "N of 14 clean days" where N = `item.cleanDays` (`min(Calendar.daysBetween(item.lastMissDate, now), 14)`).
- "Days since last miss" label: `Calendar.daysBetween(item.lastMissDate, now)` days.
- Miss history list: one row per `MissLog` for this item, sorted newest-first. Row format: "Apr 1, 2026" (date only — no reasons, no notes).
- Footer: "Edit name" button (neutral), "Remove from watch list" button (destructive, red).

**User actions:**
- Tap "Edit name" → inline text field replaces name label; "Save" / "Cancel" affordances.
- Tap "Remove from watch list" → confirmation alert ("Remove [item] from watch list? This deletes all miss history.") → on confirm, delete and navigate back.
- Tap back arrow → return to Screen 2.

**State changes:**
- "Save" on edit name: updates `Item.name`. Change is reflected immediately on Screen 2 cards.
- "Remove" confirmed: deletes `Item` record and all associated `MissLog` records via cascade delete → navigates back to Screen 2.

**Acceptance criteria:**
- [ ] Mastery progress bar shows `min(floor((now − item.lastMissDate) / 86400), 14)` out of 14.
- [ ] When `cleanDays >= 14`, progress bar shows 14/14 and the item appears in Screen 2's mastered section.
- [ ] Miss history list shows one row per `MissLog` associated with this item, sorted newest-first, formatted "MMM d, yyyy."
- [ ] No miss reasons, contextual notes, or category labels appear anywhere on Screen 5.
- [ ] Tapping "Edit name" presents a text field with the current name pre-filled.
- [ ] Saving an edited name updates `Item.name`; the new name is immediately visible on Screen 2 item cards.
- [ ] Tapping "Remove from watch list" shows a confirmation alert before taking any action.
- [ ] After removal confirmed, the item is absent from both the active and mastered sections on Screen 2.
- [ ] Navigating back from Screen 5 returns to Screen 2 at the previously viewed scroll position.

**Conditional states:**
- **Mastered:** Progress bar full (14/14); item is in Screen 2 mastered section.
- **Single miss (missCount == 1):** Item Detail is not navigable from Screen 2 (item not shown in any card). Screen 5 is only accessible for items with `missCount >= 2`.
- **No miss history (edge case):** If `Item.lastMissDate == nil`, progress bar shows 0/14 and miss history section shows "No miss history yet."

---

### Screen 6: Settings [Required v1]

**Purpose:** Configure departure time, notification preferences, and item mastery resets.

**Visual elements:**
- Grouped list layout with four sections:
  1. **Departure Times:** Weekday row showing current time (e.g., "Mon–Fri  08:30") with "Edit" chevron. Weekend row is Optional polish (see Section 4).
  2. **Notifications:** Toggle for `AppSettings.notificationsEnabled`. Inline banner "Notifications are off — enable in iOS Settings" when system authorization status is `.denied`.
  3. **Items:** One row per Item with "Reset mastery" action.
  4. **About:** App version label (e.g., "1.0.0"), feedback link (opens `mailto:` or external URL).

**User actions:**
- Tap weekday departure time row → time picker sheet → "Save."
- Toggle notifications.
- Tap "Reset mastery" for an item → confirmation alert → on confirm, reset.
- Tap feedback link → exits app to mail/browser.

**State changes:**
- Saving new weekday departure time: updates `AppSettings.weekdayDepartureHour` / `weekdayDepartureMinute`, cancels existing weekday notification, schedules new one.
- Toggling notifications off: sets `AppSettings.notificationsEnabled = false`, cancels all pending notification requests.
- Toggling notifications on: sets `AppSettings.notificationsEnabled = true`, reschedules if `watchListItems.count > 0` and permission granted.
- "Reset mastery" confirmed: sets `item.lastMissDate = Date()`, causing `cleanDays = 0` and `isMastered = false`.

**Acceptance criteria:**
- [ ] Editing weekday departure time and saving updates `AppSettings.weekdayDepartureHour` / `weekdayDepartureMinute` and reschedules the `UNCalendarNotificationTrigger` to fire at the new time.
- [ ] Toggling notifications off cancels all pending notification requests (verifiable via `UNUserNotificationCenter.getPendingNotificationRequests` returning empty array).
- [ ] Toggling notifications on reschedules the departure notification when active watch-list items exist and system authorization is granted.
- [ ] The inline notification-denied banner appears when system notification authorization status is `.denied`, regardless of the `AppSettings.notificationsEnabled` toggle state.
- [ ] "Reset mastery" shows a confirmation alert before modifying data.
- [ ] After "Reset mastery" confirmed, Screen 5 for that item shows a progress bar of 0 of 14 clean days.
- [ ] The weekday departure time displayed in Settings matches the value stored in `AppSettings.weekdayDepartureHour` / `weekdayDepartureMinute`.

**Conditional states:**
- **Notification permission denied:** Inline banner in Notifications section; toggle is still visible but cannot enable notifications until the user acts in iOS Settings.
- **No items:** Items section shows empty state or is absent.

---

## 10. External Integrations

**UserNotifications (UNUserNotificationCenter)**
- **Purpose:** Schedule the daily departure notification listing active watch-list item names.
- **Auth:** `requestAuthorization(options: [.alert, .sound])` called once on onboarding completion.
- **Scheduling:** One `UNNotificationRequest` per weekday trigger using `UNCalendarNotificationTrigger` with `dateMatching: DateComponents(hour:, minute:, weekday: 2...6)` for Mon–Fri. Notification `title`: "GotIt". `body`: comma-separated list of active watch-list item names (e.g., "badge, charger — got them?"). `userInfo["deep_link"] = "gotit://pre-departure"` to navigate to Screen 4 on tap. Rescheduled on: departure time change (Screen 6), watch list changes (new item reaches `missCount == 2`, item mastered, item removed).
- **Gotchas:** Always check `authorizationStatus` before scheduling. Cancel existing pending request with the same identifier before scheduling a replacement. On iOS 17, the notification repeats via `repeats: true`; active item list must be rebuilt at schedule time.

**WidgetKit (App Intents)**
- **Purpose:** Home screen and lock screen widget showing `globalDaysClean` and a one-tap "I forgot…" button that deep-links to Screen 3.
- **Auth:** No special entitlement beyond the WidgetKit capability.
- **Implementation:** `AppIntent` conforms to `AppIntent` protocol; `perform()` opens the app to `gotit://miss-logger`. Widget `TimelineEntry` reads from SwiftData via a shared App Group container. `WidgetCenter.shared.reloadAllTimelines()` called after every miss log and every mastery state change to keep the `globalDaysClean` count current.
- **Gotchas:** App Group entitlement must be added to both the main app target and the widget extension target. The SwiftData `ModelContainer` must be initialized with the shared App Group container URL, not the default Documents directory. Without this, the widget reads stale or empty data.

---

## 11. Open Risks & Assumptions to Verify Before Build

- **Risk: Users don't log misses after the moment of frustration has passed.** The personalization engine is empty if miss logs don't accumulate. **What to verify:** Ship a bare-bones miss logger to 5 TestFlight users in week 1; check whether at least 3 of 5 log 3+ misses within 14 days without being prompted. If they don't, the core loop fails before the watch list can build.

- **Risk: Widget tap-to-text-field takes >3 seconds on real hardware.** If the App Intent + WidgetKit path introduces a loading screen or requires biometric auth, the convenience story is broken. **What to verify:** Build the widget in Days 5–6 on a physical iPhone (not Simulator) and measure tap-to-keyboard-focus time before investing in the rest of the app UI.

- **Risk: 14-day mastery threshold breaks for non-daily routines.** Users who WFH some days or travel will find the 14 calendar-day clock unfair — it conflates calendar time with departure frequency. **What to verify:** In first TestFlight build, ask users their average weekly departure frequency. If median is <5 days/week, switch to "N clean departures" before the mastery logic is finalized. The threshold is a single constant — change it before building, not after.

- **Risk: Free-text miss logging creates duplicate items.** Case-insensitive exact match catches "Badge" == "badge" but not "badge" vs. "work badge" vs. "id badge." A fragmented watch list destroys the product's value. **What to verify:** After 14 days of TestFlight use, inspect SwiftData records manually. If >15% of items have near-duplicate names, add autocomplete (prefix match against existing names) before v1 ships.

- **Risk: Cold-start watch list leaves the app empty for the first 1–2 weeks.** A user who forgets items infrequently won't reach 2 misses quickly; the app appears broken. **What to verify:** Decide the watch-list admission threshold (1 miss vs. 2) before building the threshold logic. Changing this constant after the UI is built requires updating item card logic, notification scheduling logic, and chip display logic simultaneously. Commit to a number before Day 3.

- **Risk: Pre-departure "Yes" tapping drifts into blind compliance.** Users may dismiss the micro-check without actually checking, especially once the list is familiar. **What to verify:** This is a behavioral risk, not an engineering one. No mitigation needed before build, but do not add friction to "Yes" (e.g., confirmation dialogs) to counteract it — that makes the UX worse. Accept the risk; measure actual miss-log recurrence after reminder exposure in week 2.

---

## 12. Visual Reference

- **Live prototype (static mock — visual layout reference only):** https://jakebuild.github.io/idea-factory/40-front-door-panic-prevention-ap/
- **Screen files on GitHub:** https://github.com/jakebuild/idea-factory/tree/gh-pages/40-front-door-panic-prevention-ap
- **Source idea:** https://github.com/jakebuild/idea-factory/blob/main/40-front-door-panic-prevention-ap/idea-v3.md
- **Source critique:** https://github.com/jakebuild/idea-factory/blob/main/40-front-door-panic-prevention-ap/critique-v3.md

**Design decisions adopted from visual reference:**
- Glassmorphic header treatment (20px backdrop blur) across all screens
- Color palette: teal primary `#26667c`, off-white background `#f7f9fb`, dark text `#2c3437`, error red `#a83836`
- Typography: Manrope for headlines, Inter for body and labels
- Material Symbols Outlined icons (24px, weight 400) throughout
- Progress bar pattern for mastery on Screen 5
- Fraction display (e.g., "1 of 3 resolved") for pre-departure progress on Screen 4
- Per-day departure time rows in Settings (Mon–Fri, optional Sat/Sun)
- Bottom navigation bar (Home + Settings tabs) persistent across Screens 2–6
- Quick-pick chips in miss logger with horizontal scroll

**Design decisions rejected from visual reference (explicitly out of scope per this PRD):**
- "Apprentice" level system and gamification on Item Detail — REJECTED: critique explicitly warned against decorating a shallow loop; not in source idea
- "DEVICE TRACKING" category label on Item Detail — REJECTED: adds setup complexity not in source idea; no categories in v1 data model
- Secondary streak metrics ("12 days," "8 days") on Home — REJECTED: source specifies one global "days since last miss" counter; multiple metrics are confusing and unmaintained
- "Connected Accounts" section in Settings — REJECTED: NFC and hardware pairing are out of scope; there are no accounts to connect
- Miss history entries with contextual reasons (e.g., "Social Media Overload") — REJECTED: source specifies dates only; reason fields would require extra input, add noise, and are not in the data model
- Pre-departure check showing hardcoded generic items (Badge, Wallet, Keys) regardless of watch list state — REJECTED: source requires ONLY active watch-list items; generic checklist behavior is exactly what this product is replacing

---

## 13. Suggested Folder Structure

```
GotIt/
├── GotItApp.swift                      # App entry, SwiftData ModelContainer setup with App Group URL
├── AppDelegate.swift                   # UNUserNotificationCenterDelegate, deep-link routing
│
├── Models/
│   ├── Item.swift                      # @Model: Item entity + computed properties (isOnWatchList, isMastered, cleanDays)
│   ├── MissLog.swift                   # @Model: MissLog entity
│   └── AppSettings.swift              # @Model: singleton settings (onboardingComplete, departure times, notificationsEnabled)
│
├── Views/
│   ├── Onboarding/
│   │   └── OnboardingView.swift        # Screen 1: departure time picker + Done CTA
│   ├── Home/
│   │   ├── HomeView.swift              # Screen 2: hero metric + watch list + mastered section
│   │   └── ItemCardView.swift          # Reusable card for active and mastered items
│   ├── MissLogger/
│   │   └── MissLoggerView.swift        # Screen 3: text field + quick-pick chips + Log it CTA
│   ├── PreDeparture/
│   │   └── PreDepartureView.swift      # Screen 4: per-item Yes/Skip cards + Good to go state
│   ├── ItemDetail/
│   │   └── ItemDetailView.swift        # Screen 5: progress bar + miss history + edit/remove
│   └── Settings/
│       └── SettingsView.swift          # Screen 6: departure time, notifications toggle, reset mastery, about
│
├── Services/
│   ├── NotificationService.swift       # Schedule / cancel / reschedule UNCalendarNotificationTrigger
│   └── MissLogService.swift            # Log miss: dedup, create/update Item, create MissLog, reload widget
│
└── Extensions/
    └── Calendar+CleanDays.swift        # Calendar.daysBetween(_ from: Date, _ to: Date) -> Int

GotItWidget/
├── GotItWidgetBundle.swift             # WidgetBundle entry point
├── GotItWidget.swift                   # TimelineProvider, TimelineEntry, widget view (days clean + I forgot button)
├── MissLoggerIntent.swift              # AppIntent: opens gotit://miss-logger deep-link
└── SharedStore.swift                   # Reads SwiftData from shared App Group container URL
```
