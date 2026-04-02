Verdict: WORTH BUILDING
Score: 6/10
What's Actually Good:
- The pain is real and expensive. People do forget trials, renewal dates, and price jumps, and getting burned feels immediate.
- The reveal moment is strong. "You are about to lose $X by Y date" is sharper than generic subscription tracking sludge.
- The MVP can be narrow. Screenshot in, amount/date out, reminders on. That is small enough for a solo builder to plausibly ship.
- The input behavior already exists. People already save signup emails and subscription screenshots because they do not trust companies to be clear.
- The fear-of-loss framing is more emotionally potent than budgeting dashboards. This has a better chance of being memorable than another fintech organizer.
Brutal Feedback:
- "Copies Rocket Money" is a bad sign, not a strategy. You are walking into a category where the incumbent already owns the default user expectation: automatic tracking, bank sync, and broad coverage. Your app is a manual workaround.
- The entire product depends on messy extraction from screenshots and emails, and you are pretending that is a solved detail. It is not. OCR misses dates, marketing emails bury terms in tiny text, App Store screenshots vary, and one wrong deadline destroys trust.
- "Extracts the exact amount" is dangerously overconfident. Lots of trials do not show the exact post-trial charge cleanly, taxes vary, currencies vary, and some offers switch plans or prices by region. Exactness is a promise you probably cannot keep reliably.
- "Hidden renewal traps" sounds cool and fake. Most of what you call a trap is just ugly billing copy buried in legalese. Unless the app can consistently identify non-obvious edge cases better than the user reading the email, this is branding inflation.
- The cancel-prep checklist is weak sauce. If the app cannot actually help cancel, deep-link to the right place, or auto-generate a calendar/reminder flow that users trust, then "prep checklist" is just decorative UX around anxiety.
- Your retention story is not proven. Why does the user come back after day 1? Most users do not sign up for new trials every week. This is a spiky utility, not obviously a habit.
- The app risks becoming a glorified reminder tool with OCR attached. That is not terrible, but it is much less exciting than the pitch and much harder to monetize.
- Manual input is friction you are underestimating. Users have to remember to upload the screenshot or forward the email at the exact moment they sign up. The whole point of trial regret is that people are impulsive when they subscribe and forgetful afterward.
- The strongest competitor is not Rocket Money. It is "set a calendar reminder in 4 seconds and move on." If your flow takes longer than that, the product loses on day zero.
- There is no moat here. Screenshot parsing plus reminders is cloneable, and generic AI tools can do most of the extraction with one decent prompt.
- Notification-heavy fear UX can backfire fast. If the tone is too aggressive, users mute it. If the tone is too soft, the core emotional hook disappears. That balance is harder than the pitch makes it sound.
- App Store subscription pages are especially annoying because the actual cancellation path often lives outside your app and differs by platform, region, and subscription type. If the product implies easy escape but hands users vague instructions, that gap will feel insulting.
- You are also assuming users trust an app with renewal screenshots and email content. That is not bank-level sensitive, but it is personal enough to trigger hesitation unless privacy handling is extremely clear.
- If the seed material is "screenshots and renewal emails are already available," that is only half true. The real work is structuring noisy text into reliable fields, handling confidence, and giving users a way to correct bad parses. That manual cleanup work belongs in the build estimate.
Key Questions:
- What is the exact extraction stack, and what happens when the app is only 70 percent confident about the amount or date?
- Why does the user come back after day 1?
- Is the real wedge "fear reveal dashboard" or "dead-simple trial reminder capture"? Those are different products.
- Can you beat a calendar reminder on speed and clarity, or are you just wrapping the same behavior in scarier copy?
- Which inputs are actually reliable for MVP: forwarded renewal emails, screenshots, App Store pages, or all three? Trying to support everything at once is how this gets sloppy.
- Will the app help users cancel, or only warn them? If it only warns, how much value is left after the first dramatic reveal?
Suggestions:
- Cut the scope to one reliable input first, preferably forwarded renewal emails or pasted text from emails. Screenshots from every source are a mess magnet.
- Stop promising "exact" extraction unless you have a confidence system and an edit flow. Better to say "we found a likely charge and deadline" than to be precisely wrong.
- Make the MVP win on speed: ingest one proof, show one blunt risk card, add one reminder with one tap.
- Add an explicit confidence/edit step. If the parsed date or amount is wrong, the user should be able to fix it in seconds instead of abandoning the product.
- Treat cancellation guidance as a lightweight assist, not a fake superpower. Link users to the right platform help page or settings path instead of pretending you can magically cancel everything.
- Build for a narrow audience first, like heavy free-trial users, deal hunters, or people managing app subscriptions, instead of "everyone with recurring charges."
- Test whether the emotional hook works without overdoing the theatrics. Fear sells the first click and can kill retention if the app feels manipulative.
- If you want better retention, add a simple running "money saved" scoreboard and a calendar timeline of upcoming charges. Without that, the product has no durable home screen reason to exist.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — a thin MVP is realistic if you narrow the inputs, skip fake automation, and accept that extraction will need manual correction. The broader pitch with multiple input types, reliable trap detection, and polished cancel flows is too ambitious for a few weeks.
- Biggest solo complexity traps: unreliable OCR and parsing across screenshots and emails, confidence and error handling for wrong dates/amounts, notification timing and reminder logic, cancellation-path fragmentation across platforms, privacy/trust UX, and overbuilding generic subscription management instead of shipping one sharp workflow.
Design Handoff:
- Screens needed: onboarding/value prop, import proof, extraction review/edit, risk reveal dashboard, charge detail view, reminder settings, cancellation help view, history/saved money view, settings/privacy.
- Key UI interactions: upload screenshot or forward email, parse-and-confirm extracted fields, swipe or tap between upcoming charges, set escalating reminders, mark canceled, open cancellation instructions, correct parsed data inline.
- Data each screen needs: onboarding needs core promise and sample outcome; import needs source type and upload state; extraction review needs raw text/image preview plus parsed amount/date/plan/confidence; dashboard needs total upcoming loss, countdown cards, urgency state, and reminder status; detail view needs source proof, parsed fields, notes, and cancel deadline; reminder settings need notification schedule and timezone; cancellation help needs platform/source-specific steps and outbound links; history needs canceled items, saved amount estimate, and past reminders; settings needs privacy policy, notification permissions, and data deletion state.
