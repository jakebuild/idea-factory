# Idea #71: (Improved)
## Version: v3
**Date:** 2026-04-08 12:51
**Status:** improved
## Original Idea
CredentialCliff is a fear-of-loss app that copies the NEXT Insurance model but applies it to licensed workers at risk of losing income from expired credentials.
## What Changed and Why
- Narrowed the wedge from generic "independent contractors" to handymen who subcontract for small property managers. This fixes the biggest market-shape problem: these users get repeated COI requests, face immediate work stoppage if their proof is stale, and can be reached with a much clearer message than a broad contractor bucket.
- Removed the two structurally bad inputs from v2: weekly income exposure and next booked job date. They drift too fast, make the red card dishonest, and create setup friction before value. The app now shows only confirmed status data: current COI, expiration date, renewal contact, and whether the document is ready to resend.
- Cut OCR, weekly review, and history from MVP. Manual entry is the default, because it is faster to ship, easier to trust, and avoids building the product around unverified convenience features. In their place, v1 adds one stronger repeat job: when a property manager asks for proof of insurance, the user can open the app and resend the current COI in seconds.
## Improved Description
CredentialCliff becomes a mobile-first COI readiness utility for solo handymen who work with property managers and get asked, over and over, to send current proof of insurance before a job, vendor setup, or site approval. The product is not pitched as a new contractor operating system. It does one job: keep the latest COI easy to resend and hard to let expire.

The setup is deliberately short. The user uploads their current COI, manually confirms the expiration date, saves a broker or insurer contact, and generates renewal reminders through a calendar export flow. The home screen then shows a blunt status card: `Active until May 28`, `Expires in 20 days`, `Broker: Sam at ABC Insurance`, and `Send current COI`. No weekly income math. No next-job prediction. No fake compliance intelligence.

The repeat use case is practical, not aspirational. When a property manager texts, emails, or calls asking for an updated COI, the user opens the app, sees whether the document is current, and resends the latest file immediately. That is the week-2 return reason. Renewal reminders matter, but they are not the retention thesis by themselves. The product is useful in the middle of active work, not only near the expiration date.

The red/yellow status card stays because it is the strongest piece of the concept, but it is now tied only to stable facts the app can actually know: current document on file, confirmed expiration date, and renewal contact path. That makes the warning credible instead of theatrical.

The initial distribution wedge is specific: handymen who subcontract for small property managers. If early testing shows that this segment does not reopen the app to resend COIs, the fallback is not to widen the audience. The fallback is to reduce the product further into a lightweight send-and-remind utility and test it as a feature, not a standalone business.

Three biggest assumptions that could kill the product:
- Handymen working with property managers are asked to resend COIs often enough that "current COI ready to send" creates repeat usage beyond renewal season. `UNVERIFIED`
- Manual setup stays low-friction enough when it is only upload + expiration date + broker contact + reminder export. `VERIFIED`
- OCR meaningfully reduces setup time for COIs. `FALSE`

Fallback plan for the unverified differentiator:
- Do not present repeat resend behavior as a moat. It is an open risk. If users only care near expiration, reposition the product as a low-frequency renewal utility or bundle the feature into a broader contractor admin workflow later.

