# Idea #72: (Improved)
## Version: v2
**Date:** 2026-04-08 15:31
**Status:** improved
## Original Idea
CredentialCliff is a fear-of-loss app that copies the NEXT Insurance model but applies it to licensed workers at risk of losing income from expired credentials.
## What Changed and Why
- Narrowed the product from "any licensed worker with any document" to a compliance triage tool for small Texas nursing staffing agencies tracking only two credential types in v1: Texas RN/LVN licenses and BLS certification. This removes the horizontal rules swamp and creates a user with a real weekly workflow instead of a one-time consumer scare moment.
- Removed automatic renewal-step extraction, OCR-led certainty, and "upcoming shifts/jobs at risk." v1 uses manual expiry entry or CSV import, optional proof upload, human confirmation, and hand-curated checklist templates with official links. This makes the product trustworthy enough to ship because it stops pretending messy documents and schedule data can be reliably parsed in 2-4 weeks.
- Shifted the retention loop from "wait until your next renewal" to an operational queue: every week the compliance coordinator gets a 30/14/7-day risk digest, sees who is blocked soon, marks renewal progress, and uploads proof when complete. That gives the user a reason to come back in week 2 even if no single clinician renews that day.
## Improved Description
CredentialCliff becomes a narrow compliance triage board for small Texas nursing staffing agencies, not a consumer scan-anything app. The first customer is an agency owner or compliance coordinator managing 10-100 clinicians. In v1, they track only Texas RN/LVN licenses and BLS certification. They add clinicians manually or import a CSV from their existing spreadsheet, confirm each expiration date, assign a status, and see a blunt risk board showing who is safe, who expires in 30 days, 14 days, or 7 days, and who is already non-compliant.

Each credential record is intentionally simple: clinician name, credential type, expiration date, proof status, and a controlled renewal checklist template for that credential. The app does not promise to infer renewal rules from documents. Instead, it gives the coordinator a single place to track progress, attach renewal proof, and follow a checklist that the builder hand-curates from official sources for this one state and these two credential types. The core output is no longer "4 shifts at risk" based on fragile integrations. It is "12 days until this clinician cannot legally work under your policy" plus the exact next admin action.

This is better because it sells operational clarity, not fake certainty. The user does not need to trust a black-box scan on day 1. They only need to trust a dashboard that reflects dates they confirmed themselves. That is a much lower trust bar and a much more realistic solo-built product.

**3 biggest assumptions that could kill the product**
- `VERIFIED`: Expired credentials create real income and staffing pain. The critique explicitly confirms the pain is real and stronger than generic productivity.
- `UNVERIFIED`: Small nursing staffing agencies will adopt a dedicated triage board instead of staying with spreadsheets and calendar reminders. This is an adoption risk, not the moat. Fallback plan: start as a concierge spreadsheet-import service for 1-3 pilot agencies and only productize the screens they actually use weekly.
- `FALSE`: A solo-built v1 can safely automate renewal-step extraction and document interpretation across messy credential files. This was the structural flaw in v1 and is removed from MVP scope.

**Why would users come back in week 2?**
- Because the real user is not an individual worker waiting months for one renewal. It is a staffing/compliance operator with a living roster. Every week the app surfaces a new 30/14/7-day risk queue, sends a digest email, and tracks which clinicians still need proof. The retention mechanic is the weekly compliance review, not habit for habit's sake.
## Why This Is Worth Building (Solo + AI)
This version is viable for a solo developer because it swaps hard problems that fail silently, like OCR accuracy and schedule integrations, for straightforward CRUD, reminders, CSV import, and a tiny rules template set. AI coding tools are well-suited to shipping the dashboard, import flow, notification logic, and file handling quickly, while the content-prep work stays bounded to one state and two credential templates.

