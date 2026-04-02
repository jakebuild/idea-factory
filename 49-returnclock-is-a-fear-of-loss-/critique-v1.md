Verdict: NEEDS MAJOR WORK
Score: 5/10
What's Actually Good:
- The value proposition is instantly understandable: "miss this date, lose this money" is sharper than generic package-tracking or shopping-assistant fluff.
- The fear-of-loss framing is stronger than "organize your purchases" because it creates urgency and gives the app a screen-recordable hook.
- A narrow MVP exists if you brutally cut scope to manual entry plus reminders instead of pretending reliable extraction is solved on day one.
Brutal Feedback:
- This is built on the fantasy that return policies are clean, parseable, and consistently enforceable from random screenshots. They are not. Retailers hide exceptions in product category rules, holiday windows, final-sale clauses, membership tiers, opened-item exclusions, and region-specific terms. Your "exact return deadline" promise is where this idea starts lying.
- "Screenshot order confirmations, policy pages, or shipping emails" sounds flexible, but it is really three different ingestion systems pretending to be one feature. OCR from screenshots, email parsing, and policy interpretation are separate problems. That is scope creep disguised as convenience.
- The killer screen only works if the extracted answer is correct. If you tell someone "lose $86 by Sunday 5:00 PM" and the merchant says the item was final sale or the deadline was actually based on delivery date, your hero moment turns into instant distrust. This is not a small bug. It is the whole product collapsing.
- The app does not actually solve the hard part of returns. The hard part is printing labels, repackaging items, finding drop-off points, dealing with partial refunds, and overcoming user procrastination. A countdown is emotional theater unless it materially reduces the friction of returning the item.
- The Rocket Money comparison is flattering nonsense. Subscriptions are recurring pain with repeated savings opportunities. Shopping regret is episodic and inconsistent. People do not have ten active return windows every month unless they are pathological returners, and those users may already be using retailer-native flows.
- Retention is weak. Why does the user come back after day 1? Most people will only open this app when they already regret a purchase. That is not a habit loop; that is occasional damage control.
- The "save the proof once, watch the countdown" loop is barely a loop. It is a one-off timer with notifications. That is a feature, not a durable product.
- Refund amount at risk is often not the real amount at risk. Shipping fees may be non-refundable. Taxes may or may not be returned. Store credit versus original payment matters. Restocking fees vary. Return shipping can eat the refund. Your headline metric is messy in exactly the way users hate.
- Escalating reminders are easy to build and easy to ignore. Notifications are not a moat. Apple Reminders can already scream at me for free.
- If you later need inbox access, merchant integrations, Gmail parsing, or retailer-specific logic to make this accurate, the solo-dev-friendly story dies fast. That is a team-and-maintenance business, not a few-weeks vibe-coded app.
- There is also an ugly trust problem: users must hand over purchase screenshots and maybe email data so the app can tell them whether they screwed up a return. That is high sensitivity for a lightweight utility with no brand credibility.
- The market may be narrower than you think. Heavy online shoppers already get flooded with merchant emails and app notifications. Casual shoppers do not suffer enough return losses to install a dedicated app. You are squeezed between "not painful enough" and "already solved elsewhere."
Key Questions:
- How will you verify return deadlines when policies depend on delivery date, item condition, sale status, membership level, or category exceptions?
- Why does the user come back after day 1?
- What is the MVP if OCR and policy extraction are unreliable? Manual entry? If yes, is that still compelling enough to install?
- How many real users lose enough money from missed returns each month to care about a dedicated app instead of calendar reminders?
- What happens when the app is wrong and costs the user money? Do you just shrug and say "best effort"?
- Are you building a notification utility, a document parser, or a full return assistant? Right now it is trying to be all three.
Suggestions:
- Cut the magic. Start with a dead-simple "return deadline vault" where users manually enter purchase amount, merchant, deadline, and optional proof screenshot. That is boring, but at least it is honest and shippable.
- Narrow the use case to one ingestion path for MVP. Best option: screenshot plus manual confirmation of the extracted deadline before anything is saved.
- Stop promising "exact" unless the user confirms the parsed result. If accuracy is user-verified, the trust problem becomes manageable.
- Add one behavior-changing action beyond reminders: checklist for "box item, label printed, drop-off selected, return completed" so the app helps users finish, not just panic.
- Test demand with a fake concierge flow before building extraction logic. If people will not manually track returns to avoid losing money, they definitely will not care about your AI parser.
- Consider a less brittle angle: "money at risk tracker for returns and rebates" with manual setup, instead of trying to infer policy truth from chaotic merchant artifacts.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — but only if the MVP is basically manual entry, screenshot attachment, countdowns, and notifications. The marketed "instant exact extraction" version is not a credible 2-4 week solo build.
- Biggest solo complexity traps: OCR quality from messy screenshots; policy parsing ambiguity; delivery-date-dependent deadlines; notification reliability; email ingestion creep; retailer-specific exceptions; refund amount calculation edge cases; trust/support burden when parsed data is wrong
