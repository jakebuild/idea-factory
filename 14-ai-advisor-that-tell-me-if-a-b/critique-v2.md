Verdict: WORTH BUILDING
Score: 7/10

What's Actually Good:
- The v1-to-v2 iteration is exactly right. Cutting camera, adding swipe onboarding, defining the verdict format — these are the three decisions that could have killed this product, and they were all fixed before building. That is good product thinking.
- The "well-read friend paragraph" is a genuinely differentiated output format. Every Goodreads clone regurgitates star averages and plot summaries. A second-person opinionated verdict with a single bold CTA is a real product decision, not a feature list.
- Mandatory swipe onboarding is correct. Making it unskippable is correct. Making it feel like a game is correct. 15 books is borderline (see below), but the mechanism is right.
- PWA over native app is the only sane choice for a solo dev validating an idea. This is not a compromise — it is the right call.
- Freemium baked in from day one instead of "we'll figure it out later" is the difference between a sustainable indie product and a burnout project.
- The scope cut list is clean and disciplined. Verdict history, taste profile, and the paywall are all load-bearing features — everything else is correctly deferred.

Brutal Feedback:
- 5 verdicts per month is a conversion killer disguised as a paywall. A reader browsing for their next book on a Sunday afternoon burns through 5 verdicts in 20 minutes. They hit the paywall before they've decided if the product is worth $4.99. You're asking for money before delivering the dopamine loop. Consider 10 free verdicts or a 7-day unlimited trial instead.
- 15 swipes is not enough signal, and you know it. If 10 of the 15 curated books are outside a user's wheelhouse (plausible for anyone who reads nonfiction, romance, or literary fiction exclusively), you have 5 data points going into your "personalized" verdict. That first verdict is going to feel generic, and a generic verdict on the first try is churn. You need either more swipes (20–25) or a smarter onboarding that detects sparse signal and asks a direct follow-up.
- The entire product quality depends on one thing: prompt engineering. Getting an LLM to produce a verdict that feels genuinely personal — not like a horoscope, not like a Goodreads excerpt with "you" injected — requires weeks of iteration, not a sprint. You've listed "20–30 manual test cases before launch" as an open question. That is not an open question. That is the product. Budget more time here than anywhere else in the build.
- The competitive moat is essentially zero. This is Google Books API + a taste profile in a prompt. The moment this gets any traction, someone clones it in a weekend. More critically, Goodreads, Amazon, and StoryGraph already have the reading history data to do this better. They don't because it's low priority for them — but that could change in a quarter.
- "Maybe Later" as a verdict CTA is a hedge. The entire product promise is decisive, opinionated advice from a well-read friend. A friend who says "maybe later" is useless. Force the AI to commit: Read It or Skip It. "Maybe Later" is what you say when you don't have enough taste data or the book is truly borderline — in which case, show that framing explicitly rather than making it a default option.
- LLM API cost is a ticking bomb at $4.99/month unlimited. An enthusiastic reader doing 50+ verdicts a month costs you real money. You need a per-verdict cost model before launch, not after. Either cap "unlimited" at something reasonable (100/month), or price at $7.99 where margins survive power users.
- Magic link auth is correct friction-reduction, but it adds transactional email infrastructure (Resend, Postmark) to the dependency stack. On a 12–14 day build, that's a day of plumbing that bites you when deliverability acts up. Not a reason to skip it, but budget for it.
- The taste profile updating silently from thumbs up/down is architecturally unclear. What is the taste profile data structure? A weighted genre vector? A list of liked authors? A JSON blob you shove into a prompt? "Updates in the background" is hand-waving over the hardest design decision in the product. If the profile is just a prompt string that grows forever, it will eventually blow token limits and produce incoherent verdicts.
- Retention is structurally weak. The use case is episodic: user wants a book, gets verdict, reads or skips, disappears for 3 weeks. There is no daily hook, no streak, no community pull. This is not fatal for an indie product, but it means your paid conversion depends heavily on hitting the right user (someone actively going through a TBR pile) at the right moment. Churn will be real.
- Verdict history is the least valuable screen in this product. Nobody re-reads verdicts from 6 months ago. The engineering time for history persistence and UI would be better spent making the verdict itself sharper. Cut it from MVP or reduce it to a simple flat list.

Key Questions:
- Who exactly is the target user: the casual reader (4–6 books/year) who needs help choosing, or the voracious reader (2–3 books/month) who is already reading and wants validation? These users have very different tolerance for a paywall and different verdict quality requirements.
- What happens when the verdict is wrong and the user reads the book and hates it? The thumbs up/down on a verdict is a signal, but there is no flow for "I read this because you said so and you were wrong." That is the highest-trust feedback signal you'll ever get, and it is missing.
- How does this handle series? "Should I read book 4 of a 14-book series I haven't started" is a completely different question than a standalone. The Google Books API has no series context awareness, and your taste profile has no reading progress state.
- What is the viral or discovery vector? Share copies plain text — but plain text shared by a person recommending a book looks identical to a text message. There is no product branding in the share, no reason for the recipient to download BookVerdict. Growth relies entirely on word of mouth with no engineered loop.

