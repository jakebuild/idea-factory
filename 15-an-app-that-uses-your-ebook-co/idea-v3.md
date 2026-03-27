# Idea #15: BookScan — "Should I Buy This?" Personalized Book Scanner (Improved)

## Version: v3
**Date:** 2026-03-25 22:37
**Status:** improved

## Original Idea
an app that uses your ebook collection to generate rich previews (summaries, mind maps, reviews) for books users scan at bookstores, helping them decide before buying, with affiliate links to purchase the real book

## What Changed and Why

- **Replaced Goodreads OAuth/API with Goodreads CSV export as the primary data source.** The Goodreads public API has been closed to new developers since 2020. It is not a grey zone — it is a graveyard. Building the onboarding flow on a dead API is a launch blocker disguised as a question mark. Goodreads does provide an official CSV export of your entire library (ratings, dates read, shelves, reviews) — it's documented, legal, requires no API key, and actually contains *richer* data than the API ever exposed. The UX is uglier (user has to export and import a file), but it works on day one without negotiating with a dead API. StoryGraph also offers CSV export. Both are supported from launch.

- **Replaced taste-match percentage with a label + signal system.** "82% match" is a number Claude invents. Users with two similar books getting "82%" and "79%" will correctly conclude the app is making things up. Replaced with four plain-English labels: **Strong match / Decent match / Weak match / Not your genre** — derived from real, computable signals: genre overlap between the scanned book and the user's top-rated books, author adjacency (has the user rated this author or their comps highly?), and average rating patterns within similar genres. Claude writes the reasoning paragraph explaining *why*, but the label comes from a deterministic scoring function, not from asking the LLM to produce a number.

- **Raised the free tier from 10 to 30 scans/month, and changed the paid model to one-time purchase.** 10 scans is not a freemium model — it's a trial that expires mid-visit. A single bookstore session produces 15–30 books examined. The free tier must survive at least one real session or it creates uninstalls, not upgrades. More importantly, a $2.99/month subscription for an app used 2–4 times a month is a $36/year commitment for occasional use. The value-per-dollar perception is unfavorable compared to daily-use apps. A one-time purchase at $4.99 is easier to justify ("I'll buy it like I buy a coffee before browsing"), has no churn risk, and is actually more honest about how frequently the app will be used. Power users who scan frequently enough to hit 30/month are also the users most likely to pay $4.99 outright.

- **Added Google Books API as immediate fallback for book metadata.** Open Library is free and requires no API key, but its coverage of books published in the last 6–12 months is poor — and new releases are the primary use case for bookstore scanners. Barnes & Noble staff picks, NYT bestsellers, and new fiction are exactly the books people pick up and consider. Falling back to Google Books API (free tier, generous rate limits, excellent new-release coverage) when Open Library returns no data means the app works on day one in an actual bookstore, not just on a backlist title test.

- **Added cold-start onboarding for users with fewer than 20 ratings.** The CSV import path naturally segments users: people with a rich Goodreads library import it and get full personalization immediately. New users or people without a Goodreads account get a "Rate 10 books to start" genre-seeding flow — tapping thumbs up/down on a curated list of 20 well-known titles. This is a 60-second flow and produces enough signal for a meaningful first taste profile. It also has a secondary benefit: it doesn't require Goodreads at all, widening the addressable audience.

- **Made the Goodreads API path an optional enhancement, not a dependency.** If a future developer obtains a Goodreads API key (existing keys still technically function), the app can support OAuth login as a premium convenience. But this is not on the critical path. The CSV import ships first; OAuth is a v2 enhancement if demand warrants it.

## Improved Description

BookScan is a mobile app for people who browse physical bookstores and want a fast, personalized answer to "should I buy this?"

**Setup (one time, ~2 minutes):**
The user either (a) exports their Goodreads library as a CSV and imports it into the app, or (b) taps through a 60-second "rate these books" genre-seeding flow. Either path produces a taste profile. The app builds a local, cached representation of the user's reading preferences: top genres, favorite authors, rating patterns, and the books they've loved most. This runs once at setup and can be refreshed anytime.

**In the bookstore:**
Scan a barcode. BookScan looks up the book via Open Library (with Google Books as fallback), retrieves metadata (title, author, genre, themes, description, Goodreads average rating), then runs it through a two-step process: (1) a deterministic scoring function computes genre overlap, author adjacency, and rating-pattern similarity against the user's taste profile to produce a label; (2) a Claude Haiku call generates a short, personalized reasoning paragraph explaining the match. Total time: under 3 seconds.

