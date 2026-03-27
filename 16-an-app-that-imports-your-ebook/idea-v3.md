# Idea #16: BookScan — AI Shelf Companion (Improved)

## Version: v3
**Date:** 2026-03-25 22:44
**Status:** improved

## Original Idea
an app that imports your ebook metadata (titles, authors, genres) to build a personal taste profile, then uses AI + Goodreads reviews to generate personalized book previews when you scan a book at bookstores, helping you decide if it's worth buying

## What Changed and Why

- **Removed the wizard as a prerequisite — "scan first, personalize later"**: The v2 design required rating 10 books and tagging each one before getting any value. The critique correctly identified this as a catastrophic drop-off risk. The fix: the app works on first open with zero setup. Scan a book → get a verdict. Claude uses a default profile ("general literary fiction reader") until the user opts into personalization. After each verdict, a single prompt appears: "Add your taste to improve this." This is a progressive commitment model, not an upfront tax.

- **Stopped lying about data depth — reframe Claude as the product, not "AI + reviews"**: Google Books returns publisher blurbs and genre tags. That's it. Open Library is thinner. There are no review summaries, no "comparable titles" feature, no Goodreads data. The v2 description implied richer sourcing than exists. The fix: lean into what Claude actually does well — reasoning from thin data. The verdict card is reframed as "Claude's take based on what publishers say + your stated taste" not "personalized from reviews." Honesty about the data source prevents trust destruction when users notice the verdicts cite book jacket copy.

- **"No typing. Open. Scan. 30 seconds." is the one competitive differentiator — build the whole UX around it**: ChatGPT can answer "should I read this?" better than this app IF the user types. The only moat is the camera-first, zero-typing experience. Every screen decision now optimizes for that: scanner is the home screen (not a CTA away), verdict loads in under 10 seconds, no login required ever. If a user has to type before scanning, the product has failed its core promise.

- **iOS Safari barcode scanning is a go/no-go gate, not a footnote**: The critique flags this as a potential week-eater. The fix: this is build day one. If `getUserMedia` + a WASM barcode library (ZXing-wasm or Dynamsoft) works reliably on iOS Safari 17+, proceed. If it doesn't, the fallback is `<input capture="environment">` which opens the native camera — slower UX but still functional. Decision is made before any wizard or verdict UI is touched. The build plan reflects this reality.

- **Monetization is baked in from v1**: 15 free scans total (not per month — a lifetime trial), then $2.99/month unlimited. This is decided now, before the API bill surprises anyone. Claude API cost per scan is roughly $0.01–$0.03. At $2.99/month with 100 paying users, margin is healthy. The free tier is a trial, not a perpetual free plan. Paywall appears on scan #16, with a count shown from scan #1 ("3 of 15 free scans used").

- **Local storage is fine for v1 — but add profile export as a safety valve**: The critique is correct that Safari aggressively clears local storage. The fix isn't adding accounts (that's months of scope). The fix is a one-tap "Export my profile" button that downloads a JSON file the user can re-import later. This is a 2-hour build that prevents the trust-destroying data loss scenario. It's not elegant, but it's honest and functional.

## Improved Description

BookScan is a PWA you open while standing in a bookstore. Point the camera at a book's barcode. In 10 seconds, Claude tells you whether it's worth buying — based on Google Books metadata and whatever taste preferences you've told it. No account. No setup required. No typing.

The app works immediately on first open: scan any ISBN, Claude returns a verdict using a default "curious general reader" profile. After the verdict, a gentle prompt asks "Want better verdicts? Tell me what you love." One tap opens a 60-second tag picker (not a 10-book wizard — just "pick 5 words that describe your reading taste"). That profile is stored locally and attached to every future scan.

The verdict card is honest about its sources: "Based on the publisher description and your stated taste for [tags], here's Claude's take." Three sentences: what the book is actually about (beyond jacket copy), why someone with your taste would love it, one honest watch-out. No hallucinated review quotes. No fake "comparable titles" from an API that doesn't have them.

