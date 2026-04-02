# PRD: BiteBack

## 1. Overview
BiteBack is a mobile-first web app/PWA for people who keep eating or drinking after brushing their teeth at night and want a short, concrete reset instead of a forever tracker. The app guides the user through a 14-night reset around one repeat late-night culprit, stores one simple cutoff plan plus two fallback moves, and gives one low-drama response each night: rescue before the slip, recovery after the slip, or one-tap logging if they skipped it.

## 2. Target User
The v1 user is a person who already knows their late-night habit is a problem but still keeps having something after brushing: soda, candy, dessert, sports drink, or post-brushing grazing. Existing solutions fail them because they are either generic dental content, broad habit trackers, or pseudo-clinical apps that pretend to know exact risk timing. This user wants one clear next step tonight, not a dashboard or a lecture.

## 3. Tech Stack
- Platform: Web PWA
- Language + framework: TypeScript + React + Vite
- Key libraries: `react-router-dom` for screen routing, `zustand` for app state, `dexie` for IndexedDB persistence, `zod` for seeded content/schema validation, `vite-plugin-pwa` for installability, `plausible-tracker` for lightweight analytics
- Hosting / deployment: Cloudflare Pages for static hosting
- Why this stack fits the solo dev + 2–4 week constraint: the app is local-first, has no auth, no server-side product logic, and only needs fast mobile UI plus reliable browser persistence. Vite keeps setup small, Dexie gives structured local storage without backend work, and Cloudflare Pages ships a PWA with minimal ops.

## 4. V1 Scope
- **Required v1**: Landing page that explains the 14-night reset, target user, and trust framing
- **Required v1**: Onboarding flow to choose exactly one habit category from the fixed v1 category list
- **Required v1**: Initial plan builder with exactly one cutoff plan and exactly two fallback choices
- **Required v1**: Nightly check-in with exactly three actions: `I'm about to have it`, `I already had it`, `I skipped it tonight`
- **Required v1**: Category-specific rescue and recovery cards with one recommended action, one avoid-this warning, and one later-tonight reset step
- **Required v1**: 14-night progress/history view for the current reset only
- **Required v1**: One rule-based plan adjustment shown once per reset after repeated slips
- **Required v1**: Local-only persistence for the active reset, night logs, and accepted adjustment state
- **Required v1**: Plain-language Method / Trust screen with non-medical framing and support contact
- **Required v1**: Anonymous analytics for funnel and completion events
- **Optional polish**: PWA install prompt after onboarding completion

## 5. Out of Scope
- Camera scanning
- Food recognition
- Exact safe-brush times
- Free-form snack entry
- Category list beyond the fixed v1 categories
- Streaks
- Weekly summaries
- Pattern dashboards
- Notifications or reminders
- User accounts
- Cloud sync
- Social sharing
- Payments
- Apple Health integrations
- Personalized dental risk modeling
- Nightly reflection notes
- Separate settings screen

## 6. Screen Inventory
| # | Screen Name | Scope |
|---|-------------|-------|
| 1 | Landing / Value Prop | Required v1 |
| 2 | Choose Your Habit | Required v1 |
| 3 | Build My Plan | Required v1 |
| 4 | Nightly Check-In | Required v1 |
| 5 | Rescue / Recovery Card | Required v1 |
| 6 | Night Logged | Required v1 |
| 7 | Plan Adjustment | Required v1 |
| 8 | Reset Progress / History | Required v1 |
| 9 | Method / Trust | Required v1 |

## 7. State Model
### Global App States
- **Boot loading:** triggered on first app load while IndexedDB content is read and validated; shows branded loading shell; no actions available.
- **Boot ready, unauthenticated local session:** triggered when local state is valid or initialized; all v1 screens are available without login; actions depend on route.
- **Boot recovery error:** triggered when persisted state fails schema validation; shows recovery message with `Reset local data` action; action deletes invalid records and returns to Screen 1.
- **Authenticated session:** not supported in v1; no login UI exists and no screen may depend on an authenticated user.

