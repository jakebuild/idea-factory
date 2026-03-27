# Idea #13: "Will I Like This?" — Zero-Setup AI Book Verdict Scanner (Improved)

## Version: v3
**Date:** 2026-03-25 21:16
**Status:** improved

## Original Idea
Using camera to capture the cover of book on bookstores to read the summary, reviews... before buying, save money and lot of time for buying wrong book

## What Changed and Why

- **Killed the onboarding wall entirely.** The v2 critique's single most damning point: gating the first verdict behind a taste profile setup kills 70%+ of installs at the exact moment users have peak intent — standing in a store holding a book. The fix is structural, not cosmetic: first scan works immediately with zero setup. No profile required. The app delivers a useful verdict immediately using aggregate signals (genre, ratings, review sentiment from Google Books) without personalization. Personalization accumulates *implicitly* as the user saves and skips books over time. The profile builds itself around the user's behavior instead of demanding upfront effort they haven't yet decided to give.

- **Expanded use case beyond physical bookstores to fight the low-frequency death spiral.** The critique correctly identified that visiting a bookstore 4-12 times a year is not enough frequency to build a habit. The fix: the app works anywhere a book decision happens — physical bookstore (barcode scan), library (barcode scan), online browsing (title/author manual search), buying a gift for someone (search by genre/vibe). This triples the use surface without adding meaningful scope. Same scanner, same verdict engine, same wishlist — just remove the artificial constraint that it only works in stores.

- **Added a shareable wishlist as the distribution mechanism.** The biggest structural hole in v2 was zero distribution strategy — no growth loop, no virality, no SEO surface. The fix: the wishlist becomes a shareable artifact. One tap generates a simple static link: "Here's what I'm considering buying this month." Book readers share on BookTok, Instagram, and Goodreads already. A shareable wishlist is a natural fit for this community and creates a word-of-mouth loop that doesn't require app store ads or SEO. Each share is a low-friction install prompt for the recipient.

- **Added a data confidence indicator to stop LLM verdict quality collapse.** The critique was correct: Google Books returns 40-word back-cover blurbs for niche and backlist books. An AI verdict confidently generated from 40 words is noise dressed as signal, and users will notice and distrust the whole app. The fix: show an honest data confidence badge on the verdict screen (High / Medium / Low based on description length and review count). For Low confidence books, the UI says "Limited book data — verdict is a rough guess" rather than pretending it's authoritative. This builds trust for the cases it does nail, instead of eroding it on the cases it can't.

- **Switched to a Goodreads import for taste profile bootstrapping (optional, not blocking).** For users who *want* personalization from day one, a Goodreads shelf import is one button. Power users already have all their read history in Goodreads — asking them to manually re-enter 10 books is insulting. The import doesn't block the app; it's surfaced post-first-scan as "Want better verdicts? Import your Goodreads shelf." This solves the shallow taste profile problem without making it a conversion barrier.

- **Monetization fixed to free-until-value model.** Free forever for core scanning and wishlist (up to 30 items). $4.99 one-time purchase unlocks unlimited wishlist history + shareable wishlist links. Users can fully experience the loop — scan, save, get verdicts — before ever seeing a paywall. The paywall only appears when they've gotten value and want more of it.

## Improved Description

A mobile app that gives you an instant AI verdict on any book — YES, MAYBE, or SKIP — the moment you're considering it. No setup required. Point your camera at the barcode (or type a title), and within 3 seconds you see: the verdict, a one-sentence reason calibrated to your taste, and a data confidence indicator so you know how much to trust it.

The taste profile builds itself silently in the background. Every book you save to your wishlist teaches the app what you like. Every book you skip teaches it what you don't. After 10-15 interactions the personalization is real. For users who want to fast-track it, a Goodreads import fills the profile in one tap.

Works anywhere a book decision happens: in a bookstore, at a library, browsing Amazon late at night, picking a birthday gift for someone. The wishlist persists across sessions and can be shared as a link — useful for sharing "my current reading shortlist" with friends or on social media.

The monetization is clean: free to use forever, $4.99 one-time to unlock unlimited wishlist history and shareable links. Users pay only after they've already gotten value.

## Why This Is Worth Building (Solo + AI)

