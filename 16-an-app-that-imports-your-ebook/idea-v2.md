# Idea #16: BookScan — Instant Personalized Book Verdict at the Shelf (Improved)

## Version: v2
**Date:** 2026-03-25 22:40
**Status:** improved

## Original Idea
an app that imports your ebook metadata (titles, authors, genres) to build a personal taste profile, then uses AI + Goodreads reviews to generate personalized book previews when you scan a book at bookstores, helping you decide if it's worth buying

## What Changed and Why

- **Dropped Goodreads entirely; use Open Library + Google Books APIs instead.** Goodreads killed its public API in 2020 and scraping it is legally gray and brittle. Open Library and Google Books both have free, stable APIs with ISBNs, descriptions, subjects, and some community data. This eliminates the single biggest technical risk that could kill the project before launch.

- **Replaced ebook metadata import with a 5-minute taste setup wizard.** The critique is right: Kindle/Apple Books/Kobo each require different export flows, and the resulting data (titles + authors) only tells you genre anyway. A setup wizard where users rate 10 books they've loved gives richer signal faster — users can explain *why* they liked something via tags ("slow burn", "unreliable narrator", "plot-driven"). This turns shallow genre-matching into real preference capture, and it works on day one without any platform integrations.

- **Narrowed the target user from "ebook buyer + bookstore shopper" to just "bookstore shopper who wants a second opinion."** The Venn diagram problem is real. The new framing doesn't require an ereader at all. Anyone standing in a bookstore with a phone is the user. Taste profile comes from the wizard, not from purchase history.

- **Rebuilt retention with a "My Shelf" wishlist + weekly digest.** Episodic in-store use alone can't support a product. After scanning a book, users can save it to a wishlist. A weekly email/notification shows price drops or availability updates on wishlist books. This creates a loop outside the bookstore moment.

- **Scoped to a progressive web app (PWA) instead of native mobile.** No App Store review delays. Camera barcode scanning works via the browser's native camera API. Faster to iterate with AI coding tools. One codebase, ships in days not weeks.

## Improved Description

BookScan is a mobile-first PWA that helps you decide in 30 seconds whether a book is worth buying while you're standing in a bookstore.

**Setup (one-time, 5 minutes):** You rate 10 books you've loved using a swipe-style wizard. For each, you tag why you loved it — pacing, writing style, emotional tone, themes. This builds your taste profile. No account required for the first scan (profile stored locally; account needed to save wishlist).

**At the shelf:** Open the PWA, tap Scan, point your phone camera at the barcode. The app looks up the ISBN via Open Library/Google Books, pulls the book's description, genre tags, comparable titles, and any available review excerpts. It sends this — along with your taste profile — to Claude, which returns a 3-sentence personalized verdict: "Based on your love of slow-burn psychological thrillers with unreliable narrators, you'll probably like this. The pacing is reportedly similar to Gone Girl. The main risk for you is the ending — reviews call it divisive."

**After the store:** Books you scan are saved to history. Tap "Save to Wishlist" to track a book you didn't buy. The app sends a weekly digest showing wishlist books available at local libraries (Open Library integration) or price drops if you link an Amazon/Bookshop.org account.

**Taste profile evolves:** After reading a book, you can rate it in the app. The AI refines your profile over time based on what landed vs. what missed.

## Why This Is Worth Building (Solo + AI)

The core loop — scan ISBN, get personalized AI verdict — is achievable in 1-2 weeks with AI coding tools using stable public APIs (Open Library, Google Books) and a single Claude API call per scan. There's no proprietary data dependency that can be pulled out from under you. The taste setup wizard eliminates the ebook import integration nightmare entirely, and a PWA removes App Store gatekeeping. The retention hook (wishlist + digest) is a simple database + email feature that can ship in week 3.

## Solo MVP Scope

- **What's in v1:**
  - Taste setup wizard (rate 10 books, add tags per book)
  - Barcode scanning via phone camera (PWA, native camera API)
  - ISBN lookup via Open Library + Google Books APIs
  - AI verdict generation via Claude API (3-sentence personalized summary)
  - Scan history (local storage)
  - Wishlist save (local storage, no account required)

- **What's out of v1:**
  - Ebook metadata import (any platform) — cut entirely
  - Account/auth system — defer to v2
  - Weekly digest / email notifications — defer to v2
  - Library availability lookup — defer to v2
  - Price tracking — defer to v2
  - Taste profile evolution from post-read ratings — defer to v2
  - Social features, sharing, lists — never, solo dev, out of scope

- **Estimated build time with AI coding tools:** 2-3 weeks
  - Week 1: Setup wizard UI, taste profile storage, barcode scanning, ISBN API integration
  - Week 2: Claude API integration for verdict generation, scan history, wishlist
  - Week 3: Polish, edge cases (no barcode found, no book data), PWA install prompt, deploy

## Open Questions

- Does Google Books return enough review/community data for Claude to generate a meaningful verdict, or does the AI mostly have to work from the official description alone? Need to test API response quality on a sample of 20-30 books before committing.
- What happens when a book has no ISBN (self-published, older books, some international editions)? Need a fallback — manual title search?
- Is the taste setup wizard (rate 10 books) too much friction for first-time users? Should it be optional with a "skip and rate as you scan" path?
- How specific should taste tags be? "Unreliable narrator" is niche; "mystery/thriller" is generic. Finding the right granularity for the tags is a product design problem that affects verdict quality.
- PWA camera API permissions differ across iOS/Android browsers. Need to validate barcode scanning works reliably on Safari (iOS) before committing to PWA over native.

