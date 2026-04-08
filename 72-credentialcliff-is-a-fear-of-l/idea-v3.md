# Idea #72: (Improved)
## Version: v3
**Date:** 2026-04-08 15:37
**Status:** improved
## Original Idea
CredentialCliff is a fear-of-loss app that copies the NEXT Insurance model but applies it to licensed workers at risk of losing income from expired credentials.
## What Changed and Why
- Narrowed v1 from a general credential tracker to one painful workflow for one user: a compliance coordinator at a small Texas nursing staffing agency preventing RN/LVN license lapses. This answers the critique that the wedge was too abstract and too broad; the new promise is specific and measurable: catch expiring licenses before they lapse.
- Removed proof upload, BLS tracking, and maintained renewal checklists from MVP. The critique called proof upload a security and trust trap, and checklist templates a maintenance debt. v1 now stores metadata only and links out to the official Texas Board of Nursing renewal page plus internal notes, which keeps scope shippable and avoids fake authority.
- Reframed the product from "a prettier dashboard" into a weekly exceptions workflow that saves coordinator time after import. Instead of asking users to babysit every record, the app highlights only licenses that are expiring soon, missing a next action, or overdue for follow-up. That directly addresses the "second spreadsheet" problem and gives users a concrete reason to return every week.
## Improved Description
CredentialCliff v3 is a weekly lapse-prevention cockpit for one compliance coordinator at a small Texas nursing staffing agency. It does one job: keep RN/LVN licenses from silently expiring across a roster of 10-100 clinicians. The coordinator imports the roster from the agency's current spreadsheet, confirms license expiration dates, and from that point forward works from an exceptions queue instead of a full spreadsheet.

The core product is not "store all compliance data." It is "show me exactly who needs attention this week." The main view groups clinicians into actionable buckets: expiring in 45 days, expiring in 21 days, expiring in 7 days, already expired, and "stalled" records where no next follow-up date or owner note exists. Each row shows clinician name, license type, expiration date, days remaining, last contact date, next follow-up date, and the next action note. The coordinator can update status, log outreach, set the next follow-up date, and mark a record cleared. The app sends one weekly digest email with the current exceptions list and optional deadline reminders for the 21-day and 7-day windows.

This is better because it stops trying to replace the whole compliance system. It replaces the ugliest, highest-stress part: the Monday-morning review where someone has to rebuild filters, scan dates, and guess what got missed. If the app works, the measurable outcome is fewer unnoticed expirations and less coordinator time spent manually triaging the roster each week.

**3 biggest assumptions that could kill the product**
- `VERIFIED`: Small nursing staffing agencies have a real weekly workflow around expiring clinician credentials. The critique explicitly validated the buyer and the weekly queue as real, not invented.
- `UNVERIFIED`: Agencies will switch from their spreadsheet to a dedicated exceptions workflow because it saves enough weekly time and prevents enough lapses. This is the core adoption risk, so it is not the moat. Fallback plan: run v1 as a concierge-backed layer on top of the existing spreadsheet, with setup help and weekly review support for one design partner before deciding whether to productize further.
- `FALSE`: Document storage and prescriptive renewal checklists are necessary to make the product valuable in v1. The critique explicitly argues they add trust and maintenance burden before proving workflow fit, so they are removed.

**Why would users come back in week 2?**
- Because the queue changes every week. New clinicians move into the 45/21/7-day windows, some records become stalled because no follow-up date was set, and the digest email brings the coordinator back to clear exceptions. The retention mechanic is a recurring operational review, not a one-time setup task.
## Why This Is Worth Building (Solo + AI)
This version is viable for a solo developer because it is mostly bounded CRUD, CSV import, reminder scheduling, and filtered queue logic. AI coding tools can accelerate the admin UI, import validation, and email workflow, while the non-code work stays small because v1 tracks one credential family and uses official links instead of maintaining compliance content.

