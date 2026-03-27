Verdict: WORTH BUILDING
Score: 7/10

What's Actually Good:
- Dropping Goodreads was correct and overdue. Open Library + Google Books are stable, free, and don't require scraping. This single change made the idea viable.
- PWA over native is the right call for a solo builder. No App Store gatekeeping, camera API works in-browser, ships faster. Smart.
- The setup wizard replacing ebook import is genuinely better product design. Tags like "slow burn" and "unreliable narrator" give Claude more signal than "user owns 47 Kindle books."
- The v1 scope is actually disciplined. Local storage, no auth, no email, no price tracking. Rare to see a solo builder scope this cleanly.
- The core loop — scan ISBN → API lookup → Claude verdict — is simple enough to prototype in a weekend. That's a real strength.
- The open questions section is honest and self-aware, especially the iOS Safari flag and the Google Books data quality concern. Most solo builders ignore these until they're on fire.

Brutal Feedback:
- The taste wizard friction is catastrophically underestimated. Rating 10 books AND tagging each one before getting a single shred of value is a brutal ask. This is a Tinder-style swipe UI, which means it's fun for about 45 seconds, and then most people abandon halfway. The builder acknowledges a "skip" path in the open questions but doesn't actually design it. The drop-off between "open app" and "first scan" is the product's biggest existential risk, and it's punted to a footnote.
- Google Books data quality will disappoint you. The builder correctly flags this but then lowballs how bad it is. Google Books reliably returns: publisher description (marketing copy), a handful of genre tags, and a rating count. "Comparable titles" — mentioned in the improved description — is NOT a Google Books API feature. It's an Amazon/Goodreads feature. Open Library is even thinner. Claude is going to be generating "personalized verdicts" almost entirely from book jacket copy. The example verdict ("The pacing is reportedly similar to Gone Girl") is going to look like hallucination, because it is — there's no review data to cite from these APIs. Users who read enough to buy books at physical bookstores will see through this immediately.
- The personalization is shallower than it sounds on paper. The "taste profile" is a tag list: ["slow burn", "psychological thriller", "unreliable narrator"]. Claude gets that list + a publisher blurb and returns 3 sentences. That's prompt injection, not personalization. Any user can get an equivalent result by asking ChatGPT "should I read [book] if I like slow burn psychological thrillers?" — without a 5-minute wizard, without installing a PWA, without any friction. The competitive moat of "AI verdict" is roughly zero because every AI chatbot already does this better, with typing.
- iOS Safari barcode scanning is a real implementation minefield. The builder mentions validating this but frames it as a checkbox item. In practice: Safari does not support continuous camera stream barcode scanning via the standard WebRTC path that Chrome uses. You'll end up using `<input type="file" capture="environment">` which opens the camera app, takes a photo, and passes it back — not a real-time scanner experience. Libraries like QuaggaJS and ZXing both have documented Safari regression issues. If the scanner feels slow or clunky on iPhone, half your users churn on first use. This could eat an entire week of debugging alone.
- No monetization plan means every user costs you money. Each scan is a Claude API call. At even modest traction (100 users × 5 scans/month = 500 Claude calls), you're paying out of pocket indefinitely with no revenue. This isn't a "nice to have" — without a monetization decision baked in, sustainability is undefined. Free tier with a scan cap is obvious but isn't mentioned anywhere.
- Local storage will silently destroy power user data. The v1 scope says "no account, profile stored locally." Fine for MVP. But mobile browsers clear local storage aggressively — browser updates, clearing cache, switching phones, using incognito. The users who complete the wizard, build a wishlist, and scan 20 books are exactly the users who will hit this wall. They're also the users most likely to tell others about your app. Losing their data without warning is a trust-killer.
- StoryGraph already exists and does this better for taste profiling. Amazon's app lets you scan books and see ratings/reviews instantly. Goodreads has the network. The "AI verdict while standing at the shelf" is the actual differentiator — but the builder hasn't articulated why someone opens a PWA instead of just typing a question into ChatGPT, which requires zero setup and gives arguably better answers.
- The 10-book wizard has a product-market fit tension baked in. Casual readers — exactly the kind of person who'd want help deciding at a bookstore — struggle to name 10 books they've read AND loved AND can identify in a swipe UI. Heavy readers already have systems. The wizard serves neither group optimally.

Key Questions:
- Have you manually tested the core loop with 10 real books? Type the ISBN into Google Books API right now and see what data you actually get back. Then paste that into Claude with a taste profile and read the verdict. Does it feel like magic or does it feel like a book report?
- Why does a user open your PWA instead of typing "should I read [book]?" into ChatGPT? What's the specific answer to that question, in one sentence?
- What happens to a user's wizard and wishlist data when Safari clears local storage? Have you designed the failure state?
- iOS Safari barcode scanning — have you tested it yet? Not "planned to test," actually tested?
- What's the cost per scan and what's the plan when 100 users are scanning 5 books a month?

