Verdict: NEEDS MAJOR WORK
Score: 6/10
What's Actually Good:
- You finally stopped cosplaying as an AI skincare oracle and cut the scanner, OCR, database, auth, and fake personalization bloat. That is the first adult decision this idea has made.
- The scope is now small enough that a solo developer could plausibly ship a working MVP instead of drowning in integrations and taxonomy sludge.
- The input model is less stupid than product parsing. Step cards are a real simplification, even if they are still imperfect.
- The core job is at least understandable now: "tell me if tonight's routine is reckless before tomorrow matters."
Brutal Feedback:
- This is still dangerously close to being a glorified checklist with attitude. The core value proposition is "don't combine harsh skincare steps before an important morning." That is useful, but it is not remotely novel.
- The biggest problem is unchanged: the app is only as smart as a tiny manual rule table. If the product's brain is 8-12 canned warnings, users may get the same value from a decent TikTok, Reddit post, or dermatologist infographic without needing your app at all.
- Your differentiation is weak because the hard part is not code, it is trust. And right now trust is exactly where the product is vulnerable. If you miss a common irritation combo, you look incompetent. If you over-warn, you look alarmist. In skincare, "conservative" can still feel dumb.
- UNVERIFIED: the step-card taxonomy is better than scanning, but it still assumes users know what bucket their routine belongs in. Many do not. A serum with multiple actives, a toner marketed with vague language, or a treatment that straddles categories will make people hesitate. The second they hesitate, your "simple" flow stops feeling simple.
- The retention loop is still weak. Why does the user come back after day 1? "Another important morning someday" is not a retention engine. It is occasional utility. That can support a tiny tool, but it does not support inflated product ambition.
- Optional next-morning logging is not harmless just because you call it an experiment. It adds another screen, another concept, another piece of copy, and another place for users to ignore you. Most people will not want homework after a night routine. If the logging is low, that whole branch is dead weight.
- The "importance of avoiding irritation tomorrow" input smells like fake nuance. Either it changes the recommendation logic, which is risky because user anxiety should not alter skin advice, or it only changes the tone, which means it is ornamental UX fluff.
- The rules are still not actually "ready." They need source selection, wording, severity calibration, contradiction handling, examples, and QA. That is real product labor, not setup polish. Your build estimate is flattering itself by treating content rigor like a side quest.
- The market is awkward. Skincare beginners may not trust themselves to classify products. Skincare enthusiasts may find the advice insultingly obvious. That leaves you fishing for anxious intermediates who use actives, have event-driven panic, and also want a dedicated checker instead of Google. That is a narrower audience than the writeup admits.
- The brand premise still has a trashy undertone. You say you moved away from attractiveness claims, but the original DNA is still "your face will look worse tomorrow." That fear-based framing might get clicks, but it can also make the product feel manipulative and unserious.
- There is no moat, and worse, there may not even be much product. If the best version of this thing is a well-designed one-session checker, fine, but then stop pretending there is a meaningful compounding system here.
Key Questions:
- Why does the user come back after day 1?
- Which exact 8-12 rules make v1, and what source standard keeps them from being Reddit-tier skincare folklore?
- What happens when one product clearly belongs to two step cards, or the user does not know whether it does?
- Does the "importance tomorrow" selector change output logic or just copy tone?
- If next-morning logging gets ignored, what is left besides a disposable checker?
- What is the acquisition channel for a tool that solves a niche, occasional anxiety spike?
Suggestions:
- Cut the next-morning log from v1 unless you are explicitly running a validation test with real users. It is the easiest thing to romanticize and the easiest thing for users to ignore.
- Remove the "importance tomorrow" selector unless it drives a concrete decision rule. Decorative personalization is weak product design.
- Shrink the cards further to only the clearest high-risk steps if user testing shows hesitation. Fewer confident inputs beat more ambiguous ones.
- Treat the rule set as the actual product and budget time accordingly. If the rules are mediocre, the app is mediocre regardless of code quality.
- Position this honestly as a disposable pre-event checker, not a habit product, until real repeat usage proves otherwise.
- Validate the audience fast with fake-door landing pages or concierge tests before polishing seven screens for a maybe-useful utility.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — the app shell is easy, but trustworthy rules, crisp taxonomy, and copy that does not sound reckless will eat more time than the code.
- Biggest solo complexity traps: overestimating how obvious the step cards are, underestimating the manual work to define and validate rule copy, shipping fake nuance like "importance tomorrow," keeping the optional logging flow after it proves weak, and mistaking a one-session utility for a real recurring product.