Why would users come back in week 2?
- Because the product doubles as the fastest place to check "is my COI current?" and resend it when a property manager asks for proof before a job. If that behavior does not appear in testing, confidence in the standalone app drops sharply.
## Why This Is Worth Building (Solo + AI)
This version is viable for a solo builder because it cuts every feature that depends on stale manual data, unreliable OCR, or a fake habit loop and reduces the app to one narrow document workflow. AI coding tools help compress the UI, file handling, reminder export, and sharing flows, but the product no longer depends on AI being right about compliance or extraction.
## Solo MVP Scope
- What's in v1: one user type only (solo handymen subcontracting for property managers), one document type only (current COI), manual upload plus manual expiration-date confirmation, broker/insurer contact storage, status card, resend/share flow for the latest COI, replace-current-COI flow, calendar-based reminder export, and plain-language boundary copy saying the app does not provide legal or compliance advice.
- What's out of v1: all non-COI credentials, all other contractor categories, weekly income exposure, next booked job date, weekly review prompts, OCR, archive/history, compliance-rule databases, renewal-step generation, employer or platform integrations, push notification systems, SMS, marketplace features, payments, payouts, user-generated content, moderation, team accounts, and any feature that depends on users returning to contribute data.
- Estimated build time with AI coding tools: 2.5-3.5 weeks total, including 5-7 days for UI and core flows, 2-3 days for secure file upload/storage and replacement handling, 2 days for share/resend and calendar export integration setup, 2-3 days for empty states, reminder copy, legal-boundary copy, and onboarding content prep, and 3-4 days for mobile QA, file edge cases, permission friction, and deployment cleanup. This estimate includes UI build, integration setup, and the manual content-prep work for reminder text, help copy, and property-manager-focused onboarding.
## Open Questions
- Do handymen serving small property managers actually get enough repeated COI requests to justify a dedicated tool, or is this really just a reminder feature inside a broader admin app?
- Is calendar export enough as the MVP reminder channel, or do testers treat it as too manual compared with native push?
- Will users trust a dedicated resend flow more than just keeping the PDF in email or phone files?
- Is the first test best run as a free utility to validate repeat use, or as a cheap annual product once reminder reliability is proven?
## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / wedge-specific pitch — explains that the app helps handymen keep a current COI ready for property manager requests; key elements are headline, short proof-of-pain copy, sample red/yellow/green status card, and CTA to add current COI.
- Screen 2: Add current COI — lets the user upload a PDF or photo of the current COI; key elements are upload controls, accepted file guidance, privacy copy, and a skip-to-manual option.
- Screen 3: Confirm COI details — collects the confirmed expiration date and optional insurer name; key elements are date field, editable label for the document, current-status preview, and continue CTA.
- Screen 4: Save renewal contact — captures the broker or insurer contact path; key elements are contact name, phone, email, website fields, and copy explaining this becomes the default renewal action.
- Screen 5: Reminder setup — generates calendar reminder timing before expiry; key elements are default reminder offsets, export/add-to-calendar action, explanation of reminder limits, and completion CTA.
- Screen 6: Home / status card — shows whether the current COI is active, expiring soon, or expired; key elements are expiration status, days remaining, resend current COI action, renewal contact shortcut, and replace-current-COI CTA.
- Screen 7: Resend current COI — focused share/send view for the latest document; key elements are file preview, send/share actions, document label, expiration summary, and warning state if expired.
- Screen 8: COI detail / edit — displays current file, confirmed metadata, renewal contact, and reminder status; key elements are metadata fields, edit actions, file preview, and remove/replace controls.
- Screen 9: Replace current COI — uploads the renewed document and confirms the new expiration date; key elements are new upload control, old-vs-new summary, updated status preview, and save action.
- Screen 10: Empty / expired states — handles first-run and failure moments; key elements are no-COI state, expired-COI warning state, add/replace CTA, and plain-language explanation of what to do next.
- Screen 11: Settings / support — holds account basics, data deletion/export, support link, and product-boundary copy; key elements are privacy controls, support contact, and "not legal advice" disclaimer.
Key interactions:
- User lands on the wedge-specific pitch, uploads the current COI, confirms the expiration date, saves renewal contact info, exports reminders to calendar, and reaches the active status card in one session.
- From the home card, the user can resend the current COI, call or email the broker, edit the document details, or replace the COI after renewal.
- When the COI is renewed, the user uploads the replacement, confirms the new date, and the status card returns to green immediately.
- When a property manager asks for proof, the user opens the app, checks that the COI is still current, and sends the latest file from the resend screen.
## Version History
- v1: Original idea
- v2.1: Critique - still too broad, weak on retention, dependent on stale manual risk inputs, and padded with low-value MVP features
- v3: Improved - narrowed to one contractor wedge, removed fake precision and unverified automation, and repositioned the product around current-COI resend plus renewal reminders
