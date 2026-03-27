Verdict: WORTH BUILDING
Score: 6/10

What's Actually Good:
- The pivot from "lookup tool" to "will I personally like this" is legitimate differentiation. Google Lens, Amazon, and Goodreads tell you what a book is. None of them tell you whether *you specifically* will like it at the moment you're holding it. That gap is real.
- Killing cover recognition and switching to barcode scanning was the right call. Deterministic, reliable, no CV model to maintain. ISBN → Google Books API is a solved pipeline.
- The wishlist as a retention mechanism is smart. It transforms a one-shot tool into a bookstore companion habit. Scan → save → compare → buy is a genuine loop.
- Scope discipline is good. The in/out list for v1 is clean. No social features, no offline mode, no price comparison. This is someone who has actually thought about what kills solo projects.
- Technical stack is genuinely manageable: barcode scanner library, one free API, one LLM API call. No backend infra needed if you keep it client-side with local storage.
- The 10-14 day estimate is believable, not delusional.

Brutal Feedback:
- The target user is a dying breed. People who (a) regularly browse physical bookstores, (b) feel genuine uncertainty about purchases, (c) will download a dedicated app for it, and (d) will bother completing an onboarding taste profile before their first scan — this Venn diagram might have 40,000 people globally. Physical bookstore traffic is declining every year. You're building for a niche inside a shrinking market.
- The onboarding is a conversion killer disguised as a feature. Before you get a single verdict, users must name 5-10 books they loved, select genres, and flag themes to avoid. The most valuable scanning moments are impulsive — you pick up a random book that catches your eye in-store and want an answer *right now*. Having to set up a profile first obliterates that impulse. Most users won't finish onboarding. The core value is locked behind a wall that will kill 70%+ of installs before they see anything useful.
- ChatGPT is a zero-friction competitor that requires no download. Open ChatGPT, type "I loved Sapiens and thinking about buying [book name] — will I like it?" and you get a personalized verdict in seconds with no setup. Your only advantage is the barcode scan removing typing friction. Is one UX improvement worth an entire app?
- The LLM verdict quality will be embarrassingly inconsistent. The AI is only as good as the Google Books description it receives. For bestsellers and classics, those descriptions are decent. For the long tail of books — small press, older backlist, niche nonfiction, international editions — the description is 2 sentences of back-cover marketing fluff. That's exactly where the user most needs help (they've already heard of the big books). When the verdict for an obscure book is "This sounds like it could be interesting based on your preferences" because there's no real data, trust collapses fast.
- The taste profile is too shallow to generate genuinely personalized verdicts. "I loved Sapiens" tells the LLM almost nothing specific. Did they love it for the prose? The scope? The contrarianism? The cognitive science angle? A handful of book titles plus genre chips creates the illusion of personalization more than the reality. The LLM will mostly do generic book recommendations dressed up as personalized insights.
- Monetization is basically nonexistent. $2.99 one-time purchase means a user can't experience the value before paying (first scan requires a completed profile, which requires commitment). Free with scan limit means you hit a paywall at exactly the worst moment — standing in a bookstore with a book in your hand. Subscription for a low-frequency use case is nearly impossible to justify. There is no good pricing model here.
- Use frequency is too low to build a habit. Most people visit physical bookstores once a month at best, probably once a quarter. Low frequency means high churn. The wishlist helps but a "bookstore companion" app that only gets opened 4-12 times a year is going to get deleted during the next phone cleanup.
- StoryGraph is an under-acknowledged competitor. It already has personalized book recommendations based on your read history, mood, and themes. Its users are exactly the "bookworm who cares about whether they'll like a book" demographic. They haven't nailed the in-store scanning moment, but they have the data moat this app is trying to build from scratch.
- No distribution strategy whatsoever. "AI book scanner app" is not a search term people use. The App Store is a graveyard for niche utility apps. How does anyone find this? There's no growth loop, no sharing mechanism, no SEO surface, no community angle. Build it and they will not come.

