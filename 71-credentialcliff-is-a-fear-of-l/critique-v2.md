Verdict: WORTH BUILDING
Score: 6/10
What's Actually Good:
- This is dramatically less stupid than v1. Narrowing from "all credentials for all licensed workers" to one document type finally gives the product a shape.
- Manual-first confirmation is the right move. You stopped pretending OCR is magic and moved the trust boundary back onto user-confirmed data.
- The red risk card is still the strongest part of the concept. "Your COI expires in 18 days and $1,400 this week is exposed" is blunt, legible, and emotionally effective.
- Saving the renewal path is smart because it turns panic into action instead of fake compliance intelligence.
- This is close enough to a real solo-buildable utility that a single developer could actually test demand instead of drowning in platform fantasies.
Brutal Feedback:
- You did not invent a market category here. You built a sharper reminder app for one annoying document. That can still work, but stop dressing it up like a new operating system for contractor risk.
- The retention story is still shaky and still UNVERIFIED. A weekly "Work Protected?" review sounds tidy in a product doc, but in real life it is one more chore for people already juggling jobs, invoices, and client bullshit. Why does the user come back after day 1? Because you nag them. That is not a moat. That is a notification schedule.
- The product depends on users doing manual upkeep on the two most perishable fields in the app: weekly income exposure and next booked job date. Those numbers drift constantly. The second they go stale, the red card becomes fake precision theater.
- You cut the impossible compliance brain, which was good, but now the value proposition is thinner than you want to admit. "Store a COI, track expiry, keep a broker link, send reminders" is useful, but it is also brutally easy to replicate and painfully hard to charge much for.
- You are still hiding fragmentation inside the phrase "independent contractors." A cleaner, handyman, photographer, DJ, and field inspector do not buy software the same way, discover products the same way, or feel COI urgency with the same frequency. If you cannot name the first wedge precisely, your marketing is still mush.
- The onboarding burden is not solved. The user still has to find a COI, upload it, verify dates, enter weekly income exposure, enter next booked job, add a renewal path, and configure reminders before they feel the payoff. For a product whose whole pitch is "don't lose work," that setup is longer than it should be.
- OCR is correctly demoted to convenience, but it is still UNVERIFIED. If the OCR prefill is wrong often enough, it does not save setup time; it adds a trust tax because users now have to audit the machine before trusting the app.
- The archive/history idea is fluff unless users actually need records for disputes or audits. Most people do not want a museum of old COIs. They want the current one not to screw them.
- Reminder delivery is being treated like a solved problem when it absolutely is not. Mobile web reminder reliability is mediocre, email gets ignored, push requires app install and permissions, and SMS costs money. For this kind of product, reminder failure is not a minor bug. It is the product failing at the only job the user hired it to do.
- Monetization is awkward. Users who only need this a few times a year will resist a subscription, and a one-time purchase makes reminder infrastructure and support economics uglier. If the business model is "maybe contractors are anxious enough to pay anyway," that is not a business model.
- There is a decent chance the real best version of this is not a standalone app at all. It may be a tiny mobile-first utility, a WhatsApp/SMS concierge, or a feature inside a broader contractor admin tool. Standalone only works if the pain is both frequent and sharp enough to earn a home-screen slot.
- Your estimate still reads like happy-path math. The screens are easy. The annoying parts are reminder reliability, file handling edge cases, document replacement flows, permission friction, offline capture weirdness, and writing copy honest enough that users do not assume you give legal/compliance advice.
Key Questions:
- Which exact contractor wedge launches first: cleaners, handymen, photographers, notaries, inspectors, or someone else? "Independent contractors" is not a wedge.
- Why does the user come back after day 1 if their COI is not close to expiring and no job is at immediate risk?
- What happens when the user ignores the weekly review for three weeks and the red card is built on stale income/job inputs?
- What reminder channel is the real MVP: native push, email, SMS, or calendar export? Pick one and be honest about its failure modes.
- Is the first paid offer a cheap annual utility, a per-document fee, or a free tool to validate demand? If you do not know, you do not know whether this is a product or a feature.
- Do users actually care about upload history, or are you adding recordkeeping because the core product feels too thin?
Suggestions:
- Narrow again. Pick one contractor type with frequent proof-of-insurance requests and ugly consequences for showing up with stale paperwork.
- Treat the weekly review as an experiment, not as the retention thesis. If users will not maintain the risk inputs, default the product back to expiry tracking and reminder execution.
- Make manual entry the true default and keep OCR behind a soft convenience layer until document accuracy is proven on real samples.
- Cut history from MVP unless interviews show a real recordkeeping need. Current document, confirmed expiration, renewal path, and reminder control are enough.
- Decide the platform based on reminder reliability, not developer comfort. If mobile web cannot do the job dependably, stop pretending speed-to-ship makes it the right choice.
- Validate willingness to pay before polishing the vault. If nobody will pay for "don't forget your COI," the product is a nice utility, not a business.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — a stripped-down version is plausible, but only if the builder stops inflating the weekly review into a habit system and focuses on document tracking plus reliable reminders.
- Biggest solo complexity traps: choosing a real contractor wedge instead of a fake broad one; reminder reliability across platforms; OCR confidence and correction UX; file upload/storage edge cases; permission friction for notifications; stale manual risk inputs turning the dashboard into nonsense; weak monetization for a low-frequency utility; writing copy that is useful without implying compliance advice.
Design Handoff:
- Screens needed: landing page, add COI method, manual COI entry, OCR review/confirmation, risk setup, home/dashboard with risk card, COI detail, reminder settings, replace COI flow, weekly review prompt, settings/support, empty states for no COI and expired COI.
- Key UI interactions: upload or manually add a COI, confirm or correct extracted fields, enter income exposure and next job date, tap into the renewal link/contact, adjust reminder timing, replace the current COI with a renewed one, complete or dismiss a weekly review, edit stale risk inputs from the dashboard.
- Data each screen needs: current COI file and metadata, confirmed expiration date, OCR candidate values plus confidence state, insurer/broker contact, renewal URL/email/phone, weekly income exposure, next booked job date, reminder schedule and channel, replacement history if kept, review due state, notification permission state, support/legal boundary copy.