## Design Handoff

List every screen/view the app needs:

- **Screen 1: Landing / Home** — First-time empty state with "Start Setup" CTA; returning user sees recent scans, quick-access Scan button, and wishlist count badge. Key elements: app name, tagline ("Your book verdict in 30 seconds"), large Scan button (bottom center), list of 3 most recent scans.

- **Screen 2: Taste Setup Wizard — Step 1 (Book Rating)** — Swipe-style card stack of ~20 popular books across genres; user swipes right (loved it), left (not for me), or up (haven't read). Shows progress indicator (e.g. "6 rated, need 10"). Key elements: book cover image, title, author, swipe affordance, skip button.

- **Screen 3: Taste Setup Wizard — Step 2 (Tag Why You Loved It)** — For each book swiped right, shows a tag picker: "Why did you love this?" Tags include: Fast-paced, Slow burn, Plot twists, Character-driven, Dark/gritty, Funny, Emotional, Unreliable narrator, World-building, Based on true story. Multi-select. Key elements: book cover thumbnail, tag grid (2 columns), Next button.

- **Screen 4: Taste Setup Wizard — Complete** — Summary of taste profile ("You love: psychological thrillers, slow burns, unreliable narrators"). CTA to start scanning. Option to add more books later. Key elements: profile summary tags, illustration/celebration moment, "Start Scanning" button.

- **Screen 5: Scanner** — Full-screen camera view with barcode targeting overlay (rectangle reticle). Instructions: "Point at the barcode on the back cover." Manual entry fallback link. Key elements: camera viewfinder, scan reticle, "Enter ISBN manually" link, cancel button.

- **Screen 6: Loading / Verdict Generation** — Shown after barcode detected while AI generates verdict. Shows book cover + title + author pulled from API. Loading animation with copy like "Reading the room..." Key elements: book metadata preview, animated loader, cancel button.

- **Screen 7: Book Verdict** — The money screen. Shows book cover, title, author, genre tags. Then: AI verdict (3 sentences, personalized). Subtext: "Why you might love it" and "Watch out for" as two short bullet points. Actions: Save to Wishlist, Scan Another, Share. Key elements: book cover (large), verdict card, two-tone breakdown, action buttons.

- **Screen 8: Scan History** — Chronological list of all scanned books. Each row: cover thumbnail, title, author, scan date, wishlist icon. Tap to re-view verdict. Key elements: scrollable list, filter by wishlist/all, empty state for new users.

- **Screen 9: Wishlist** — Grid or list of saved books. Each item: cover, title, "Added [date]". Tap to re-view verdict. Swipe to remove. Key elements: grid layout (2 columns), remove affordance, empty state with prompt to scan first book.

- **Screen 10: Taste Profile (Settings)** — Shows current taste tags in a visual cluster. Option to re-rate books or add more. Option to clear profile and restart wizard. Key elements: tag cloud/list, "Add more books" button, "Reset profile" destructive action.

- **Screen 11: Manual ISBN Entry** — Simple text input screen. User types or pastes ISBN (10 or 13 digit). Lookup button. Error state if ISBN not found. Key elements: number input field, lookup button, back to scanner link.

**Key interactions:**

- **First-run flow:** Landing → Taste Setup Wizard (Step 1 → Step 2 for each loved book → Complete) → Scanner
- **Returning scan flow:** Home → Scanner → Loading → Verdict → (Save to Wishlist or Scan Another)
- **Wishlist review flow:** Home → Wishlist → tap book → Verdict (re-displayed)
- **Profile update flow:** Home → Taste Profile → Add more books → Wizard Step 1 → Wizard Step 2 → back to Profile
- **No barcode flow:** Scanner → Manual ISBN Entry → Loading → Verdict

## Version History
- v1: Original idea
- v1.1: Critique — Goodreads API dead, ebook import is per-platform nightmare, target user Venn diagram too small, no retention loop
- v2: Improved — Dropped Goodreads/metadata import, setup wizard replaces import, stable public APIs, PWA not native, wishlist retention added

### Critique

**Verdict: WORTH BUILDING — Score: 7/10**

v2 is a genuinely improved idea. Dropping Goodreads, PWA over native, and the scoped v1 cut list are all correct calls. The core loop is achievable solo in 2–3 weeks with AI tools.

Remaining risks: (1) Google Books API data is thinner than hoped — "comparable titles" doesn't exist in that API, and Claude will mostly be working from publisher blurbs, making verdicts feel shallow. (2) iOS Safari barcode scanning via WebRTC is a known minefield — test this on day one before building anything else. (3) The taste wizard requires 10 rated + tagged books before first value — high drop-off risk; make skip-able by default. (4) No monetization plan means every scan costs money with no revenue path. (5) Local storage data loss will silently destroy power user data — at minimum, warn users.

Biggest suggestion: build the core loop first (hardcoded taste profile → scan ISBN → Google Books → Claude → read verdict). If the verdict quality isn't impressive, nothing else matters. Test verdict quality before investing a week in wizard UI.