### Screen 1: Landing / Value Prop
- **First visit:** triggered when `app_install.hasStartedReset = false`; shows headline, one-sentence value prop, three-step explanation, start CTA, trust link.
- **Return visit with active reset:** triggered when `reset.status = in_progress`; shows same header plus `Continue tonight's reset` CTA routed to Screen 4.
- **Return visit with completed reset:** triggered when `reset.status = completed` or `abandoned`; shows `Start a new 14-night reset` CTA routed to Screen 2.
- **Loading:** uses global boot loading state only.
- **Error:** uses global boot recovery error only.

### Screen 2: Choose Your Habit
- **Default selection state:** triggered after Screen 1 CTA or restart flow; shows fixed v1 category cards; primary CTA disabled until one category is selected.
- **Category selected:** triggered when `reset.habitCategoryId` is set in draft onboarding state; highlights one card and enables continue CTA.
- **Category coverage warning:** triggered when user taps `None of these fit`; shows inline message that free-form entry is out of scope for v1 and links back to Screen 1.
- **Loading:** not separate from route transition.
- **Error:** if seeded categories fail validation, show blocking error with retry and support link.

### Screen 3: Build My Plan
- **Draft plan incomplete:** triggered when cutoff plan or two fallback selections are missing; shows chosen category summary, cutoff plan selector, fallback list, trust link; continue CTA disabled.
- **Draft plan complete:** triggered when `reset.cutoffPlanId`, `reset.primaryFallbackId`, and `reset.secondaryFallbackId` are set; continue CTA enabled.
- **Edit existing plan:** triggered from Screen 8 before any new night is logged; shows current saved plan values and allows replacement.
- **Loading:** not separate from route transition.
- **Error:** if selected category content is missing its seeded cutoff or fallback options, show blocking error and route back to Screen 2.

### Screen 4: Nightly Check-In
- **Tonight not yet logged:** triggered when no `night_log` exists for `currentNightNumber`; shows day number, plan summary, and the three action buttons.
- **Tonight already logged:** triggered when a `night_log` exists for `currentNightNumber`; hides action buttons and shows `View tonight's result` CTA to Screen 6 plus `View history` CTA to Screen 8.
- **Reset complete:** triggered when `reset.completedNightCount = 14`; shows completion summary and `View reset history` CTA to Screen 8.
- **Loading:** brief route transition only.
- **Error:** if no active reset exists, redirect to Screen 1.

### Screen 5: Rescue / Recovery Card
- **Rescue state:** triggered from Screen 4 action `I'm about to have it`; shows content from `playbook_card` where `cardType = about_to`.
- **Recovery state:** triggered from Screen 4 action `I already had it`; shows content from `playbook_card` where `cardType = already_had`.
- **Ready to save:** shows one primary save CTA and one back action to Screen 4 before save.
- **Saved and locked:** after save action succeeds for tonight, route immediately to Screen 6; user cannot create a second log for the same night from this screen.
- **Loading:** if playbook content lookup is in progress, show skeleton card.
- **Error:** if no matching playbook card exists for the chosen category and action type, show blocking error and support link.

### Screen 6: Night Logged
- **Standard logged state:** triggered after Screen 5 save or Screen 4 `I skipped it tonight`; shows the most recently saved `night_log` for the active `reset`, progress through 14 nights, next route CTA, optional history CTA.
- **Adjustment pending state:** triggered when `plan_adjustment.status = pending`; shows logged confirmation plus primary CTA `Review plan change` to Screen 7.
- **Reset completed state:** triggered after saving night 14; shows completion message, final progress, and `View full reset` CTA to Screen 8.
- **Loading:** not separate from route transition.
- **Error:** if this route is opened without any `night_log` for the active `reset`, redirect to Screen 4.

### Screen 7: Plan Adjustment
- **Pending adjustment:** triggered when `plan_adjustment.status = pending`; shows what has been happening, one recommended change, `Accept change`, and `Change later`.
- **Accepted adjustment:** triggered when `plan_adjustment.status = accepted`; shows accepted state and `Back to tonight` or `Back to check-in` CTA.
- **Deferred adjustment:** triggered when `plan_adjustment.status = deferred`; shows same recommendation with `Accept change` still available until next night is logged.
- **Loading:** not separate from route transition.
- **Error:** if no pending or existing adjustment record exists, redirect to Screen 8.