The technical stack is identical to v2 but the onboarding is gone — which eliminates the #1 conversion risk and removes 2-3 days of autocomplete polish. A solo dev can ship the zero-setup scanner + implicit profile + basic wishlist in 10 days with Expo/React Native and AI coding assistance. The shareable wishlist is a genuine growth loop that doesn't require a marketing budget, making distribution tractable for a solo project.

## Solo MVP Scope

- **What's in v1:**
  - Barcode scanner (camera) → ISBN lookup via Google Books API
  - Immediate first verdict with no setup (uses genre/rating signals only until profile exists)
  - AI verdict: YES / MAYBE / SKIP + one-sentence reason + data confidence badge (High/Medium/Low)
  - Implicit taste profile: built from save/skip behavior silently, visible in Profile screen
  - Bookstore Wishlist: save scanned books with optional note, cap at 30 items (free tier)
  - Manual title/author search fallback for no-barcode situations
  - Goodreads import prompt (shown post-first-scan, not blocking)
  - Shareable wishlist link (behind $4.99 one-time unlock)

- **What's out of v1:**
  - Onboarding wizard of any kind (killed entirely)
  - Cover photo recognition (barcodes only)
  - Social feed, follows, or comments
  - Price comparison / Amazon lookup
  - Offline mode
  - Reading log / "books I've read" tracking
  - Android support (iOS first to cut testing matrix in half)
  - Notifications or reminders

- **Estimated build time with AI coding tools:** 10-14 days

## Open Questions

- Does the zero-setup first verdict produce a result good enough to not feel broken? Test: scan 10 books cold with no profile and evaluate whether the output is at least "better than flipping a coin." If not, the whole premise needs rethinking before building.
- What percentage of Google Books descriptions for niche/backlist books are under 100 words? Validate against a sample of 20 books (including 5 obscure ones) before committing. If >30% are thin, the confidence indicator becomes load-bearing and must be prominent.
- Does the Goodreads web API still allow OAuth import for third-party apps? (Goodreads locked down their API in 2020 — may need to fall back to manual CSV import or scrape via a user-provided URL.)
- What's the right implicit profile signal: just saves vs. skips, or also time-on-verdict-screen? Short dwell + skip is a stronger negative signal than a quick dismissal.
- How does the shareable wishlist work technically without a backend? Options: (a) encode wishlist as URL params (limited length), (b) use a free KV store like Cloudflare Workers KV to generate a short link. Option (b) introduces the first backend dependency — worth it?

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: First Launch** — Purpose: get users to their first scan in under 10 seconds with zero friction. Key elements: app name + tagline ("Instant verdict on any book"), single large CTA "Scan a Book", small secondary link "Search by title instead", no account prompt, no onboarding steps. This screen should disappear after first use — subsequent launches open directly to Scanner.

- **Screen 2: Scanner** — Purpose: main camera view for barcode scanning. Key elements: full-screen camera viewfinder, barcode targeting reticle (rectangular, centered), instruction text "Aim at the barcode on the back cover" (disappears after first successful scan), "Search by title" link (bottom left), bottom tab bar (Scanner | Wishlist | Profile). Auto-triggers on barcode detection — no tap needed.

- **Screen 3: Loading / Verdict Generation** — Purpose: interstitial while fetching book data + generating verdict. Key elements: book cover image (from Google Books, shown as soon as ISBN resolves), book title + author below cover, subtle animated progress indicator, copy "Getting your verdict..." Duration: 2-5 seconds. Needs a timeout state at 10s: "Taking longer than usual — check your connection."

- **Screen 4: Book Verdict** — Purpose: deliver the verdict clearly. Key elements: (1) large verdict badge — YES (green) / MAYBE (yellow) / SKIP (red) — occupies top 30% of content area and is the first visual element; (2) book cover + title + author; (3) one-sentence personalized reason ("You tend to save narrative nonfiction — this fits that pattern" or generic "Well-reviewed in its genre" if no profile yet); (4) data confidence badge (High / Medium / Low) with tooltip "Based on [X words of description and Y reviews]"; (5) 2-sentence synopsis from Google Books; (6) two primary CTAs: "Save to Wishlist" and "Scan Another"; (7) expandable "More details" section with full description and aggregate rating. Data: book metadata + implicit taste profile + LLM verdict (verdict: YES/MAYBE/SKIP, reason: string, confidence: high/medium/low).

