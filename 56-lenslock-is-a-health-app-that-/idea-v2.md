# Idea #56: (Improved)
## Version: v2
**Date:** 2026-04-03 05:19
**Status:** improved
## Original Idea
LensLock is a health app that copies the Alarmy model but applies it to contact lens anxiety instead of waking up. You stick a cheap NFC tag on your lens case once, tap when you put your lenses in, and the app starts a wear-time clock that turns hostile near bedtime if you have not tapped the case to confirm removal.
## What Changed and Why
- Replaced NFC and the "hostile red screen" with a manual bedtime closeout flow plus local reminders. This directly fixes the biggest technical and UX risk from the critique: the product no longer lives or dies on flaky tag reads or novelty nagging.
- Narrowed the audience to reusable soft-lens wearers on non-extended schedules and changed the promise from "high-irritation risk" to "you are past your planned wear window" or "you are heading to bed with lenses still in." This makes the logic understandable, safer, and more credible because it is based on user-set rules instead of fake medical precision.
- Added ongoing utility beyond one daily timer: pair-replacement countdown, case-replacement reminders, missed-log recovery, and a weekly bedtime compliance summary. This fixes the retention problem by giving users a reason to come back in week 2 even after the novelty of the timer is gone.
## Improved Description
LensLock becomes a bedtime closeout assistant for reusable soft contact lens wearers who regularly realize too late that they are still wearing lenses. The MVP is not NFC-first, not hostile, and not a pseudo-clinical risk engine. The user sets three things during onboarding: lens replacement cycle (`biweekly` or `monthly`), usual bedtime window, and their planned max daily wear window. From there, the app supports two lightweight actions: `Start wearing` when lenses go in, and `Close out tonight` when bedtime arrives. If the user forgets the morning start log, the bedtime flow still works by asking, "Are your lenses still in?" and "About what time did you put them in today?" so one missed tap does not corrupt the whole day.

The app's output is intentionally conservative and transparent. It does not claim clinical risk scores or predict tomorrow's irritation. It says things like "You are about 2 hours past your planned wear window" or "You marked that you are going to bed and your lenses are still in." The action is equally concrete: `Remove lenses now`, `Log as already removed`, or `Skip timing for today and keep your replacement schedule accurate.` This keeps the trust model honest and avoids liability-heavy language.

The real product value is a small but recurring routine for a known bad habit. By week 2, users come back for two reasons: the bedtime closeout reminder is still useful every night they wear lenses, and the app also tracks pair age and case age so they do not lose track of replacement timing. The weekly summary reinforces whether bedtime failures are improving without pretending to be a medical dashboard. If users do not care enough to return for that combined workflow, the fallback is to shrink the product further into a simple reminder utility rather than pretending the habit loop is stronger than it is.

3 biggest assumptions that could kill the product:
- VERIFIED: A solo developer can ship a single-platform MVP with manual start/stop logging, local notifications, missed-log recovery, and replacement countdowns in 2-4 weeks. The critique explicitly says this stripped-down version is feasible, while the NFC-first version is the scope lie.
- UNVERIFIED: Reusable soft-lens wearers with this bad habit will prefer a dedicated bedtime closeout tool over generic phone reminders or physical hacks like leaving the lens case on the pillow. This is the core demand risk, so it is not the moat; fallback plan is to validate quickly and kill or reposition the product as a minimal reminder utility if retention is weak.
- FALSE: NFC is required for the product to feel differentiated. The critique makes clear that NFC is the fragile gimmick, not the durable value, so it is removed from v1 entirely.