The scanner is the home screen. Not behind a button — the camera is live the moment the app opens. Wishlist saves books locally. Scan history is cached by ISBN so re-scanning the same book returns instantly. After 15 scans, a paywall: $2.99/month unlimited. Profile export available in settings so data loss is survivable.

## Why This Is Worth Building (Solo + AI)

The camera-first, zero-setup UX is genuinely differentiated from ChatGPT — not because the AI verdict is better, but because the interaction requires no typing and works in 10 seconds while your hands are full holding a book. The core loop (scan → API → Claude → verdict) is a weekend prototype with AI coding tools, and every week after that is layering UX on a working foundation. The monetization decision is made upfront, so this doesn't become a hobby with an API bill.

## Solo MVP Scope

- **What's in v1:**
  - PWA, no native app
  - Scanner as home screen (ZXing-wasm or file-capture fallback depending on iOS Safari test result)
  - ISBN → Google Books API → Claude verdict (3 sentences + one watch-out)
  - Default "general reader" profile works with zero setup
  - Optional 60-second tag picker (5 tags, no book rating wizard)
  - Verdict cached by ISBN + profile hash (localStorage)
  - Wishlist (localStorage)
  - Scan history (localStorage)
  - Profile export to JSON (import from JSON)
  - Scan counter visible from scan #1, paywall at scan #16
  - Stripe payment link for $2.99/month (not a full subscription integration — a Stripe payment link that gives user a code to unlock)

