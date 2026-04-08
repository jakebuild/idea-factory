# Idea #71: (Improved)
## Version: v2
**Date:** 2026-04-08 12:42
**Status:** improved
## Original Idea
CredentialCliff is a fear-of-loss app that copies the NEXT Insurance model but applies it to licensed workers at risk of losing income from expired credentials.
## What Changed and Why
- Narrowed the product from "all credentials for all licensed workers" to one wedge: independent contractors who need an active Certificate of Insurance (COI) on file to keep working. This fixes the fragmentation problem and gives the MVP one document type with a more standardized format instead of an impossible cross-profession compliance product.
- Cut automated renewal-step extraction, supporting-paperwork extraction, and any claim that the app knows upcoming shifts or job risk from external systems. The app now only stores a document, suggests an expiration date when possible, forces user confirmation, and lets the user enter their own weekly income exposure and next booked job date, which removes the biggest trust and integration failures from v1.
- Reframed retention around a weekly "Work Protected?" review instead of passive reminders alone. Users update weekly revenue exposure and next booked job date every week, so the red risk card stays current and gives them a real reason to reopen the app before the deadline panic window.
## Improved Description
CredentialCliff becomes a manual-first COI expiration tracker for independent contractors who cannot afford to show up to a client, site, or onboarding request with expired insurance proof. The user can add one COI by taking a photo or uploading a PDF, but the app treats OCR as optional convenience only. If it can detect an expiration date with reasonable confidence, it prefills a candidate value and immediately asks the user to confirm or correct it. If OCR fails, the user enters the date manually and still gets the full product value.

The core output is a blunt red card built only from confirmed data: `COI expires in 18 days`, `weekly income exposed: $1,400`, `next booked job: Apr 23`, and `next action: contact insurer / upload renewed COI`. Instead of pretending to know renewal rules, the app asks the user to save one official renewal path: insurer portal URL, broker phone number, or renewal email. That makes the next step fast without inventing compliance advice. The app also stores the current COI file, so when a renewed version arrives the user uploads the replacement and the red card clears immediately.

Why this is better now: it is no longer three brittle products pretending to be one. It is one narrow utility for one document type, with manual correction everywhere, no external schedule integrations, and no fake compliance intelligence. The value is not "AI knows your renewal requirements." The value is "I can see exactly when my insurance proof becomes dangerous to my income, and I can fix it fast."

Why would users come back in week 2? Because the app asks for a lightweight weekly review every Friday: confirm the next booked job date and this week's revenue at risk. For independent contractors, client work and exposure change week to week. That review keeps the red card accurate, and uploading a renewed COI is a clear completion moment that closes the loop.

Three biggest assumptions that could kill this:
- Contractors who rely on COIs will complete manual setup and maintain one weekly revenue-at-risk number because the red card is useful enough on day 1. `UNVERIFIED`
- COI documents are consistent enough that OCR can reduce setup time, even though manual entry remains the fallback. `UNVERIFIED`
- A weekly exposure review is enough to create week-2 retention beyond push notifications. `UNVERIFIED`

False assumption removed from v1:
- "A generic scan can reliably extract renewal steps and supporting paperwork across permits, licenses, certifications, and insurance docs." `FALSE`

