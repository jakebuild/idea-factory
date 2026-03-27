Verdict: WORTH BUILDING
Score: 7/10

## What's Actually Good:

- The pivot from ebook parsing to Goodreads reading history is the right call — not just "better," it's the only call. The original was legally radioactive and technically unshippable.
- The UX flow is tightly scoped. Scan → result in under 3 seconds is the correct north star. Everything else is detail work.
- Killing mind maps was correct. Mind maps in a bookstore scanner is the kind of feature that gets you a Product Hunt upvote and zero returning users.
- The freemium math actually holds. $2.09 net per subscriber, ~$0.002 per scan = breakeven is trivially low. This is one of the few AI app monetization plans I've seen that isn't delusional.
- The Design Handoff section (written by the ideator) is already complete and correct. Nine screens, clear flows, no ambiguity. A designer could open Figma right now. That's rare.
- RevenueCat for payments is the correct tool — don't roll your own IAP logic as a solo dev.
- Open Library as the metadata source is smart. No API key, no rate limits, no cost. Correct call.
- The cached taste profile approach (built at login, not at scan time) shows actual architectural thought. Most solo devs would have put the Claude call on the hot path and wondered why it felt slow.

## Brutal Feedback:

- **The Goodreads API is functionally dead and you know it.** You buried this in "Open Questions" like it's a minor concern. It is not. Amazon shut down public Goodreads API access in 2020. New developers cannot get keys. Existing keys still technically function but are undocumented, unsupported, and could break any day. This is not a grey zone — it is a graveyard. Your entire data acquisition strategy rests on a closed API owned by a company that has shown zero interest in maintaining it. If you're planning to ship this, you need a concrete answer to "where does the taste profile data come from" before writing a single line of code.

- **StoryGraph has no public API either.** You listed it as the "safer bet" fallback, but StoryGraph is a small team that doesn't publish a public API. You'd be scraping, which is fragile and against their ToS. This isn't a fallback — it's a second API problem.

- **Your target user is tinier than you think.** The Venn diagram: people who browse physical bookstores AND have a Goodreads account with 20+ ratings AND want AI recommendations AND will pay $2.99/month. Each filter cuts the audience dramatically. Book nerds with Goodreads accounts already know what they want to read — they have to-read shelves 200 books deep. Casual browsers don't have Goodreads accounts. You're targeting a sliver of a sliver.

- **10 scans/month will paywall users on their first real session.** A single visit to a decent bookstore produces 15-30 books picked up and examined. Your free tier runs out in one afternoon. That's not a freemium model — that's a trial that expires mid-experience. You'll get uninstalls, not upgrades.

- **The taste profile prompt sent to Claude will be messier than you expect.** A user with 300 rated books on Goodreads generates a huge context. If you summarize it, you lose signal. If you send it raw, you hit token limits and latency targets. The "concise taste profile" that fits in a fast Claude Haiku call while remaining meaningfully personalized is an unsolved prompt engineering problem — not an assumption you can defer.

- **"82% match" is a made-up number and users will know it.** Percentages imply precision. Claude does not do reliable numeric scoring — it will hallucinate consistent-looking but meaningless percentages. Users who scan two similar books and get "82%" and "79%" will have no idea what that means. You need to either remove the percentage or build an actual scoring system outside of Claude (semantic similarity, genre overlap, author adjacency) rather than asking the LLM to produce a number.

- **Open Library data quality for new releases is poor, but new releases are your primary use case.** People in bookstores are mostly browsing new titles, staff picks, and recent bestsellers. Open Library is great for catalog coverage of older books and unreliable for books released in the last 6-12 months. The moment you scan a hot new release and get back empty metadata, the app feels broken.

- **Goodreads' own barcode scanner exists and already shows ratings, reviews, shelves, and similar books.** Your differentiator is the personalized Claude verdict. That's a real differentiator — but you need to make sure users understand why "82% match to your taste" is meaningfully better than what Goodreads already shows them. The marketing message needs to be sharp.

- **$2.99/month for an app you use at bookstores is a tough recurring charge.** Bookstore browsing is not a daily habit. Even avid readers visit physical stores 2-4 times a month. That's $36/year for occasional use. Compare to Spotify ($11/month, used daily) — the value-per-dollar perception is unfavorable. Consider a one-time purchase model ($4.99-$6.99) or a per-scan credit system instead of monthly recurring.