It is more viable commercially because it now promises a hard outcome: fewer missed RN/LVN expirations and a faster weekly review. That is still unverified, but it is a sharper test than shipping another red-yellow-green board and hoping operators care.
## Solo MVP Scope
- What's in v1: single-user workspace for one compliance coordinator; clinician roster; one-time CSV import plus manual add/edit; Texas RN and LVN license tracking only; exceptions dashboard with 45/21/7-day, expired, and stalled views; notes and next-follow-up dates on each record; weekly digest email; reminder emails at 21 and 7 days; official outbound link to the Texas Board of Nursing renewal page; simple activity history for date/status/note changes.
- What's out of v1: proof upload; BLS or any non-RN/LVN credential; OCR; document parsing; maintained renewal checklists; shift-risk or staffing integrations; multi-user collaboration; recruiter/manager portals; payments; marketplace features; user-generated content; moderation; mobile app; deep integrations with agency systems.
- Estimated build time with AI coding tools: 3-4 weeks total. Roughly 3-4 days for product shaping, CSV template/sample prep, and pilot data mapping; 8-10 days for UI and core queue logic; 2-3 days for auth, reminder scheduling, and email setup; 2-3 days for import edge cases, audit history, and deploy; 3-4 days for QA with messy spreadsheets, seed data, and pilot onboarding.
## Open Questions
- Which specific pain is stronger in the first pilot: preventing license lapses, reducing weekly admin time, or creating cleaner audit evidence for outreach?
- Will one real compliance coordinator agree to run their Monday review in this tool for 2-3 weeks instead of only asking for a spreadsheet template?
- Are the 45/21/7-day windows correct for the workflow, or does the real operator use a different cadence?
- Is a single-user workflow acceptable for the first pilot if the output can be shared by email, or does the coordinator immediately need lightweight read-only visibility for an owner or recruiter?
- If switching intent stays weak, should the fallback product be a premium spreadsheet system with reminder automation instead of a standalone app?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Sign In — purpose: let the compliance coordinator access the workspace; key elements: email, password, sign-in action, password reset link; data it shows: account state only.
- Screen 2: Create Workspace / Initial Setup — purpose: create the agency workspace and define the first import path; key elements: agency name, coordinator name, timezone, upload sample CSV link, "import roster" CTA; data it shows: supported fields and setup checklist.
- Screen 3: CSV Import Wizard — purpose: bring the current spreadsheet into the app with minimal friction; key elements: file upload, column mapping, validation summary, duplicate warnings, preview table, confirm import action; data it shows: parsed clinician rows, expiration dates, missing values, invalid date formats.
- Screen 4: Exceptions Dashboard — purpose: show the weekly action queue; key elements: summary cards for 45 days, 21 days, 7 days, expired, stalled; filter bar; searchable table; quick actions to set follow-up date, add note, and mark cleared; data it shows: clinician name, license type, expiration date, days remaining, last contact date, next follow-up date, latest note snippet.
- Screen 5: Clinician Record — purpose: manage one clinician's license tracking details; key elements: clinician header, license record, status chip, expiration date, last contact date, next follow-up date, notes timeline, activity history; data it shows: all tracked metadata for that clinician's RN/LVN license.
- Screen 6: Quick Update Drawer / Modal — purpose: let the coordinator update a record without leaving the queue; key elements: status selector, date fields, note field, save action; data it shows: current record values and recent activity.
- Screen 7: Reminder Settings — purpose: control weekly digest and deadline reminder behavior; key elements: digest day selector, recipient email, 21-day toggle, 7-day toggle, test email action; data it shows: current notification settings and last send status.
- Screen 8: Weekly Digest Preview — purpose: show what the Monday email contains; key elements: grouped exceptions list, counts by bucket, link back to affected records; data it shows: live records that will appear in the next digest.
- Screen 9: Activity History — purpose: provide trust and traceability without handling documents; key elements: chronological event feed, filters by clinician and event type; data it shows: imports, date changes, status changes, note additions, reminder sends.
Key interactions:
- Coordinator signs in, creates a workspace, uploads the agency spreadsheet, maps columns, and lands on the Exceptions Dashboard with the first queue populated.
- From the Exceptions Dashboard, the coordinator opens a quick update modal or the full Clinician Record to log outreach, set the next follow-up date, and clear or escalate a record.
- Each week, the digest email pulls the coordinator back into the Exceptions Dashboard to process newly expiring or stalled licenses.
- When a record needs renewal context, the coordinator follows the outbound Texas Board of Nursing link from the clinician record and returns to update the next action internally.
## Version History
- v1: Original idea
- v2.1: Critique - still a spreadsheet replacement with extra admin work, trust-heavy proof handling, and weakly proven differentiation
- v3: Improved - narrowed to a single weekly RN/LVN lapse-prevention workflow with metadata-only tracking and an exceptions-first queue
