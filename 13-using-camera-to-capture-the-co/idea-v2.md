# Idea #13: "Will I Like This?" — AI Book Verdict Scanner (Improved)

## Version: v2
**Date:** 2026-03-25 21:12
**Status:** improved

## Original Idea
Using camera to capture the cover of book on bookstores to read the summary, reviews... before buying, save money and lot of time for buying wrong book

## What Changed and Why

- **Killed the lookup feature entirely.** Google Lens, Amazon, and Goodreads already answer "what is this book" perfectly. Building that is rebuilding a solved problem. The pivot is from "what is this book" to "will I personally like this book" — a question those apps never answer well because they don't know you.

- **Added a personal taste profile as the core product layer.** The real user problem isn't lacking information about books — it's the cognitive load of deciding whether a specific book matches your specific taste. An AI verdict engine that synthesizes reviews + your stated preferences into a 10-second YES/NO/MAYBE verdict with a one-sentence reason is genuinely differentiated. No existing app does this for in-store moments.

- **Switched from cover recognition to barcode scanning.** The critique correctly identified that cover recognition is unreliable in real bookstore lighting. Barcode scanning is deterministic, works in any conditions, and ISBN barcodes are on every book. Google Books API is free, requires no approval, and returns metadata + description reliably from ISBNs. This removes the biggest technical trap.

- **Added a "Bookstore Wishlist" as the retention hook.** The original idea has zero retention — you use it once and forget it. The wishlist (scan → save with a note → decide later) creates a habit loop for every bookstore visit. It also builds up your taste data over time, making the AI verdict smarter. The app becomes your bookstore companion, not a one-shot lookup tool.

- **Cut social and sharing features.** The critique mentioned a social "spotted this" angle, but that requires network effects a solo dev can't bootstrap. Keeping it personal-only keeps scope tight and the value proposition clear.

## Improved Description

A mobile app for people who regularly browse physical bookstores but hate second-guessing their purchases. You build a quick taste profile when you onboard (books you loved, genres you like, themes you actively avoid). Then in the bookstore, you scan any book's barcode. The app fetches the book's metadata and description from Google Books API, runs it through an LLM that knows your taste profile, and returns a verdict: YES / MAYBE / SKIP — with a one-sentence reason like "You loved Sapiens — this covers the same cognitive science territory but goes deeper on neuroscience, which you flagged as a favorite theme."

Books you're considering get saved to your Bookstore Wishlist with your own notes. The list persists so you can come back later, compare options, and track what you actually bought vs. what you skipped. Over time, the taste profile improves as you rate books you actually read.

The differentiation: this is not a lookup tool. It's a personal reading advisor that meets you at the moment of decision, in the store, with a direct answer calibrated to who you are as a reader.

## Why This Is Worth Building (Solo + AI)

The technical stack is straightforward — barcode scanner, Google Books API (free), and an LLM API call — all proven tools a solo dev can wire together in days with AI coding assistance. The retention mechanism (wishlist + growing taste profile) is built into the core loop, not bolted on. The differentiation (personalized verdict) is real and isn't eroded by Google Lens because Google Lens doesn't know you.

## Solo MVP Scope

- **What's in v1:**
  - Barcode scanner (camera) → ISBN lookup via Google Books API
  - Onboarding taste profile: 5-10 books you loved, 3 genres you like, 3 themes you avoid
  - AI verdict screen: YES / MAYBE / SKIP + one-sentence personalized reason (LLM call using taste profile + book description)
  - Bookstore Wishlist: save scanned books with optional personal note
  - Wishlist view: list of saved books, tap to see verdict again

- **What's out of v1:**
  - Cover photo recognition (barcodes only — covers are unreliable)
  - Social/sharing features (network effects trap for solo dev)
  - "Read later" tracking or reading log (separate app category)
  - Offline mode (require network — adds weeks of complexity)
  - Taste profile auto-learning from ratings (manual profile edit only in v1)
  - Price comparison or "buy cheaper online" features (that's Google Lens's job)

- **Estimated build time with AI coding tools:** 10-14 days

## Open Questions