- **What's out of v1:**
  - Accounts / auth / backend database
  - 10-book rating wizard
  - "Comparable titles" feature (the data doesn't exist)
  - Review aggregation from any source
  - Price tracking
  - Social features / sharing
  - Native iOS/Android app
  - Full Stripe subscription management (use payment link + unlock code instead)
  - Email capture or notifications

- **Estimated build time with AI coding tools:** 2–3 weeks
  - Day 1: Test iOS Safari barcode scanning. Go/no-go on WebRTC vs file-capture. Build nothing else.
  - Week 1: Core loop only. Scanner → ISBN → Google Books → Claude → verdict card. Hardcoded taste profile.
  - Week 2: Tag picker, profile persistence, cache, wishlist, scan history.
  - Week 3: Paywall (Stripe payment link + unlock code), profile export/import, polish, edge cases (book not found, camera denied, API errors).

## Open Questions

- Does ZXing-wasm actually work on iOS Safari 17+ with acceptable latency? If not, does the `<input capture>` fallback feel fast enough to be usable? This is the first answer needed.
- What does a real Google Books API response look like for a niche/older book? What % of physical bookstore inventory has adequate Google Books coverage? Test with 20 real ISBNs before building the verdict UI.
- Does the 3-sentence Claude verdict feel useful when it's based only on publisher blurb + 5 taste tags? Build the prototype and ask 5 real readers. If the verdict isn't impressive, the prompt strategy needs rethinking before building the wizard.
- Stripe payment link + unlock code: is this too clunky for users? Alternative is no monetization in v1 and a scan cap with a waitlist for "paid tier coming soon." Decide before week 3.
- What's the Google Books API quota at free tier (1000 req/day) and does caching by ISBN reduce that enough for early traction?

## Design Handoff

List every screen/view the app needs:

- **Screen 1: Scanner (Home)** — The default screen. Full-screen camera feed with a centered barcode reticle overlay. Scan counter badge top-right ("12 of 15 free"). Settings gear icon top-left. "Enter ISBN" text link bottom-center as fallback. No other UI. Camera activates on app open.

- **Screen 2: Loading / Verdict Generation** — Appears immediately after ISBN detected. Shows book cover (large, from Google Books), title, author below it. Animated "Claude is reading..." indicator. Takes 3–8 seconds. User can cancel (back arrow).

- **Screen 3: Book Verdict (money screen)** — Book cover top-third. Title + author. Three-sentence verdict in large readable type. Below that: "Why you'd love it" (1–2 bullets) and "Watch out for" (1 bullet). Bottom action bar: Save to Wishlist / Scan Another / Share. Small footnote: "Based on publisher description + your taste tags." Tap verdict to expand to full Claude response.

- **Screen 4: Paywall** — Appears on scan #16. Minimal: "You've used your 15 free scans." One CTA: "Unlock unlimited — $2.99/mo" (Stripe payment link). Secondary: "Enter unlock code" (for users who paid). Back link to browse wishlist without scanning.

- **Screen 5: Taste Setup (optional, 60 seconds)** — Accessible from verdict screen ("Improve future verdicts →") and from Settings. Single scrollable grid of ~30 taste tags ("slow burn", "unreliable narrator", "plot-driven", "character-driven", "dark", "cozy", "literary", "genre fiction", etc.). User taps up to 8. CTA: "Save my taste." No book rating required. Can be skipped or abandoned at any point.

- **Screen 6: Wishlist** — 2-column grid of saved books. Cover image + title. Tap to view cached verdict. Swipe-to-remove. Empty state: "Scan a book to save it here."

- **Screen 7: Scan History** — Scrollable list. Cover thumbnail + title + author + scan date + wishlist icon. Tap row → verdict (from cache). Empty state. Pull to refresh does nothing (no backend), just visual confirmation.

- **Screen 8: Settings** — Taste profile summary (tag cloud of selected tags, tap to edit → Screen 5). Scan count ("12 of 15 free scans used" or "Unlimited"). Export profile (downloads JSON). Import profile (upload JSON). Reset profile (destructive, confirmation dialog). Unlock code entry field.

- **Screen 9: Manual ISBN Entry** — Simple: number input field, "Look up" button, loading state, error state ("Book not found — try scanning again or check the ISBN"). Back link. Auto-formats ISBN as typed.

- **Screen 10: Error — Book Not Found** — Shown when ISBN has no Google Books result. "We couldn't find this book." Two options: Try Manual Entry / Scan Another Book. Soft suggestion: "Older or niche books may not have coverage."

- **Screen 11: Error — Camera Permission Denied** — Full-screen explainer. Illustration. "BookScan needs camera access to scan barcodes." Step-by-step instructions for iOS and Android to enable. "Try Manual Entry Instead" CTA.

**Key interactions:**

- App opens → camera live immediately (no splash, no onboarding, no wizard gate)
- ISBN detected → auto-transition to Loading screen (no tap required)
- Verdict loads → tap "Save to Wishlist" → icon fills, count updates in top-right of Scanner
- Verdict screen → tap "Improve future verdicts" → Taste Setup (optional, dismissable)
- Scan #16 → Paywall interrupts before Loading screen
- Settings → Export profile → system share sheet with JSON file
- Settings → Import profile → file picker → replaces current tags (confirmation dialog)
- Wishlist/History → tap row → Verdict screen (loaded from localStorage cache, instant, no API call)
- Verdict screen → tap full response expand → Claude's full text in a scrollable modal
- Taste Setup → save → immediate toast "Taste saved — next verdict will be more personal"

**Data each screen needs:**

- Scanner: scan count (for badge), wishlist count (for badge)
- Loading: ISBN, book metadata from Google Books (title, author, cover URL, description, genre tags)
- Verdict: book metadata + Claude verdict text (3 sentences) + bullet arrays + taste tags used (for footnote)
- Paywall: scan count
- Taste Setup: hardcoded tag list (~30 tags), user's currently selected tags
- Wishlist: filtered scan history (wishlist flag = true), verdict cache by ISBN
- Scan History: all scanned entries (ISBN, title, author, cover, timestamp, wishlist flag)
- Settings: taste tags list, scan count, subscription status (unlocked bool in localStorage)
- Manual ISBN Entry: text input state, error/loading/success states
- Errors: static content only

## Version History
- v1: Original idea — ebook metadata import + Goodreads API + bookstore scanning
- v2.1: Critique — wizard friction is a drop-off killer; Google Books data is thinner than described; no moat vs ChatGPT typing; iOS Safari scanning is a real risk; no monetization; local storage data loss unaddressed
- v3: Improved — scanner is the home screen (zero setup to first value); wizard replaced by optional 60-second tag picker; verdict reframed as "Claude's take" not "AI + reviews"; iOS Safari test is build day one; $2.99/month paywall at scan #16; profile export as data loss safety valve