**The result screen shows:**
- A verdict label: **Strong match / Decent match / Weak match / Not your genre**
- One bold sentence: the verdict ("You love slow-burn character studies; this is exactly that" or "You've consistently rated thrillers 3 stars or lower — skip this one")
- "Why this fits you" paragraph (from Claude, ~60 words)
- 2–3 books from the user's own history that are most similar, with their own star ratings shown
- Book metadata: cover, author, Goodreads average rating

**Monetization:**
Free tier: 30 scans/month. One-time unlock: $4.99 for unlimited scans forever. No subscription, no recurring charge. Simple.

## Why This Is Worth Building (Solo + AI)

The CSV import path removes the only launch blocker from v2 — the dead Goodreads API — and replaces it with a fully controllable flow that requires no external approval, no API key, and no ongoing dependency. The core loop (scan → lookup → score + Claude paragraph → display) is a well-understood mobile pattern that AI coding tools handle well. The deterministic scoring function is simple logic (genre array intersection, author lookup) — not ML — which means a solo dev can build and debug it without specialized expertise. The one-time purchase model removes churn complexity and is honest about usage frequency.

## Solo MVP Scope

- **What's in v1:**
  - Goodreads CSV import + StoryGraph CSV import
  - "Rate 10 books" cold-start onboarding flow (for users without a CSV)
  - Taste profile builder from CSV data (runs locally, cached in SQLite)
  - ISBN barcode scan using iOS Vision framework
  - Book metadata lookup: Open Library first, Google Books as fallback
  - Deterministic match label (genre overlap + author adjacency scoring)
  - Claude Haiku call for reasoning paragraph (uses pre-built taste profile summary)
  - Result screen: label, verdict sentence, reasoning, similar books from history
  - Scan history (last 50 results, cached locally)
  - Free tier: 30 scans/month
  - One-time unlock: $4.99 via RevenueCat + Apple IAP
  - Manual title/ISBN entry fallback

- **What's out of v1:**
  - Goodreads OAuth/API (add in v2 if CSV friction proves too high)
  - StoryGraph OAuth (same reason)
  - Android (iOS first)
  - Social/sharing features
  - Custom taste profile editing UI
  - Mind maps (cut permanently)
  - Affiliate links (cut permanently)
  - Push notifications

- **Estimated build time with AI coding tools:** 14–18 days
  - Days 1–2: CSV parser + taste profile data model + SQLite schema
  - Days 3–4: "Rate 10 books" onboarding flow + cold-start taste profile builder
  - Days 5–6: Barcode scan + Open Library + Google Books fallback
  - Days 7–8: Deterministic scoring function (genre overlap, author adjacency)
  - Days 9–10: Claude Haiku integration + prompt engineering for reasoning paragraph
  - Days 11–12: Result screen + scan history UI
  - Days 13–14: RevenueCat + IAP + free-tier gate
  - Days 15–18: Polish, edge cases, TestFlight, App Store submission prep

## Open Questions

- **Google Books API quota:** Free tier allows 1,000 requests/day per project. At launch this is fine; need to monitor if the app gets any traction. Is this enough for a soft launch?
- **CSV parsing robustness:** Goodreads exports are well-documented but can have encoding issues, missing fields, or books with no ISBN. How graceful is the import when fields are missing? Build for messy CSVs from day one.
- **Taste profile prompt size:** A user with 300+ rated books in their CSV produces a lot of signal. The taste profile summary sent to Claude Haiku needs to distill this into ~200 tokens without losing the personalization. How much prompt iteration is needed to get this right? Budget a full day.
- **Cold-start book list:** The "rate these 20 books" onboarding needs a curated list — not random books, but titles with strong genre signal and broad recognition. What's the right list, and how often does it need updating?
- **App Store review:** Does the barcode-scan-to-book-verdict use case raise any App Store concerns? Unlikely, but worth checking the guidelines for camera-based apps before submitting.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding** — Two-path choice screen. Headline: "Know if a book is for you before you buy it." Two large option cards side by side: (A) "Import from Goodreads" with Goodreads logo and instruction "Export your library CSV from Goodreads.com, then import here — takes 2 min"; (B) "Rate some books" with a thumbs icon and "Tap through 20 books to seed your taste profile — takes 60 sec." Below both cards: small text "No account required." App logo at top.

- **Screen 2: CSV Import** — Shown after tapping "Import from Goodreads." Step-by-step instructions with screenshots: (1) Go to Goodreads > My Books > Export; (2) Save the CSV file; (3) Tap "Choose File" here. Large file picker button. Progress bar shown after file is selected: "Reading 247 books…" then "Building your taste profile…" Success state: "Done! Your profile is ready." CTA: "Start scanning."