- Which LLM API to use — Claude API gives best reasoning quality for the verdict synthesis; OpenAI is also viable. Cost per scan needs to be modeled (likely under $0.01/scan at Claude Haiku or GPT-4o-mini pricing).
- Monetization: one-time purchase ($2.99) or free with a scan limit then subscription? Needs validation before launch.
- Does Google Books API return enough description text to make the verdict meaningful for niche or older books? Worth testing against a sample of 20 books before committing.
- iOS or Android first? iOS users over-index on bookstore browsing behavior and are more likely to pay. Start iOS.
- What happens when the barcode scan returns no result (self-published, international editions)? Need a fallback UX — "Book not found, search by title instead."

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding — Taste Profile Setup**
  Purpose: capture user's reading preferences before first scan. Key elements: title "What kind of reader are you?", input field to add books they loved (autocomplete via Google Books API), multi-select chips for genres (Fiction, Non-fiction, History, Science, Biography, Self-help, etc.), multi-select chips for themes to avoid (e.g. graphic violence, heavy romance), "Start scanning" CTA button. Shows 3 steps max.

- **Screen 2: Scanner**
  Purpose: main camera view for scanning barcodes. Key elements: camera viewfinder with barcode targeting reticle, "Point at the barcode on the back of the book" instruction, manual search fallback link ("Can't scan? Search by title"), bottom nav bar (Scanner | Wishlist | Profile).

- **Screen 3: Loading/Verdict Generation**
  Purpose: brief interstitial while fetching book data and generating AI verdict. Key elements: book cover thumbnail (from Google Books), book title + author, animated "Reading your taste profile..." message. Duration: 2-4 seconds.

- **Screen 4: Book Verdict**
  Purpose: show the personalized YES / MAYBE / SKIP verdict. Key elements: large verdict badge (color-coded: green/yellow/red), book cover + title + author, one-sentence personalized reason ("You loved X — this is similar because..."), star rating aggregate from Google Books, short synopsis (2-3 sentences), two CTAs: "Save to Wishlist" and "Scan Another". Secondary: "See full summary" expand.

- **Screen 5: Save to Wishlist**
  Purpose: save the book with an optional personal note. Key elements: book thumbnail + title, text input "Add a note (optional)" — e.g. "on sale, looks interesting", "Save" and "Skip" buttons. Quick modal/sheet, not a full screen.

- **Screen 6: Wishlist**
  Purpose: browse all scanned/saved books. Key elements: list of saved books with cover thumbnail, title, author, verdict badge (YES/MAYBE/SKIP), personal note preview, date saved. Tap row to see full verdict again. Swipe to delete. Empty state: "Scan your first book to start your list."

- **Screen 7: Taste Profile (Settings)**
  Purpose: view and edit reading preferences. Key elements: "Books I love" list (editable), genre preferences chips (editable), themes to avoid chips (editable), "Save changes" button. Accessed from bottom nav or settings icon.

- **Screen 8: Book Not Found**
  Purpose: fallback when barcode returns no result. Key elements: "We couldn't find this book" message, search field pre-filled with any text detected, "Search" button, "Scan a different book" link.

**Key interactions:**
- Scanner → (scan barcode) → Loading → Verdict: primary happy path, takes ~3 seconds
- Verdict → "Save to Wishlist" → Save Sheet → Wishlist: capture flow
- Verdict → "Scan Another" → Scanner: fast loop for browsing a shelf
- Wishlist → tap book → Verdict (cached): review past decisions
- Bottom nav connects Scanner, Wishlist, and Profile at all times
- Onboarding runs once on first launch, skippable after providing at least 3 loved books

### Critique

**Verdict: WORTH BUILDING — Score: 6/10**

The v2 pivot from "lookup tool" to "will I personally like this" is legitimate differentiation and the scope discipline is solid. Technical stack (barcode scanner + Google Books API + LLM call + local wishlist) is genuinely shippable by a solo dev in 10-14 days.

The brutal problems: target audience is tiny (frequent physical bookstore shoppers who will complete an onboarding taste profile — maybe 40,000 people globally); the onboarding is a conversion killer that gates value behind effort at exactly the wrong moment; ChatGPT with a typed title is a zero-download competitor; Google Books descriptions for niche/backlist books are too shallow for meaningful AI verdicts; and there's no distribution strategy. Monetization is also genuinely hard for a low-frequency use case.

Worth building as a focused side project with a realistic ceiling. Not a business without solving the onboarding friction and finding a distribution angle.

## Version History
- v1: Original idea — camera scan book to read summary/reviews before buying
- v1.1: Critique — already solved by Google Lens/Amazon/Goodreads; no moat, no retention, no distribution
- v2: Improved — pivoted from "what is this book" lookup to "will I like this book" AI verdict engine with personal taste profile and bookstore wishlist for retention