## Key Questions:

- What is your actual data source plan given that Goodreads API is dead and StoryGraph has no public API? Have you tested whether Goodreads API keys can still be obtained? Do you have a fallback that doesn't involve scraping?
- What happens to users with under 20 ratings? The idea mentions this but the "rate 10 books" fallback is a completely different onboarding flow that adds scope. Have you scoped that?
- How do you build a taste profile that fits in a fast Haiku prompt without losing the personalization that makes this better than Goodreads' own scanner?
- Why monthly subscription instead of one-time purchase? Has this been validated with real users or assumed?
- Have you actually checked whether Open Library has metadata for the top 100 books in a Barnes & Noble right now?

## Suggestions:

- **Solve the API problem first, before any code.** Try to get a Goodreads API key. If you can't, the app needs to pivot to: (a) manual in-app book rating (builds its own taste data), (b) CSV import from Goodreads export (Goodreads lets users export their library as CSV — this is a real, documented, legal route), or (c) partner with StoryGraph. Option (b) is probably the fastest viable path: "Export your Goodreads library, import the CSV" is ugly UX but it works without any API.
- **Replace the percentage with a label system.** Instead of "82% match," use: "Strong match," "Decent match," "Weak match," "Not your genre." Derive these from actual signals (genre overlap, author similarity, average rating patterns) computed locally. Let Claude write the reasoning paragraph but don't ask it to produce a number.
- **Raise the free tier to 25 scans/month or drop it entirely.** 10 scans is too low. Either make the free tier generous enough that users actually build the habit, or skip freemium entirely and charge a one-time $4.99. Habit formation matters more than paywall pressure at launch.
- **Implement Goodreads CSV import on day one.** It's uglier than OAuth, but it's reliable, legal, requires no API key, and gives you richer data (the CSV includes ratings, dates read, shelves, reviews). This is your safety net.
- **Verify Open Library coverage before building the scan flow.** Pull ISBNs for the current NYT bestseller list and check coverage. If it's below 80%, add Google Books API as an immediate fallback.
- **Reframe the marketing against Goodreads' own scanner.** The pitch needs to be explicit: "Goodreads' scanner shows you what the crowd thinks. BookScan shows you what *you'll* think, based on your history." That framing is differentiating and true.

## Solo Dev Reality Check:

- **Can one person ship this in 2-4 weeks with AI coding tools?** MAYBE — the app architecture is genuinely simple: barcode scan + API call + display. The UI is well-scoped. BUT — the Goodreads API uncertainty could blow up week one. If you can't get a Goodreads API key and have to pivot to CSV import, add 3-5 days. If you go with CSV import from day one, YES, this is a 2-3 week ship.

- **Biggest solo complexity traps:**
  - Goodreads API access failure requiring mid-build pivot to CSV import — plan for this now, not after you've built the OAuth flow
  - Taste profile prompt engineering — getting Claude to produce useful, fast, non-hallucinated verdicts requires iteration; budget a full day just for this
  - iOS camera barcode scanning UX — getting the viewfinder to feel snappy and reliable across lighting conditions takes more polish than expected; use Vision framework, not a third-party scanner
  - RevenueCat + Apple IAP sandbox testing — budget a half-day minimum; sandbox IAP testing is notoriously finicky
  - Open Library metadata gaps — you will encounter books with no data on day one of real testing; build the fallback before launch, not after user complaints
  - Cold start problem (users with few ratings) — if you ship without a rating-collection fallback, a meaningful percentage of signups will hit a dead end

---

## Design Handoff:

*(Already complete in the idea document — the ideator produced a full 9-screen spec with flows. No additions needed. A designer can start directly from the Design Handoff section in idea-v2.md.)*

- **Screens needed:** Onboarding, Loading/Syncing, Home/Scan, Manual Entry, Loading Result, Result Screen, Scan History, Paywall/Upgrade, Settings
- **Key UI interactions:** Barcode scan → auto-detect → result; manual search fallback; paywall trigger on scan limit; history swipe; OAuth webview for Goodreads
- **Data each screen needs:** Onboarding needs nothing; Syncing needs import progress count; Home needs remaining scan count; Result needs book metadata + Claude verdict JSON + similar books from user history; History needs cached scan list; Paywall needs current scan count vs. limit; Settings needs connected account info + subscription status
