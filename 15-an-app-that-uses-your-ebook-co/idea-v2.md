# Idea #15: BookScan — "Should I Buy This?" Personalized Book Scanner (Improved)

## Version: v2
**Date:** 2026-03-25 22:34
**Status:** improved

## Original Idea
an app that uses your ebook collection to generate rich previews (summaries, mind maps, reviews) for books users scan at bookstores, helping them decide before buying, with affiliate links to purchase the real book

## What Changed and Why

- **Ditched ebook text as the data source → replaced with Goodreads/StoryGraph reading history.** The original relied on parsing users' ebook files, which are almost universally DRM-locked on Kindle or Apple Books and legally radioactive to process with AI. Reading history via API is public, accessible, and legally clean. It also contains *more* useful signal — ratings, shelves, reviews — than raw ebook text.

- **Killed the "personal library as preview source" premise → the app now asks "does this fit my taste?" not "do I own something like this?"** The original's value prop was backwards: your owned ebooks rarely overlap with what you're browsing in a bookstore. The real question a bookstore browser has is *"Is this for me?"* A taste-match score answers that directly. No copyright exposure, no parsing complexity.

- **Dropped mind maps entirely.** Mind maps are demo-bait. They require a custom renderer, add a week of work, and get ignored in real use. Replaced with three things users actually want: a one-sentence verdict, a taste-match percentage, and two or three comparable books they've already rated. Concrete, fast, useful.

- **Removed affiliate links as the monetization model.** Amazon Associates pays ~$0.67–$1.35 per book conversion, and users are *already standing in the store* — they don't need an affiliate link to buy. Replaced with a freemium model: free for 10 scans/month, $2.99/month for unlimited. Simpler, honest, and doesn't require Amazon's approval process or API rate-limit management.

- **Fixed the scan flow for bookstore reality.** Original assumed on-device AI generation in real time — too slow. New approach: ISBN barcode scan → lookup against Open Library / Google Books for metadata → Claude API call with user's taste profile (pre-loaded) → result in under 3 seconds. The taste profile is fetched and cached at app open, not at scan time. This makes the critical path fast.

## Improved Description

BookScan is a mobile app for people who browse physical bookstores and want a fast, personalized answer to "should I buy this?"

The user connects their Goodreads or StoryGraph account once. The app pulls their reading history, ratings, and shelves and builds a taste profile. In the bookstore, they scan a book's barcode. BookScan looks up the book's metadata (title, genre, themes, author, average rating), then sends it to Claude with the user's taste profile and asks: "Given what this person loves and hates, should they buy this?"

The result screen shows:
- A verdict: Buy / Skip / Depends (with one sentence of reasoning)
- A taste-match score (0–100%)
- 2–3 books from their own reading history that are similar, with their own ratings shown
- A "Why this matches / doesn't match" short paragraph

No ebook parsing. No mind maps. No DRM. No affiliate links. Just a fast, honest, personalized answer.

The app works offline for the last 50 scanned books (cached results). For new scans, it needs a network connection — acceptable since most bookstores have wifi or users have cell signal.

Monetization: freemium. Free tier gets 10 scans per month. Unlimited scans for $2.99/month via in-app purchase. No ads, no affiliate complexity.

## Why This Is Worth Building (Solo + AI)

The pivot eliminates every technically hard and legally risky piece of the original: no ebook parsing, no DRM handling, no mind map renderer, no affiliate API. What remains is a barcode scan → API call → display result loop, which is a well-understood mobile pattern a solo dev can ship in 2 weeks with AI coding help. The taste-profile personalization is the genuine differentiator over Goodreads' own barcode scanner (which exists but gives generic data, not personalized verdicts). The $2.99/month model is simple enough to validate before investing in growth.

## Solo MVP Scope

- **What's in v1:**
  - Goodreads OAuth login + reading history/ratings import
  - ISBN barcode scan (camera) with manual title entry fallback
  - Book metadata lookup via Open Library API (free, no key required)
  - Taste-match verdict via Claude API (pre-loaded taste profile cached at login)
  - Result screen: verdict, score, similar books from history, short reasoning
  - Local cache of last 50 scan results (SQLite)
  - Freemium gate: 10 free scans/month, $2.99/month unlimited (RevenueCat)

- **What's out of v1:**
  - Mind maps (cut entirely)
  - StoryGraph integration (add in v2 if Goodreads has API issues)
  - Affiliate links (revisit only if scan-to-purchase data shows demand)
  - Social/sharing features
  - Custom taste profile editing UI (auto-derived from ratings is enough)
  - Offline-first for new scans (cache hits only work offline; new scans need network)
  - Android (iOS first)

- **Estimated build time with AI coding tools:** 12–16 days

## Open Questions

