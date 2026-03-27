Verdict: NEEDS MAJOR WORK
Score: 5/10

What's Actually Good:
- The core use case is real and relatable — standing in a bookstore wondering "is this worth $30?" is a universal friction point
- Ebook metadata as a taste profile seed is clever; you already have implicit signal from what you've bought/read
- Personalized AI summary beats generic blurbs on the back cover — this is genuinely differentiated
- Bookstore moment is high-intent: user is already primed to buy, they just need a nudge or veto
- ISBN barcode scanning is a solved technical problem (phone camera + open ISBN databases)

Brutal Feedback:
- Goodreads is a graveyard for developers. Amazon killed the public API in 2020. You're not getting structured review data from Goodreads without scraping, which is legally gray and will break constantly. This is the core of your product and you don't have a real data source.
- "Personalized preview" from scraped reviews is just a fancy summary. ChatGPT already does this for free if you paste a book title. Why does someone need an app for this?
- The taste profile from ebook metadata is shallow. Titles and authors tell you genre at best. It won't distinguish "I love dark psychological thrillers" from "I bought this as a gift." Kindle/Apple Books don't expose reading behavior — only purchase history.
- Who is the target user? People who buy ebooks AND shop at physical bookstores? That's a pretty small Venn diagram. Ebook buyers are migrating away from physical stores. Physical bookstore shoppers often skew toward people who don't use ereaders much.
- You're building a product entirely dependent on data you don't control: ebook platform metadata export (varies by platform), Goodreads reviews (no API), AI inference. One policy change kills a leg of the stool.
- The "generate personalized preview" step requires knowing enough about the user's taste to say something meaningful. With just metadata, you get "you like sci-fi, here's a sci-fi summary." That's not personalized — that's just genre filtering.
- Open Library and Google Books APIs exist but their review/community data is thin. You'll end up with AI hallucinating fake opinions about a book.
- No retention mechanism. You use it in a bookstore, occasionally. There's no daily or weekly engagement loop. Hard to build a business on episodic in-store usage.
- Kindle metadata export requires users to dig through Amazon's privacy data download, which is a multi-step bureaucratic nightmare most users will abandon.

Key Questions:
- What's your actual source for book reviews if not Goodreads? Google Books has some. Amazon reviews are also locked. Where does the "AI + Goodreads reviews" part actually come from?
- How does the taste profile become more sophisticated over time? Or is it static after the initial import?
- Is the MVP just "scan ISBN → get AI summary tuned to your genre preferences"? Because that's a much simpler and more honest framing.
- Have you talked to anyone who shops at physical bookstores regularly? Do they actually have this pain, or do they just Google the book already?
- What does "import ebook metadata" look like in practice for someone on Kindle? Kobo? Apple Books? Each is a different integration nightmare.

Suggestions:
- Strip the taste profile complexity for v1. Just let users manually rate 10 books they've loved (like a setup wizard). Skip the metadata import entirely — it's a UX cliff for most users.
- Pivot the data source: use Open Library + Google Books + manually seeded review excerpts + AI. Don't promise Goodreads if you can't deliver it.
- Make the core loop dead simple: scan barcode → get a 3-sentence "why you'd like this / why you might not" based on your stated preferences. That's shippable.
- Consider a web app instead of native mobile — easier to iterate, no App Store delays, and bookstore scanning via phone browser camera is possible.
- Add a "save for later" wishlist so the app has some retention value outside the store.
- Look at what StoryGraph does — it's a Goodreads alternative with actual API ambitions and community data.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — but only if you dramatically scope down. The MVP of "scan ISBN + AI summary tuned to stated genre preferences" is 2-4 weeks. The full vision (ebook import, taste profiling, Goodreads integration, personalized previews) is 2-4 months minimum, and that's if the data access problems magically solve themselves.
- Biggest solo complexity traps:
  - Goodreads data access: no API, scraping is fragile and ToS-violating, this alone can kill the project
  - Ebook metadata import: different for every platform (Kindle, Kobo, Apple Books), each requires a separate integration or manual export dance
  - Taste profile ML/AI: making personalization actually feel personalized (not just genre-matching) requires either a lot of user data or a sophisticated prompt engineering layer — both take time to get right
  - Barcode scanning on mobile: works fine with react-native-camera or a web library, but you're building a mobile app which adds deployment overhead
  - Cold start problem: new users have no taste profile, so the first scan gives them nothing useful — you need a setup flow that doesn't feel like homework