### Screen 8: Reset Progress / History
- **In-progress history:** triggered when `reset.status = in_progress`; shows current night index, list of logged nights, current plan summary, and accepted adjustment summary if present.
- **Completed history:** triggered when `reset.status = completed`; shows all 14 logged nights and `Start a new reset` CTA.
- **Empty history within active reset:** triggered when reset exists but `night_log` count is `0`; shows current plan summary and message `No nights logged yet`.
- **Restart confirmation state:** triggered when user taps restart; shows confirmation modal; actions are cancel or confirm restart.
- **Loading:** not separate from route transition.
- **Error:** if no reset exists, redirect to Screen 1.

### Screen 9: Method / Trust
- **Default state:** shows what BiteBack can do, what it cannot do, non-medical framing, local-only data note, and support contact.
- **From onboarding context:** same content, opened from Screens 1 or 3, with back navigation to previous screen.
- **From intervention context:** same content, opened from Screen 5, with back navigation to the current card.
- **Loading:** static content, no loading state after app boot.
- **Error:** if static copy fails to load, show fallback plaintext copy bundled in app shell.

## 8. Data Model
### Entity: `app_install`
- **Fields:**
  - `id: string` — fixed singleton id, always `local-install`
  - `schemaVersion: number` — persisted migration version
  - `hasStartedReset: boolean` — drives Screen 1 first-visit vs return state
  - `createdAt: string` — ISO timestamp for first local install use
  - `lastOpenedAt: string` — ISO timestamp for analytics/session continuity
- **Relationships:** owns zero or one active `reset`

### Entity: `habit_category`
- **Fields:**
  - `id: enum` — one of `soda_after_brushing`, `candy_after_brushing`, `dessert_after_brushing`, `sports_drink_after_brushing`, `post_brushing_grazing`
  - `label: string` — exact UI label shown on Screen 2
  - `shortExample: string` — one-line example shown on Screen 2
  - `isActiveV1: boolean` — whether the category appears in selection UI
- **Relationships:** has many `fallback_option`; has many `playbook_card`; can be selected by many `reset` records over time

### Entity: `cutoff_plan_option`
- **Fields:**
  - `id: enum` — one of `after_dinner`, `one_hour_before_bed`, `brush_when_done_for_the_night`
  - `label: string` — exact UI label shown on Screen 3
  - `description: string` — supporting copy shown on Screen 3
  - `sortOrder: number` — stable display order
- **Relationships:** can be selected by many `reset` records

### Entity: `fallback_option`
- **Fields:**
  - `id: string` — unique option id
  - `habitCategoryId: habit_category.id` — category this option belongs to
  - `label: string` — exact UI label shown on Screen 3
  - `description: string` — supporting copy shown on Screen 3
  - `sortOrder: number` — stable display order
  - `isActiveV1: boolean` — whether the option appears in selection UI
- **Relationships:** belongs to `habit_category`; can be selected by many `reset` records

### Entity: `playbook_card`
- **Fields:**
  - `id: string` — unique card id
  - `habitCategoryId: habit_category.id` — category this card belongs to
  - `cardType: enum` — `about_to` or `already_had`
  - `recommendedAction: string` — main action text on Screen 5
  - `avoidThis: string` — warning text on Screen 5
  - `laterTonightStep: string` — reset step text on Screen 5
  - `version: number` — seeded content version for migrations
- **Relationships:** belongs to `habit_category`

### Entity: `reset`
- **Fields:**
  - `id: string` — unique reset id
  - `status: enum` — `draft`, `in_progress`, `completed`, `abandoned`
  - `habitCategoryId: habit_category.id` — selected category from Screen 2
  - `cutoffPlanId: cutoff_plan_option.id` — selected cutoff plan from Screen 3
  - `primaryFallbackId: fallback_option.id` — first chosen fallback
  - `secondaryFallbackId: fallback_option.id` — second chosen fallback
  - `startedAt: string` — ISO timestamp when reset becomes `in_progress`
  - `completedAt: string | null` — ISO timestamp when night 14 is logged
  - `abandonedAt: string | null` — ISO timestamp if replaced by a restart
  - `currentNightNumber: number` — integer `1..14`, derived for navigation but persisted for recovery
  - `completedNightCount: number` — integer `0..14`, used by Screens 4, 6, and 8
  - `slipNightCount: number` — count of nights logged as `about_to` or `already_had`
  - `adjustmentTriggeredAtNight: number | null` — night number when the one v1 adjustment was created