It also has a clearer buyer and a stronger wedge: a small staffing agency already feels the cost of a lapsed credential across multiple clinicians, so the product can earn weekly usage without pretending to be a broad compliance platform.
## Solo MVP Scope
- What's in v1: email/password auth; one workspace for one agency; clinician roster; manual add and CSV import; two credential types only (Texas RN/LVN license and BLS); expiration countdown board with 30/14/7-day states; optional proof upload; hand-curated checklist templates with official source links; weekly digest email plus deadline reminders; audit notes on each credential.
- What's out of v1: OCR document parsing; automatic renewal-step extraction; any shift/calendar/employer integration; support for multiple states or professions; billing; team permissions beyond one admin login; mobile app; user-generated checklist content; marketplace, payouts, or worker-to-worker features.
- Estimated build time with AI coding tools: 12-16 working days total. Roughly 2 days for content prep and official-source checklist drafting, 7-9 days for UI and core app logic, 1-2 days for file storage and reminder setup, and 2-3 days for QA, seed data, deploy, and pilot-ready polish.
## Open Questions
- Can 3-5 small Texas nursing staffing agencies confirm that weekly compliance review is painful enough to switch from spreadsheets?
- Which two credential types are most operationally painful in practice: Texas RN/LVN plus BLS, or a different pair the target agencies already track manually?
- Is optional proof upload necessary in week 1 of a pilot, or is metadata-only tracking enough to get adoption and reduce trust friction?
- Should the first distribution path be direct outbound to agency owners, or a concierge service offered to one staffing operator before packaging it as software?
- If checklist maintenance proves noisy even within one state, should v1.1 reduce templates to source links plus internal notes instead of prescriptive steps?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Sign In / Create Workspace — purpose: let a single agency admin create an account and enter agency name; key elements: email, password, agency name, sign-in and create-account actions; data it shows: none beyond account state.
- Screen 2: Onboarding / Import Choice — purpose: get the first roster into the system fast; key elements: choice between manual add and CSV import, sample CSV download, short explanation of supported credential types; data it shows: supported credential templates and setup progress.
- Screen 3: CSV Import Wizard — purpose: upload a spreadsheet and map columns into clinician + credential records; key elements: file upload, column mapping, preview table, validation errors, confirm import; data it shows: parsed rows, missing fields, duplicate warnings.
- Screen 4: Risk Dashboard — purpose: show the live compliance queue; key elements: summary cards for safe / 30 days / 14 days / 7 days / expired, searchable table of clinicians, filters by credential type and status, next-action column; data it shows: clinician name, credential type, expiration date, days remaining, proof status, note count.
- Screen 5: Clinician Detail — purpose: manage one clinician's credentials in one place; key elements: profile header, list of tracked credentials, status chips, recent notes, add credential action; data it shows: clinician name, role, credentials, expiration dates, reminder history.
- Screen 6: Credential Detail / Edit — purpose: confirm dates, update status, and progress renewal work; key elements: credential type, expiration date field, proof status, note timeline, reminder toggles, save action; data it shows: current credential metadata and history.
- Screen 7: Checklist Template View — purpose: show the controlled renewal checklist for the selected credential type; key elements: step list, official source links, disclaimer, mark-step-complete controls; data it shows: curated instructions for Texas RN/LVN or BLS renewal.
- Screen 8: Proof Upload View — purpose: attach and review renewal proof for a credential; key elements: file upload, uploaded file list, replace/remove action, proof status control; data it shows: filenames, upload timestamps, linked credential.
- Screen 9: Notifications Settings — purpose: configure reminder timing and weekly digest recipients; key elements: 30/14/7-day toggles, weekly digest day selector, recipient email field, test email action; data it shows: current reminder rules and send status.
- Screen 10: Activity Log — purpose: provide a lightweight audit trail for trust and follow-up; key elements: chronological event list, filters by clinician and action type; data it shows: imports, date edits, reminder sends, proof uploads, note updates.
Key interactions:
- Admin creates a workspace, chooses manual entry or CSV import, and gets to the Risk Dashboard with the first roster loaded.
- From the Risk Dashboard, admin opens a Clinician Detail page, edits a credential, confirms the expiration date, and assigns proof status.
- From a credential, admin opens the Checklist Template View, works through the renewal steps, uploads proof, and returns the credential to a safe state.
- Each week, the digest email drives the admin back into the Risk Dashboard to review newly at-risk clinicians and clear the queue.
## Version History
- v1: Original idea
- v1.1: Critique - broad, trust-sensitive scan-anything concept with unverified rules extraction and weak retention
- v2: Improved - narrow staffing-agency compliance board with manual verification, fixed scope, and weekly retention loop