- **Screen 5: Save to Wishlist (Bottom Sheet)** — Purpose: save the book with optional note, quick and dismissible. Key elements: book thumbnail (small) + title + author, text input "Add a note (optional)" — e.g. "on sale today", "gift for Dad", Save button (primary), "No thanks" dismiss link. Not a full screen — slides up from bottom, dismisses on swipe down. Auto-saves verdict and confidence alongside the book.

- **Screen 6: Wishlist** — Purpose: browse all saved books, revisit verdicts, share the list. Key elements: list of saved books — each row shows cover thumbnail, title, author, verdict badge (YES/MAYBE/SKIP), note preview (truncated), date saved; tap row to reopen Verdict screen (cached, no API call); swipe left to delete; share button in header (unlocks shareable link or prompts $4.99 upgrade); empty state: "Scan your first book to start your list." Data: local array [{isbn, title, author, coverUrl, verdict, reason, confidence, note, savedAt}].

- **Screen 7: Shareable Wishlist (Web View / Share Preview)** — Purpose: the shared link destination — a simple read-only list someone can view without downloading the app. Key elements: list of books with cover, title, verdict badge, optional note; "Get the app" CTA at the bottom. This is a static page (not an in-app screen) but needs design spec. Note: only shown to recipients of a shared link.

- **Screen 8: Taste Profile** — Purpose: let users see and adjust what the app has learned. Key elements: "Based on your saves and skips" header; list of implicitly detected preferences (e.g. "You seem to like: Narrative Nonfiction, Science, History"; "You tend to skip: Romance, Fantasy"); edit button to override/add manual preferences; "Import from Goodreads" button (shows import instructions or OAuth flow); "Reset profile" option. Accessed from bottom tab. Data: implicit profile object {detectedGenres: [], skippedGenres: [], savedBooks: [], skippedBooks: []}.

- **Screen 9: Book Not Found** — Purpose: fallback when barcode returns no result or Google Books has no entry. Key elements: "Couldn't find this book" message, search field (pre-filled with any text detected from scan, or blank), "Search" button, "Scan a different book" link. Data: partial ISBN or empty, user search query.

- **Screen 10: Goodreads Import Flow** — Purpose: fast-track taste profile setup for users who have a Goodreads history. Key elements: "Import your reading history" headline, instructions (if OAuth available: "Connect Goodreads"; if API locked down: "Export your Goodreads library as CSV, then upload here"), file picker or OAuth button, progress indicator during import, success confirmation "Imported X books — your verdicts will now be more personalized." Accessed from Taste Profile screen.

- **Screen 11: Upgrade Prompt (Paywall)** — Purpose: convert free users to paid when they hit the wishlist limit (30 items) or tap Share. Key elements: clear value statement "You've saved 30 books — unlock unlimited saves and shareable wishlists for a one-time $4.99"; feature list (3 bullets max); "Unlock for $4.99" CTA; "Maybe later" dismiss. Not a full-screen takeover — use a bottom sheet or modal.

**Key interactions:**
- First launch → Scanner → (scan barcode) → Loading → Verdict: entire first experience, zero friction, under 15 seconds
- Verdict → "Save to Wishlist" → Save Sheet → back to Verdict → "Scan Another" → Scanner: fast loop for browsing a shelf
- Wishlist → tap book → Verdict (cached, no API): revisit past decisions without network
- Wishlist → Share button → Upgrade Prompt (if unpaid) → shareable link generated: growth loop
- Taste Profile → "Import from Goodreads" → Import Flow → Profile updated: power user fast-track
- Bottom tab bar (Scanner | Wishlist | Profile) present on all main screens

## Version History
- v1: Original idea — camera scan book to read summary/reviews before buying
- v2.1: Critique — onboarding conversion killer, tiny shrinking market, no distribution, shallow taste profile, LLM quality inconsistency
- v3: Improved — killed onboarding wall (zero-setup first scan), expanded beyond bookstores, implicit profile from behavior, data confidence indicator, shareable wishlist as growth loop, freemium monetization