- **Relationships:** belongs to `app_install`; references one `habit_category`; references one `cutoff_plan_option`; references two `fallback_option`; has many `night_log`; has zero or one `plan_adjustment`

### Entity: `night_log`
- **Fields:**
  - `id: string` — unique log id
  - `resetId: reset.id` — parent reset
  - `nightNumber: number` — integer `1..14`
  - `localDate: string` — local date key `YYYY-MM-DD`
  - `outcome: enum` — `about_to`, `already_had`, `skipped`
  - `playbookCardId: playbook_card.id | null` — populated for `about_to` and `already_had`, null for `skipped`
  - `savedAt: string` — ISO timestamp when the night was logged
- **Relationships:** belongs to `reset`; optionally references one `playbook_card`

### Entity: `plan_adjustment`
- **Fields:**
  - `id: string` — unique adjustment id
  - `resetId: reset.id` — parent reset
  - `status: enum` — `pending`, `accepted`, `deferred`
  - `adjustmentType: enum` — `move_cutoff_earlier`, `swap_primary_fallback`, `delay_brushing_until_done`
  - `title: string` — exact recommendation headline shown on Screen 7
  - `body: string` — recommendation explanation shown on Screen 7
  - `triggeredAtNight: number` — night number that created the adjustment
  - `acceptedAt: string | null` — ISO timestamp if accepted
  - `deferredAt: string | null` — ISO timestamp if change later is tapped
  - `appliedCutoffPlanId: cutoff_plan_option.id | null` — new cutoff plan if accepted
  - `appliedPrimaryFallbackId: fallback_option.id | null` — new primary fallback if accepted
- **Relationships:** belongs to `reset`; may reference `cutoff_plan_option`; may reference `fallback_option`

### Entity: `analytics_event`
- **Fields:**
  - `id: string` — unique local mirror id
  - `eventName: enum` — `landing_viewed`, `reset_started`, `night_logged`, `adjustment_shown`, `adjustment_accepted`, `reset_completed`
  - `resetId: reset.id | null` — associated reset when applicable
  - `nightNumber: number | null` — associated night when applicable
  - `habitCategoryId: habit_category.id | null` — associated category when applicable
  - `occurredAt: string` — ISO timestamp
  - `sentAt: string | null` — ISO timestamp after successful send
- **Relationships:** optionally belongs to `reset`

## 9. Screens & Acceptance Criteria
### Screen 1: Landing / Value Prop [Required v1]
**Purpose:** explain the finite 14-night reset and route the user into onboarding or back into an active reset.  
**Visual elements:** mobile-first single-column layout; headline; one-sentence description; three-step explanation; primary CTA; secondary trust link; calm card-based visual treatment matching the prototype layout, but with BiteBack-specific dental copy only.  
**User actions:** tap `Start the 14-night reset`; tap `Continue tonight's reset`; tap `How BiteBack works`.  
**State changes:** starting routes to Screen 2 and sets `app_install.hasStartedReset = true`; continue routes to Screen 4; trust routes to Screen 9.  
**Acceptance criteria:**
- [ ] Screen 1 shows `Start the 14-night reset` when no `reset.status = in_progress` exists.
- [ ] Screen 1 shows `Continue tonight's reset` when a `reset` record exists with `status = in_progress`.
- [ ] Tapping the primary CTA on Screen 1 sets `app_install.hasStartedReset = true`.
- [ ] Screen 1 contains no clinical timing claims and no copy outside the chosen target user of post-brushing late-night eating/drinking.
**Conditional states:** loading uses the global boot loader; return-visit CTA changes based on `reset.status`; no standalone empty state.