Why would users come back in week 2? Because the app is not only a one-day warning timer. It becomes their nightly closeout ritual plus their replacement tracker: tonight's reminder prevents sloppy bedtime mistakes, while the home screen also shows days left in the current pair, days since the case was changed, and a weekly count of "almost slept in lenses" nights.
## Why This Is Worth Building (Solo + AI)
This is small enough for one person to ship because the MVP is a focused workflow app with local data, a simple rules layer, and no unverified hardware dependency. AI coding tools help move fast on the UI, notifications, and state logic, while the product is more viable now because it solves an ongoing lens-management routine instead of betting everything on NFC theater.
## Solo MVP Scope
- What's in v1: onboarding for reusable soft lenses only; manual `Start wearing` and `Close out tonight` actions; bedtime local reminders; planned wear-window overage messages; missed-log recovery flow; pair replacement countdown; lens-case replacement reminder; nightly history; weekly compliance summary; trust-setting disclaimer copy; small rules/content set for messages, reminder states, and help text.
- What's out of v1: NFC under any name; hostile/shaming alerts; clinical risk scores; dailies support; extended-wear lens support; photo scanning; Apple Health integrations; social sharing; streak gamification; community features; user-generated advice; payments; cross-platform sync; wearable integrations.
- Estimated build time with AI coding tools: 2.5-3.5 weeks total, including 1-2 days to define the rule copy and replacement logic, 7-9 days for UI and app logic, 2-3 days for notification/integration setup, 1-2 days for local storage and missed-log recovery handling, 1 day for content prep of reminder states/help text/disclaimer copy, and 2-3 days for QA and iteration.
## Open Questions
- UNVERIFIED: Is the combined value proposition of bedtime closeout plus replacement tracking strong enough to beat a normal reminder app for this niche?
- Should v1 support both `biweekly` and `monthly` lenses at launch, or start with one schedule to simplify onboarding and copy?
- What reminder tone works best: calm accountability, blunt warning, or a user-selectable choice between the two?
- Will users reliably backfill an approximate insert time when they forget the morning log, or should the fallback be even simpler and skip duration math for those days?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Welcome / Positioning — explains LensLock as a bedtime closeout and replacement tracker for reusable soft-lens wearers; key elements: value prop, narrow audience callout, "not medical advice" line, primary CTA.
- Screen 2: Onboarding / Lens Setup — captures lens schedule and bedtime rules; key elements: lens type selector (`biweekly` or `monthly`), usual bedtime window, planned max wear hours, notification opt-in.
- Screen 3: Home / Today Status — main dashboard for daily use; key elements: current wear state (`not started`, `wearing`, `closed out`), start-wearing button, close-out button, pair age countdown, case age countdown, today's reminder state.
- Screen 4: Start Wearing Confirmation — ultra-fast morning action; key elements: start time, editable timestamp, current pair indicator, quick confirm CTA.
- Screen 5: Bedtime Check-In — reminder-driven nightly flow; key elements: question "Are your lenses out?", buttons for `Yes, already out`, `No, still in`, and `I forgot to log earlier`, plus bedtime context.
- Screen 6: Missed Log Recovery — fixes messy real-world behavior; key elements: approximate insert-time picker, option to skip timing and just log removal, explanation of how today's log will be handled.
- Screen 7: Overage / Action Result — shows the plain-language result of tonight's status; key elements: wear-window status, bedtime warning if lenses are still in, one clear action, optional note about why the app keeps language non-medical.
- Screen 8: History / Logbook — chronological record of wear sessions and closeouts; key elements: date, inserted time if known, closed-out status, overage flag, edit entry action.
- Screen 9: Weekly Summary — retention screen for week-2 use; key elements: number of nights closed out on time, number of near-miss nights, average wear duration when available, pair replacement countdown, one coaching insight.
- Screen 10: Replacement Tracker Detail — manages current pair and case lifecycle; key elements: start date of current pair, expected replacement date, reset pair button, case last changed date, change-case CTA.
- Screen 11: Settings / Reminder Tone / Safety — preferences and trust screen; key elements: reminder time, tone setting, notification toggle, disclaimer/help text, data reset/export placeholder if needed.
Key interactions:
- User goes from Welcome / Positioning to Onboarding / Lens Setup, then lands on Home / Today Status.
- In the morning, the user taps `Start wearing` from Home and confirms on Start Wearing Confirmation.
- At bedtime, the reminder opens Bedtime Check-In; from there the user either logs successful removal, reports lenses still in, or uses Missed Log Recovery if the day was not logged cleanly.
- Bedtime Check-In flows into Overage / Action Result, which can save the session and return the user to Home or History.
- Weekly Summary and Replacement Tracker Detail are both reachable from Home and create the main return loop after the first few days.
## Version History
- v1: Original idea
- v1.1: Critique - NFC dependence, fake medical precision, weak retention, messy real-world logging, and trust-risk language made the concept fragile
- v2: Improved - manual bedtime closeout plus replacement tracking for reusable soft-lens wearers, with honest rules and week-2 utility