- Does Goodreads still have a usable public API? Their API has been in a grey zone since Amazon acquired them — need to verify before committing to this as the primary data source. StoryGraph may be the safer bet but has a smaller user base.
- What happens for users with fewer than 20 ratings? The taste profile needs minimum data to be meaningful. Need a fallback: either genre/author preference onboarding questions, or a "rate 10 books to get started" flow.
- Is Claude API fast enough for a sub-3-second result with a taste profile prompt? Need to test prompt size vs. latency — the taste profile summary sent to Claude needs to be concise or it will slow the critical path.
- RevenueCat integration on iOS: is $2.99/month enough to cover Apple's 30% cut (leaving $2.09) and API costs? At Claude Haiku pricing, a scan costs roughly $0.002. Breakeven is well under 1,000 MAU paying subscribers — viable.
- How should the app handle books with no Open Library data (very new releases, small press)? Need a graceful fallback — show what metadata exists and let the Claude prompt work with partial info.

## Design Handoff

List every screen/view the app needs so a designer or AI design tool can start immediately:

- **Screen 1: Onboarding / Connect Account** — Single screen. Headline: "Know if a book is for you before you buy it." One CTA button: "Connect Goodreads." Brief explanation of what the app does. No form fields. Shows app logo and 3 icon bullets (Scan → Match → Decide).

- **Screen 2: Loading / Syncing** — Shown after Goodreads OAuth while taste profile is being built. Progress indicator, message: "Building your taste profile from [X] books…" Estimated time (5–15 seconds). Cannot be skipped.

- **Screen 3: Home / Scan Screen** — Primary screen. Large camera viewfinder centered on screen for barcode scanning. Instruction text: "Point at a barcode." Manual entry link below ("Enter title or ISBN instead"). Scan count badge in top corner (free tier: "7 of 10 scans left"). Settings icon top right.

- **Screen 4: Manual Entry** — Simple search field with keyboard open. Searches Open Library by title or ISBN as user types. Shows list of results with cover thumbnail, title, author. Tap to select and proceed to result.

- **Screen 5: Loading Result** — Brief (1–3 seconds). Shows scanned book cover art + title. Animated "Matching to your taste…" indicator. Should feel snappy, not like a spinner screen.

- **Screen 6: Result Screen** — The core screen. Top: book cover, title, author. Large verdict chip: BUY / SKIP / DEPENDS in green/red/yellow. Taste-match score as a percentage (e.g., "82% match"). One-sentence verdict in bold. Expandable section: "Why this matches you" — short paragraph. Section below: "Books you've rated that are similar" — horizontal scroll of 2–3 book covers with the user's own star rating shown on each. Bottom CTA: "Scan another book."

- **Screen 7: Scan History** — Scrollable list of past scans. Each row: book cover, title, verdict chip, taste-match %, date scanned. Tap to re-open result. Shows last 50 scans.

- **Screen 8: Paywall / Upgrade** — Shown when free scan limit is reached. Shows scan count: "You've used all 10 free scans this month." Single plan: Unlimited scans for $2.99/month. One subscribe button. Link: "Restore purchase." Brief value prop: "Scan as many books as you want."

- **Screen 9: Settings** — Minimal. Shows connected Goodreads account (avatar + name). "Refresh taste profile" button. "Manage subscription" link. "Sign out" button. App version.

**Key interactions:**

- **Primary flow:** Home (scan) → barcode detected → Loading Result → Result Screen → "Scan another" returns to Home
- **Manual entry flow:** Home → tap "Enter manually" → Manual Entry search → select book → Loading Result → Result Screen
- **Paywall trigger:** Any scan attempt when monthly limit is reached → Paywall screen → subscribe → returns to Home with limit removed
- **History access:** Swipe up or tab from Home → Scan History → tap any result → Result Screen (loaded from cache, instant)
- **Onboarding:** Cold launch → Onboarding → tap Connect Goodreads → OAuth webview → Loading/Syncing → Home

### Critique

**Verdict: WORTH BUILDING — Score: 7/10**

The pivot from ebook parsing to Goodreads reading history was the right call. The architecture is clean and shippable: barcode scan → metadata lookup → Claude verdict → display. The design handoff was already complete in the idea itself. Three hard problems remain: (1) the Goodreads API is functionally dead since 2020 — you need a concrete data source plan before writing code, with Goodreads CSV export as the most viable legal fallback; (2) the taste-match percentage is a hallucination risk — replace it with a label system derived from actual signals, not a Claude-generated number; (3) the 10-scan/month free tier is too low for a single bookstore visit. Solo dev can ship in 2-3 weeks *if* the API question is resolved first. If not, add a week for the CSV import pivot. The monetization math is sound; the user acquisition funnel is narrow but real.

---

## Version History

- v1: Original idea
- v1.1: Critique — legally risky ebook parsing, DRM-locked libraries inaccessible, affiliate links confused, mind maps are scope bloat
- v2: Improved — replaced ebook text with Goodreads taste profile, dropped mind maps, killed affiliates for freemium, rebuilt for sub-3-second scan flow
