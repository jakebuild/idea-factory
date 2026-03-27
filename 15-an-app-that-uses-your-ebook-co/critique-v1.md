Verdict: NEEDS MAJOR WORK
Score: 5/10

What's Actually Good:
- The bookstore scan-to-decide use case is genuinely useful — people really do stand in bookstores unsure whether to buy
- Combining personal ebook library context with point-of-sale decisions is a novel angle nobody else is doing
- Affiliate link monetization is simple and proven — no payment infra needed, just Amazon Associates signup
- AI summarization and mind map generation from existing ebook files is technically feasible with current LLMs
- The offline-first bookstore context (no wifi often) makes cached previews from your own library feel smart

Brutal Feedback:
- The core premise is legally radioactive. Using the full text of ebooks you "own" to generate summaries is a massive copyright grey zone. Amazon, publishers, and DRM enforcement will not care that you "own" the ebook — you don't own the right to run it through an AI pipeline and redistribute the output. One cease-and-desist kills the product entirely.
- "Your ebook collection" assumes people have massive libraries of ebooks in accessible formats (EPUB, PDF). The average person's library is either on Kindle (DRM-locked, inaccessible), Apple Books (DRM-locked), or Audible (audio). The actual addressable user base of people with large, DRM-free ebook collections is tiny and skews heavily toward pirates.
- The value proposition is backwards. The books you already own are not the books you're deciding to buy. Someone scanning a book at a bookstore is encountering something new. Your personal library is unlikely to contain that exact book or closely related books at meaningful depth.
- Mind maps are a feature nobody actually asks for. Summaries? Yes. Mind maps require rendering complexity, look impressive in demos, and get ignored in real use. It's scope bloat masquerading as a feature.
- The scan-to-result flow needs to be near-instant in a bookstore context. Generating a rich preview (summary + mind map + review) from ebook content on-device or via API in real time is not fast. Users standing in aisles will abandon after 5 seconds.
- Affiliate links for books pay garbage. Amazon Associates pays 4.5% on books. The average book is $15-30. You're making $0.67-$1.35 per conversion, with likely <5% conversion rate on scans. The math on this as a business is brutal.
- Why would someone buy the physical book via your affiliate link after scanning it at a physical bookstore where they can just... buy it? The affiliate conversion moment is confused. You're in the store. The book is right there.
- This competes directly with Goodreads (which users already have on their phones), Google Books (free preview), and the bookstore's own app. The "personal library" angle is the only differentiator and it's legally and technically shaky.
- No mention of how books are scanned — barcode? ISBN? Camera OCR of title? Each has friction and failure modes that need real engineering work to get right.

Key Questions:
- What format are these ebooks in, and how does the app access them? This is the entire technical foundation and it's a black box.
- Does the AI generate summaries FROM the user's ebook text, or from general training data? If from ebook text, copyright. If from training data, why do you need the ebooks at all?
- What's the actual moment someone reaches for this app vs. just Googling the book title?
- How does this handle the 90% of books in a bookstore that the user does NOT own as an ebook?
- Who is the specific user? Serious readers with large EPUB libraries who also browse physical bookstores? That's an extremely narrow Venn diagram.

Suggestions:
- Pivot the "personal library" angle: instead of using ebook text (copyright nightmare), use reading history and ratings from Goodreads/StoryGraph API to personalize recommendations for scanned books
- Drop mind maps entirely — ship with just summary + similar books you've liked + buy/skip recommendation
- The real product: a "should I buy this?" scanner that gives a personalized score based on your taste profile, not your ebook content. Much simpler, no copyright issues, faster to MVP
- If you keep the ebook angle, narrow to public domain books only (Project Gutenberg) — no copyright risk, but also niche
- Affiliate link play only works at scale; focus first on engagement, not monetization

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? NO — The description sounds like 2-4 weeks but the actual scope is not. Ebook parsing (EPUB/PDF handling with DRM detection), barcode/ISBN scanning, AI summarization pipeline, mind map rendering, affiliate link integration, and a polished bookstore-grade UX is 6-8 weeks minimum even with AI help, and that's before hitting the legal wall.
- Biggest solo complexity traps:
  - Ebook access layer: every format (EPUB, MOBI, PDF, Kindle AZW) needs a different parser; DRM-protected files need to be gracefully rejected without breaking the app
  - Real-time generation: making AI summary + mind map feel fast in a bookstore (spotty wifi, impatient user) requires aggressive caching, pre-generation, or on-device models — none of which are trivial
  - Mind map rendering: no great off-the-shelf mobile mind map renderer; you'll spend a week just on this visual component that users don't really want
  - Copyright legal exposure: a solo dev has no legal team and no resources to fight a takedown — this risk is existential from day one
  - Affiliate integration: Amazon Associates has an approval process, API rate limits, and link-building rules that can get your account banned if you're not careful
  - ISBN/barcode scan reliability: needs camera permission handling, lighting edge cases, and fallback to manual title entry — more edge cases than expected