### Screen 2: Choose Your Habit [Required v1]
**Purpose:** let the user select exactly one supported habit category for the reset.  
**Visual elements:** header, five category cards, short examples, disabled/enabled continue CTA, inline coverage warning link.  
**User actions:** select a category card; deselect by choosing another card; tap continue; tap `None of these fit`; tap back.  
**State changes:** selecting stores draft `reset.habitCategoryId`; continue routes to Screen 3; `None of these fit` shows inline message only and does not create a new category.  
**Acceptance criteria:**
- [ ] Screen 2 renders exactly these category labels from `habit_category.label`: `Soda after brushing`, `Candy after brushing`, `Dessert after brushing`, `Sports drink after brushing`, `Post-brushing grazing`.
- [ ] The continue CTA on Screen 2 remains disabled until one `habit_category.id` is selected.
- [ ] Selecting a new category replaces the prior draft `reset.habitCategoryId` rather than creating multiple selections.
- [ ] Tapping `None of these fit` does not create any record outside the fixed `habit_category.id` enum.
**Conditional states:** seeded-content error blocks the screen; coverage warning is inline, not a separate screen.

### Screen 3: Build My Plan [Required v1]
**Purpose:** capture one cutoff plan and two fallback choices before the reset starts.  
**Visual elements:** chosen habit summary, three cutoff options, category-specific fallback options, selection counts, continue CTA, trust link.  
**User actions:** choose one cutoff option; choose exactly two fallback options; tap continue; edit selections; open trust screen.  
**State changes:** stores `reset.cutoffPlanId`, `reset.primaryFallbackId`, and `reset.secondaryFallbackId`; continue creates or updates the `reset` record with `status = in_progress`, `startedAt`, `currentNightNumber = 1`, and `completedNightCount = 0`, then routes to Screen 4.  
**Acceptance criteria:**
- [ ] Screen 3 shows exactly these cutoff labels from `cutoff_plan_option.label`: `No more after dinner`, `Stop 1 hour before bed`, `Brush only when done for the night`.
- [ ] Screen 3 does not enable its continue CTA until one `cutoffPlanId` and exactly two distinct `fallback_option.id` values are selected.
- [ ] Continuing from Screen 3 persists one `reset` with `status = in_progress`.
- [ ] `reset.primaryFallbackId` and `reset.secondaryFallbackId` must reference two different `fallback_option.id` values.
**Conditional states:** if no category is selected, redirect to Screen 2; content error blocks progress.

### Screen 4: Nightly Check-In [Required v1]
**Purpose:** provide the nightly decision point with exactly three actions.  
**Visual elements:** day counter, current plan summary, three large action buttons, optional history link, bottom nav matching the prototype structure.  
**User actions:** tap `I'm about to have it`; tap `I already had it`; tap `I skipped it tonight`; open history.  
**State changes:** first two route to Screen 5 with action context `about_to` or `already_had`; `I skipped it tonight` creates a `night_log` with `outcome = skipped`, increments `reset.completedNightCount`, updates `reset.currentNightNumber`, and routes to Screen 6; history routes to Screen 8.  
**Acceptance criteria:**
- [ ] Screen 4 always shows exactly the three action labels defined in the source idea text and no extra nightly actions.
- [ ] Tapping `I skipped it tonight` on Screen 4 creates one `night_log` with `outcome = skipped` and `playbookCardId = null`.
- [ ] If a `night_log` already exists for `reset.currentNightNumber`, Screen 4 does not allow creating a second log for that night.
- [ ] The day counter on Screen 4 reflects `reset.currentNightNumber` and `14`.
**Conditional states:** logged-tonight state hides action buttons; completed-reset state replaces actions with completion messaging.

### Screen 5: Rescue / Recovery Card [Required v1]
**Purpose:** show the concrete next step for tonight when the user is about to slip or already slipped.  
**Visual elements:** card header, recommended action, avoid-this warning, later-tonight reset step, save CTA, trust link, back action.  
**User actions:** save tonight's card; go back before saving; open trust screen.  
**State changes:** saving creates `night_log.outcome = about_to` or `already_had`, stores `playbookCardId`, increments `reset.completedNightCount`, recalculates `reset.slipNightCount`, creates `plan_adjustment` if rule threshold is met, updates `reset.currentNightNumber`, and routes to Screen 6.  
**Acceptance criteria:**
- [ ] Screen 5 loads `playbook_card` content matching both `reset.habitCategoryId` and the selected `cardType`.
- [ ] Saving from Screen 5 creates exactly one `night_log` for the current night with `outcome` equal to the Screen 4 action that opened it.
- [ ] Saving from Screen 5 stores `night_log.playbookCardId` equal to the displayed `playbook_card.id`.
- [ ] Screen 5 must not let the user edit the `recommendedAction`, `avoidThis`, or `laterTonightStep` text in v1.
**Conditional states:** rescue and recovery are variants of the same screen; missing content is a blocking error.