Suggestions:
- Raise the free tier to 10 verdicts or run a 7-day unlimited trial. Let users get addicted before asking for money.
- Expand onboarding to 20–25 books and add a lightweight fallback: if fewer than 8 swipes produce a "Loved It" signal, show a single genre-preference screen before the first verdict. Don't let sparse signal produce a bad first impression.
- Force the verdict to Read It or Skip It only. Retire "Maybe Later." If the AI is genuinely uncertain, the verdict paragraph should say "this is a coin flip for your taste" and still commit to a CTA.
- Decide your taste profile data structure on day one, before writing any prompt. It will touch everything: onboarding, verdict generation, thumbs feedback, and the profile screen. Sketch it on paper first. A flat list of genre weights and a list of liked/disliked author names fed into the prompt is simpler and more robust than a growing narrative.
- Cap "unlimited" at 150 verdicts/month in the fine print. The kind of user who runs 200+ verdicts a month is not your target user anyway.
- Kill the verdict history screen from MVP. Replace it with a single "Recent" section on the Home screen showing the last 3 verdicts as cards. Same value, zero database complexity.
- Choose one book-adjacent community (a subreddit, a Bookstagram niche, a Discord) to seed early users and get real feedback on verdict quality before spending more on growth.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? YES — the scope is tight, every API has good docs, and there are no genuinely novel engineering problems. But the 12–14 day estimate assumes prompt engineering goes fast. It won't. The verdict quality work alone will eat 3–4 days of testing, iteration, and edge case handling. Realistic estimate: 16–20 days for a shippable product.
- Biggest solo complexity traps:
  - Stripe + magic link auth together is 2–3 days of plumbing. Two separate auth/payment flows, webhook handling, edge cases (expired links, failed payments, subscription cancellations) all need to work before you can charge anyone. This is the most underestimated part of the build.
  - Taste profile data structure: if you don't design this explicitly before writing code, you will refactor it twice. The profile feeds onboarding, verdict generation, thumbs feedback, and the profile screen. A bad structure creates cascading bugs.
  - Prompt engineering iteration cycles: you need real test cases with real books and real user personas before you can ship. Each iteration is a manual test run. This is not automatable in a 2-week sprint.
  - Google Books API coverage gaps: self-published books, translated titles, and obscure backlist will have bad or missing metadata. You need a graceful degradation path before launch, not after the first bug report.
  - PWA install prompt handling: getting the "Add to Home Screen" prompt to appear reliably across iOS Safari and Android Chrome requires specific manifest configuration and HTTPS setup. Budget half a day for this.

Design Handoff:
- Screens needed:
  - Onboarding — Swipe Quiz: one book at a time (cover, title, author), three tap options (Loved It / Meh / Haven't Read), progress bar (Book 7 of 20), no skip, fast transition animation between cards
  - Home / Search: prominent search bar, 3 recent verdict cards below (cover thumbnail, title, verdict CTA label), bottom nav with Home / Profile / Settings icons
  - Search Results: list of results (cover thumbnail, title, author, year), tap to trigger verdict, empty state for no results
  - Verdict Loading: sequential animated steps with book-specific copy ("Reading your taste profile..." → "Thinking about [Title]..."), personality-forward, not a spinner
  - Verdict: large book cover, title + author, one personalized paragraph, bold color-coded CTA (Read It green / Skip It red), thumbs up/down below verdict text, share button (copies text)
  - Taste Profile: inferred genre tags (removable), inferred liked authors (removable), add-your-own input, explanatory subtext, last-updated timestamp
  - Paywall / Upgrade: minimal, single CTA, clear value prop headline, price, no distractions
  - Settings: email display, subscription status, manage subscription link (Stripe portal), sign out
- Key UI interactions:
  - Swipe quiz cards animate left/right on swipe; tap buttons work as fallback for non-swipe users
  - Tapping a search result immediately triggers loading state — no confirm step
  - Thumbs up/down on verdict screen shows a brief pulse animation and silently updates profile
  - Paywall intercepts the 6th verdict attempt inline — no navigation away from the search flow
  - Home screen search bar is focused by default on every launch (keyboard pops immediately)
- Data each screen needs:
  - Swipe Quiz: static list of 20 curated books (title, author, cover URL, genre tags)
  - Home: last 3 verdict records (book title, cover, CTA label) from user history
  - Search Results: Google Books API response array (title, author, cover URL, year, description, categories)
  - Verdict: book metadata object + LLM-generated verdict string + user taste profile summary string
  - Taste Profile: aggregated preference object (genre weights, author lists) derived from swipe history and thumbs feedback
  - Paywall: user's verdict count for current billing month, plan status (free/paid)
  - Settings: user email, plan status, Stripe customer portal URL