Fallback plan for the unverified pieces: do not position OCR or weekly habit strength as the moat. If OCR is weak, ship manual entry plus file storage. If the weekly review does not stick, reposition the product as a low-frequency deadline utility and test whether users still convert on the strength of the red card plus reminders before expanding scope.
## Why This Is Worth Building (Solo + AI)
This version is viable for a solo builder because it drops the fake automation and reduces the product to document storage, confirmed expiration tracking, reminder scheduling, and one manually maintained risk card for a single niche. AI coding tools are useful here for fast OCR wiring, notification flows, upload handling, and UI iteration, but the app no longer depends on AI-generated compliance advice being correct.
## Solo MVP Scope
- What's in v1: one niche only (independent contractors with COIs), add COI by photo/PDF or manual entry, OCR prefill for expiration date when available, forced user confirmation/editing, manual weekly income-at-risk field, manual next booked job date, stored renewal contact/link, red risk card, file archive for current COI, reminder scheduling, weekly "Work Protected?" review prompt, and renewed-COI replacement flow.
- What's out of v1: all-credential support, renewal-step generation, supporting-paperwork extraction, calendar integrations, employer scheduling integrations, gig-platform integrations, automatic job-risk mapping, state/profession compliance database, multi-user teams, payments, marketplace features, user-generated knowledge, and any feature that depends on users returning to contribute content for others.
- Estimated build time with AI coding tools: 2.5-3.5 weeks total, including 6-8 days for app UI and core state flows, 2-3 days for file upload/storage and OCR integration setup, 2-3 days for notifications and reminder scheduling, 1-2 days for the weekly review loop, and 2-3 days for onboarding copy, error states, manual QA, and content prep for the in-app templates/help text. There is no large curated compliance database in scope, but the renewal-link guidance, sample copy, and fallback states still need to be written and tested as part of the estimate.
## Open Questions
- Which contractor wedge should launch first inside the broader COI category: cleaners, handymen, photographers, or another group with frequent proof-of-insurance requests?
- Is a user-saved insurer portal / broker contact enough as the renewal path, or do early testers still expect more guided renewal help?
- Does the weekly "Work Protected?" review produce real week-2 retention, or is this ultimately a low-frequency reminder utility with weaker monetization?
- Is OCR good enough on typical ACORD/COI documents to keep in v1 onboarding, or should manual entry be the default and OCR deferred?
- Should the MVP be mobile web first for faster shipping, or native mobile first because document capture and reminders are the real product?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / product promise — explains that CredentialCliff protects contractor income from expired COIs; key elements are one-sentence value prop, sample red card, and CTA to add a COI.
- Screen 2: Add COI method — lets the user choose photo upload, PDF upload, or manual entry; key elements are method cards, trust-setting copy about confirmation, and supported file guidance.
- Screen 3: OCR review / manual confirmation — shows extracted expiration date, policy holder, insurer name if available, and editable fields; key elements are confidence notice, confirm button, and easy correction flow.
- Screen 4: Manual COI entry — fallback form for users skipping or failing OCR; key elements are expiration date, credential label, insurer/broker contact field, official renewal link field, and upload-later option.
- Screen 5: Risk setup — captures weekly income exposed and next booked job date; key elements are dollar input, date picker, explanatory copy for how risk is calculated, and preview of the red card.
- Screen 6: Home / active risk card — main dashboard showing days until expiry, weekly income exposed, next booked job, document status, and next action; key elements are red/yellow/green state card, reminder status, and CTA to upload renewed COI.
- Screen 7: COI detail / document vault — displays the current COI file, confirmed metadata, renewal contact/link, reminder schedule, and replacement history; key elements are file preview, metadata table, and edit actions.
- Screen 8: Weekly review prompt — lightweight check-in that asks whether weekly income exposed or next booked job changed; key elements are quick-edit controls, skip option, and updated risk preview.
- Screen 9: Reminder settings — manages 30/14/7/3/1-day reminders and weekly review timing; key elements are toggles, notification timing controls, and delivery-limit disclaimer.
- Screen 10: Replace COI — upload a renewed COI and confirm the new expiration date; key elements are old vs new comparison, new risk state preview, and archive confirmation.
- Screen 11: History / cleared risks — shows previous COIs and prior red-card states for personal recordkeeping; key elements are archived documents, upload dates, old expiration dates, and cleared timestamps.
- Screen 12: Settings / support — account basics, data export/delete, help text explaining what the app does not do, and support contact; key elements are privacy copy, deletion flow, and product boundary disclaimer.
Key interactions:
- User lands on the product promise, chooses upload or manual entry, confirms the expiration date, adds weekly exposure data, and reaches the first red card in one session.
- From the home card, the user can open COI detail, edit risk inputs, change reminders, or upload a renewed COI.
- Each week the user receives a "Work Protected?" prompt, updates revenue/date inputs if work changed, and returns to the refreshed risk card.
- When a renewed COI is uploaded, the app archives the old file, updates the expiration date, and changes the home card from red/yellow to safe.
## Version History
- v1: Original idea
- v1.1: Critique - too broad, too reliant on unverified extraction and compliance logic, and too thin on trustworthy retention
- v2: Improved - narrowed to COI tracking for independent contractors with manual-first risk inputs and a weekly review loop