### Screen 6: Night Logged [Required v1]
**Purpose:** confirm that tonight's result was saved and route the user to the next relevant step.  
**Visual elements:** confirmation headline, logged outcome, progress indicator, primary CTA, optional history link.  
**User actions:** continue to next step; open history.  
**State changes:** if `plan_adjustment.status = pending`, primary CTA routes to Screen 7; otherwise it routes to Screen 4 for the next visit context; history routes to Screen 8.  
**Acceptance criteria:**
- [ ] Screen 6 exists as a separate screen in production even though the current prototype omits it.
- [ ] Screen 6 shows the outcome label from `night_log.outcome` for the night just saved.
- [ ] When `plan_adjustment.status = pending`, Screen 6 primary CTA routes to Screen 7.
- [ ] When no pending adjustment exists, Screen 6 primary CTA does not skip confirmation and must not auto-route past the screen.
**Conditional states:** completion variant appears after night 14; if opened without a just-saved or existing current-night log, redirect to Screen 4.

### Screen 7: Plan Adjustment [Required v1]
**Purpose:** offer one rule-based plan change after repeated slips.  
**Visual elements:** what keeps happening summary, one recommended change card, accept CTA, change-later CTA.  
**User actions:** accept change; defer change; go back.  
**State changes:** accepting updates `plan_adjustment.status = accepted`, sets `acceptedAt`, applies `appliedCutoffPlanId` and/or `appliedPrimaryFallbackId` to the parent `reset`, and returns to Screen 4 or Screen 8; deferring sets `plan_adjustment.status = deferred` and `deferredAt`.  
**Acceptance criteria:**
- [ ] A `plan_adjustment` is created only once per `reset`.
- [ ] The v1 trigger rule for Screen 7 is: create `plan_adjustment` after the third `night_log` in the same `reset` where `outcome` is `about_to` or `already_had`, provided no prior `plan_adjustment` exists.
- [ ] Tapping `Accept change` updates `plan_adjustment.status` to `accepted`.
- [ ] Tapping `Change later` updates `plan_adjustment.status` to `deferred`.
**Conditional states:** accepted and deferred are states of Screen 7, not separate screens.

### Screen 8: Reset Progress / History [Required v1]
**Purpose:** show the current 14-night run without turning it into a dashboard product.  
**Visual elements:** progress summary, list of logged nights, current plan summary, accepted adjustment summary if present, restart CTA.  
**User actions:** view night outcomes; restart reset; edit plan before next log if the reset is still active.  
**State changes:** restart marks current `reset.status = abandoned`, sets `abandonedAt`, creates a new draft flow routed to Screen 2, and leaves old `night_log` records attached to the abandoned reset.  
**Acceptance criteria:**
- [ ] Screen 8 lists only `night_log` records belonging to the current or viewed `reset.id`.
- [ ] If a reset has zero `night_log` records, Screen 8 shows `No nights logged yet` and still shows the saved plan from `reset.cutoffPlanId`, `primaryFallbackId`, and `secondaryFallbackId`.
- [ ] Restarting from Screen 8 does not delete the previous `reset`; it sets that record to `status = abandoned` and creates a new draft path.
- [ ] Screen 8 contains no charts, streaks, weekly summaries, or risk scores.
**Conditional states:** empty history is inline when no nights are logged; restart uses a confirmation state within the screen.