Key Questions:
- What percentage of your target users will complete the onboarding taste profile on first launch? If it's under 40%, the entire product premise is broken — can you validate this before building?
- If the user already knows the book title (they're holding it), why not just ask ChatGPT directly? What specifically does the barcode scan UX save them versus typing a title?
- What's your answer when Google Books returns a 40-word description for a book? Is a verdict based on that actually useful or just LLM noise?
- What does the taste profile actually learn? You said "manual profile edit only in v1" — so after 50 scans and 20 wishlist saves, the profile is still whatever the user typed at onboarding? That's a massive miss on the compounding value angle.
- How do you get your first 100 users and what does the retention curve look like at 30 days?

Suggestions:
- Solve the onboarding problem first or kill the product. Consider letting users scan books immediately with zero setup, and build the taste profile *implicitly* from what they save vs. skip. "You saved 3 narrative nonfiction books and skipped 4 thrillers — updating your preferences." This is a dramatically better UX and the personalization improves over time without front-loaded effort.
- Alternatively, let users import a Goodreads or StoryGraph shelf as their taste profile. Power users already have this data. One OAuth import beats 10 minutes of manual entry and produces far better personalization.
- Before writing a line of code, do this: test 20 books from different categories against the Google Books API. Check the description quality. If more than 30% return descriptions under 100 words, the verdict quality problem is real and the product premise is shakier than it looks.
- Rethink the monetization before launch. The most viable model might be: free for first 20 scans, then $4.99 one-time. This lets users experience the value loop completely before paying and avoids the subscription trap for a low-frequency app.
- The actual killer feature you're underselling: the wishlist as a price tracker. After scanning in-store, show if the book is cheaper on Amazon/Kindle. That's immediately useful, not dependent on taste profile quality, and it's a concrete save-money value prop. You explicitly ruled this out but it might be what actually drives downloads.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? YES — the technical scope is honest and tight. Barcode scanning (expo-barcode-scanner or react-native-vision-camera), Google Books API call, LLM API call, local storage for wishlist and taste profile — this is a legitimate 10-14 day build with Expo/React Native and AI coding assistance.
- Biggest solo complexity traps:
  - Camera permission handling and barcode scanner library quirks on both iOS and Android simultaneously (expo-barcode-scanner has known issues with some Android devices; testing matrix is larger than it looks)
  - Onboarding autocomplete for book search (Google Books typeahead introduces debouncing, loading states, and edge cases that eat 2-3 days of polish)
  - LLM response latency in real bookstore conditions on 4G/LTE — 2-4 seconds in testing, potentially 8-12 seconds on spotty signal inside a store with thick walls; needs a timeout + fallback UX
  - Google Books API rate limits are undocumented and can surprise you at scale
  - App Store review for camera-using apps takes longer and sometimes requires justification
  - "Book not found" is not an edge case — it will happen 15-25% of the time for niche books, and that fallback UX is more work than the happy path

Design Handoff:
- Screens needed:
  - Onboarding Screen 1: Welcome / value prop ("Your personal bookstore advisor")
  - Onboarding Screen 2: Add books you loved (search + autocomplete, add up to 10)
  - Onboarding Screen 3: Genre preferences (multi-select chips)
  - Onboarding Screen 4: Themes to avoid (multi-select chips)
  - Scanner Screen: Camera viewfinder with barcode reticle, instruction text, manual search fallback, bottom nav (Scanner | Wishlist | Profile)
  - Loading/Verdict Generation Screen: Book cover + title + author + animated "Analyzing your taste..." message
  - Book Verdict Screen: Large YES/MAYBE/SKIP badge (green/yellow/red), book cover + title + author, one-sentence personalized reason, star rating, 2-3 sentence synopsis, "Save to Wishlist" CTA, "Scan Another" CTA, expandable "See full summary"
  - Save to Wishlist Sheet: Modal/bottom sheet — book thumbnail + title, optional text note, Save and Skip buttons
  - Wishlist Screen: List of saved books with cover, title, author, verdict badge, note preview, date; tap to re-open verdict; swipe to delete; empty state
  - Taste Profile Screen: Editable "Books I love" list, editable genre chips, editable avoid-themes chips, Save button
  - Book Not Found Screen: Error message, pre-filled title search field, Search button, "Scan different book" link
  - Settings Screen: API usage indicator (if scan-limited), account/data options, about

- Key UI interactions:
  - Barcode scan auto-triggers on detection — no button tap needed
  - Verdict screen has strong visual hierarchy: verdict badge is the first thing the eye sees, reason is second, everything else is secondary
  - "Scan Another" returns directly to scanner with camera already active — zero friction loop for browsing a shelf
  - Wishlist verdicts are cached locally — no API call on re-open
  - Taste profile changes do NOT retroactively update saved verdicts (would confuse users)
  - Onboarding is skippable after 3 loved books are added (surface a "skip for now" link early)

- Data each screen needs:
  - Scanner: camera access, user taste profile (loaded in background)
  - Loading: book ISBN → Google Books API response (title, author, cover URL, description)
  - Verdict: book metadata + taste profile + LLM verdict response (verdict: YES/MAYBE/SKIP, reason: string)
  - Wishlist: local array of saved books [{isbn, title, author, coverUrl, verdict, reason, note, savedAt}]
  - Taste Profile: local object {lovedBooks: [], genres: [], avoidThemes: []}
  - Book Not Found: partial ISBN or empty, search query string