- **Screen 3: Cold-Start Rating Flow** — Shown after tapping "Rate some books." Full-screen card stack of 20 book covers, one at a time. Each card shows: cover art, title, author. Three tap options below: thumbs up (loved it / want to read), thumbs down (not for me), and "Haven't heard of it" (skip). Progress dots at top (1 of 20). Fast swipe gesture also works. After 10 ratings minimum, a "Done — start scanning" button appears.

- **Screen 4: Home / Scan Screen** — Primary screen. Large camera viewfinder fills most of the screen with a focused rectangle guide for barcode. Instruction text below viewfinder: "Point at the barcode on the back cover." Bottom bar: "Enter title or ISBN manually" link. Top-right: scan count chip (free tier: "18 scans left this month" or "Unlimited" for paid). Top-left: history icon. Auto-detects and advances without a tap.

- **Screen 5: Manual Entry** — Full-screen search. Search field open with keyboard. Placeholder: "Book title or ISBN." Results list below as user types: each row shows cover thumbnail, title, author, year. Tap to select and go to Loading Result. "Cancel" button top-right returns to Home.

- **Screen 6: Loading Result** — Transitional screen (1–3 seconds). Shows detected book cover art (large, centered), title and author below it. Animated subtle pulse on the cover. Text below: "Matching to your reading history…" No progress bar — just the animation. Should feel alive, not frozen.

- **Screen 7: Result Screen** — Core screen. Top section: book cover (left), title + author + Goodreads avg rating (right). Below that: large colored label chip — **Strong match** (green) / **Decent match** (teal) / **Weak match** (amber) / **Not your genre** (red). One bold verdict sentence directly below the chip. Expandable card: "Why this fits your taste" — 60-word Claude paragraph, collapsed by default, tap to expand. Section: "Similar books you've rated" — horizontal scroll of 2–3 book covers with the user's own star rating overlaid on each thumbnail. Bottom: "Scan another" button (primary). Thin save-to-history action happens automatically.

- **Screen 8: Scan History** — Scrollable list, reverse chronological. Each row: book cover thumbnail, title, verdict label chip, date scanned. Tap any row to re-open the Result Screen from cache (instant, no API call). Pull-to-refresh does nothing (history is local). Empty state: "No scans yet — find a bookstore."

- **Screen 9: Paywall / Upgrade** — Shown when monthly scan limit is reached mid-session. Clean, non-aggressive. Shows: "You've used your 30 free scans this month." Below: single option card — "Unlimited scans, forever — $4.99 one-time." Bullet points: No monthly fee, Works at any bookstore, Unlimited history. Large "Unlock BookScan" button. Small "Restore purchase" link below. "Maybe later" dismisses and returns to Home (can't scan until next month reset or purchase).

- **Screen 10: Settings** — Minimal. Sections: (1) Reading Profile — "Imported from Goodreads CSV — 247 books" with "Update profile" button (re-import CSV). (2) Subscription — shows "Free tier: 18 scans remaining this month" or "Unlimited (purchased)." "Restore purchase" link. (3) About — app version, feedback link. No sign-in/sign-out (the app is local-first; no account needed).

**Key interactions:**

- **Primary scan flow:** Home → barcode auto-detected → Loading Result (1–3s) → Result Screen → tap "Scan another" → back to Home
- **Manual entry flow:** Home → tap "Enter manually" → Manual Entry → type title → select from results → Loading Result → Result Screen
- **Paywall trigger:** Any scan attempt when monthly limit reached → Paywall screen → tap "Unlock" → Apple IAP sheet → success → back to Home with unlimited scans
- **History access:** Tap history icon on Home → Scan History list → tap any row → Result Screen (instant from cache)
- **Onboarding — CSV path:** Screen 1 → tap "Import from Goodreads" → Screen 2 (instructions + file picker) → import completes → Home
- **Onboarding — cold start path:** Screen 1 → tap "Rate some books" → Screen 3 (card stack, rate 10+) → tap "Done" → Home
- **Profile refresh:** Settings → tap "Update profile" → Screen 2 (CSV re-import) → returns to Settings with updated book count

## Version History

- v1: Original idea
- v2.1: Critique — Goodreads API is dead, taste-match percentage is hallucination risk, 10-scan free tier expires in one bookstore visit, monthly subscription wrong for occasional-use app
- v3: Improved — replaced API with CSV import, replaced percentage with deterministic label system, raised free tier to 30 scans, switched to one-time $4.99 purchase, added Google Books fallback for new releases, added cold-start onboarding for non-Goodreads users