### Screen 9: Method / Trust [Required v1]
**Purpose:** explain what the app is and is not claiming.  
**Visual elements:** plain-language sections for `What BiteBack does`, `What BiteBack does not do`, `Why the advice is fixed and rule-based`, `Your data`, and support contact.  
**User actions:** read; go back; tap support email or contact link.  
**State changes:** no mutable product data changes; optional analytics event `trust_viewed` may be sent but is not required for v1.  
**Acceptance criteria:**
- [ ] Screen 9 explicitly states that BiteBack does not provide exact safe-brush times or personalized dental risk modeling.
- [ ] Screen 9 explicitly states that v1 data is stored locally on the device/browser.
- [ ] Screen 9 is reachable from Screen 1, Screen 3, and Screen 5.
- [ ] Screen 9 has no account, consent, or notification settings in v1.
**Conditional states:** same content regardless of entry point; fallback plaintext copy is used if rich static content fails.

## 10. External Integrations
### Service: Plausible Analytics
- **Purpose:** track funnel and completion events without adding an account system or heavy analytics SDK
- **Specifics:** use the Plausible script with a public domain key; send custom events for `landing_viewed`, `reset_started`, `night_logged`, `adjustment_shown`, `adjustment_accepted`, and `reset_completed`; no user id, no PII, and no category free text. Gotcha: because state is local-only, event payloads must rely on stable enums from the schema and must tolerate duplicate sends after offline recovery.

## 11. Open Risks & Assumptions to Verify Before Build
- **Risk/Assumption:** the target user is clearer as post-brushing late-night eating/drinking, not broad dental anxiety — **What to verify:** test the landing copy and category labels with 5–10 target users and confirm they self-identify with the problem statement in under 10 seconds.
- **Risk/Assumption:** the fixed five-category list is enough for v1 without making the product feel fake — **What to verify:** run category-fit testing with real late-night scenarios and measure how often users choose `None of these fit`.
- **Risk/Assumption:** rescue and recovery cards can feel concrete without sounding clinical or preachy — **What to verify:** review each seeded `playbook_card` with target users and reject any card described as obvious, guilt-inducing, or medically sketchy.
- **Risk/Assumption:** a 14-night reset creates enough repeat use to justify an installable app — **What to verify:** define success thresholds before coding: day-2 return rate, day-7 completion rate, and day-14 completion rate.
- **Risk/Assumption:** local-only persistence is acceptable for a utility product — **What to verify:** user-test whether “data stays on this device/browser only” causes trust or continuity concerns strong enough to require export or sync later.

## 12. Visual Reference
- Live prototype (static mock — visual layout reference only): https://jakebuild.github.io/idea-factory/55-biteback-is-a-health-app-that-/
- Screen files on GitHub: https://github.com/jakebuild/idea-factory/tree/gh-pages/55-biteback-is-a-health-app-that-
- Source idea: https://github.com/jakebuild/idea-factory/blob/main/55-biteback-is-a-health-app-that-/idea-v3.md
- Source critique: https://github.com/jakebuild/idea-factory/blob/main/55-biteback-is-a-health-app-that-/critique-v3.md

Explicit source-of-truth decisions:
- Adopt from the prototype: mobile-first single-column composition, card-based action tiles, simple top header, and bottom navigation on in-reset screens.
- Reject from the prototype: off-theme “radiance/sanctuary/physiological realignment” copy, any non-dental habits, the missing `Screen 6: Night Logged`, and the `Save Reflection` behavior.
- Production requirement beats mock behavior wherever this PRD differs.

## 13. Suggested Folder Structure
```text
src/
  app/
    App.tsx
    router.tsx
    providers.tsx
  components/
    layout/
    cards/
    buttons/
    progress/
  content/
    habitCategories.ts
    cutoffPlans.ts
    fallbackOptions.ts
    playbookCards.ts
    trustCopy.ts
  features/
    landing/
    onboarding/
    checkin/
    playbook/
    night-logged/
    plan-adjustment/
    history/
    trust/
  lib/
    db/
      dexie.ts
      migrations.ts
    analytics/
      plausible.ts
    validation/
      schemas.ts
    rules/
      planAdjustment.ts
      resetProgress.ts
  store/
    useAppStore.ts
  styles/
    globals.css
    tokens.css
  types/
    models.ts
public/
  manifest.webmanifest
  icons/
```