Suggestions:
- Build the core loop in day one, before any wizard. Hardcode a taste profile ("I love slow burn psychological thrillers with unreliable narrators"), scan an ISBN, call Google Books, call Claude, read the verdict. If the verdict isn't impressive, nothing else matters. If it is impressive, you have proof the idea works.
- Make the wizard skip-able by default, not as an afterthought. "Skip setup → get a generic verdict → rate this book to improve future verdicts" is a better cold-start UX. It lets users experience the product before committing to it.
- Test iOS Safari barcode scanning on day one of week one. Not day three, day one. If it doesn't work via WebRTC, decide whether to use the file-capture fallback or build native before you invest in anything else.
- Add a monetization line: 10 free scans/month, $3/month for unlimited. Slap it in the footer. Decide now so you're not surprised by your own API bill.
- The competitive angle needs sharpening. "AI verdict" isn't enough. Lean into the speed and camera-first UX: "No typing. Open, scan, 30 seconds, done." That's a real behavioral difference from asking ChatGPT.
- Consider caching verdicts by ISBN + taste profile hash. If two users with similar profiles scan the same book, serve the cached result. Cuts API costs dramatically and makes the app faster.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? YES — with one major caveat: test the iOS Safari barcode scanner before writing a single line of wizard UI. The core loop (barcode → ISBN → API → Claude → verdict) is genuinely 1 week of work with AI assistance. The wizard is another week. Week 3 is polish and edge cases. The timeline is realistic IF the scanner works on iOS. If it doesn't, week 1 becomes "fight with WebRTC on Safari" and everything slips.
- Biggest solo complexity traps:
  - iOS Safari camera API — not a small risk, a real week-eater if it goes wrong
  - Google Books data quality disappointment mid-build — you might finish the integration and discover the verdicts are bland, forcing a rethink of the prompt strategy
  - Wizard scope creep — "just 5 more tags," "just show more books," "just add a genre filter" — this UI is a rabbit hole if you let it be
  - Local storage data loss with no warning — not complex to solve (at minimum, show users "export your profile") but easy to forget until a user complains
  - API rate limits — Google Books has quotas; at free tier you get 1000 requests/day, which sounds like a lot until a few active users start scanning rapidly

Design Handoff:
- Screens needed:
  - Home (empty state — first visit, "Start Setup" CTA + app tagline)
  - Home (returning user — recent scans list, large Scan button, wishlist badge)
  - Taste Setup Wizard: Step 1 — Book Rating (swipe card stack, progress indicator, skip button)
  - Taste Setup Wizard: Step 2 — Tag Why You Loved It (tag picker grid, per loved book, multi-select)
  - Taste Setup Wizard: Complete (profile summary, "Start Scanning" CTA, celebration moment)
  - Scanner (full-screen camera, barcode reticle overlay, "Enter ISBN manually" fallback link)
  - Loading / Verdict Generation (book metadata preview shown while AI generates, animated loader)
  - Book Verdict — the money screen (book cover large, 3-sentence AI verdict, "Why you'll love it" + "Watch out for" bullets, Save/Scan Another/Share actions)
  - Scan History (scrollable list, cover thumbnail + title + date + wishlist icon, empty state)
  - Wishlist (2-column grid, cover + title + date, swipe-to-remove, empty state)
  - Taste Profile / Settings (tag cloud of current profile, "Add more books" button, "Reset profile" destructive action)
  - Manual ISBN Entry (number input, lookup button, error state, back link)
  - Error state: Book not found (no ISBN match — show manual search or "try another book")
  - Error state: Scanner permission denied (camera permission blocked — instructions to enable)

- Key UI interactions:
  - Swipe right/left/up on book cards in wizard (loved / not for me / haven't read)
  - Multi-select tag grid (tap to toggle, persists per book)
  - Real-time barcode detection (camera stream with overlay reticle, auto-triggers on detect)
  - Verdict card expand/collapse (3-sentence summary → full breakdown)
  - Save to Wishlist (tap icon, instant feedback, no auth required in v1)
  - Swipe to remove from Wishlist
  - Re-view past verdict from History or Wishlist (tap row → Verdict screen, pre-populated from cache)
  - Reset profile (destructive action, confirmation dialog)

- Data each screen needs:
  - Home: list of last 3 scanned books (ISBN, title, cover URL, scan timestamp), wishlist count
  - Wizard Step 1: curated list of ~30 popular books (title, author, cover URL, genre) — hardcoded or fetched from a small static JSON
  - Wizard Step 2: the books the user swiped right on, tag options list (hardcoded)
  - Wizard Complete: computed taste profile tags derived from Step 2 selections
  - Scanner: no data needed — just camera access and ISBN output
  - Loading: book metadata (title, author, cover) from ISBN lookup, in-flight
  - Verdict: book metadata + AI-generated verdict text (3 sentences) + two bullet arrays ("love it" / "watch out for")
  - Scan History: all scanned books array sorted by timestamp (ISBN, title, author, cover, date, wishlist flag)
  - Wishlist: filtered scan history where wishlist flag = true
  - Taste Profile: user's taste tag list + rated books list
  - Manual ISBN Entry: text input state, error state (not found / invalid format)
