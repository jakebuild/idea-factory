# Idea #14: AI Advisor — Is This Book Worth Reading For Me? (Improved)

## Version: v3
**Date:** 2026-03-25 21:33
**Status:** improved

## Original Idea
AI advisor that tell me if a book is worth reading based on my taste, snap cover or search by title, get personalized verdict in 30 seconds

## What Changed and Why

- **Free tier raised from 5 to 10 verdicts; price raised to $7.99/month with a 150/month cap** — 5 verdicts is a conversion trap: a user browsing their Sunday TBR pile hits the wall in 20 minutes, before the dopamine loop has delivered. 10 verdicts buys enough sessions to create habit. $4.99 unlimited is an API cost bomb — a single power user doing 200 verdicts/month costs more than they pay. $7.99 with a 150/month cap survives real usage math and still feels cheap vs. a wasted $18 hardcover.

- **Onboarding expanded to 20 books with a sparse-signal fallback** — 15 swipes leaves you with as few as 5 usable data points if the curated list misses a user's genre. That produces a generic first verdict, which is churn. 20 books across genres, and if fewer than 8 produce a "Loved It" signal, show one direct genre-preference screen ("What do you read most?") before the first verdict. Do not let sparse signal produce a bad first impression.

- **"Maybe Later" verdict CTA is eliminated — binary Read It / Skip It only** — The entire product promise is a decisive friend, not a hedge. "Maybe Later" is what you get from a star rating. If the AI is genuinely uncertain, the verdict paragraph says "this is a coin flip for your taste" — but the button still commits to Read It or Skip It. Uncertainty is communicated through the copy, never through a third option that diffuses the product's core promise.

- **Taste profile data structure defined explicitly before any code** — "Updates silently in the background" is hand-waving over the hardest design decision in the product. The profile is a flat JSON object: `{ genres: { "literary fiction": 0.9, "thriller": 0.3, ... }, liked_authors: [...], disliked_authors: [...] }` — capped at 50 genre keys and 30 authors total. Weights update via swipe/thumbs signals. This structure feeds onboarding, verdict prompt, thumbs feedback, and the profile screen. Designing it first eliminates a mid-build refactor.

- **Verdict history screen cut; replaced with a 3-card "Recent" strip on Home** — Nobody re-reads 6-month-old verdicts. History persistence is database complexity for zero user value. A flat "Recent" strip on the home screen (last 3 verdicts as cards: cover + CTA label) delivers 100% of the actual value in zero extra screens or queries.

- **"I was wrong" feedback loop added as a single post-read check-in** — The highest-trust signal in this product is "I read it because you said so and hated it." Right now that signal is completely missing. When a user gives a "Read It" verdict a thumbs up, a deferred notification (or in-app nudge after 2 weeks) asks: "Did you read it? Was it good?" One tap: Yes / No. This feeds directly back into the taste profile and is the most honest training data the product can collect.

- **Share creates a branded image card, not plain text** — Plain text share looks like a text message — no product branding, no reason for the recipient to find BookVerdict. A share generates a styled card image (book cover + verdict CTA label + "BookVerdict" watermark) that is visually distinct and makes the product discoverable when shared to social or iMessage.

## Improved Description

BookVerdict is a PWA that answers "is this book worth reading FOR ME?" in under 30 seconds. Search by title, get a one-paragraph opinionated verdict from a well-read friend, delivered as a binary Read It or Skip It with a bold color-coded button.

On first launch, a 20-book swipe quiz (Loved It / Meh / Haven't Read) establishes your taste profile in under 2 minutes. The profile is a flat JSON structure — genre weights plus liked/disliked author lists — that feeds every verdict. If your swipes produce sparse signal (fewer than 8 strong preferences), a single genre-preference screen supplements before your first verdict. You always get a calibrated first verdict, never a generic one.

Verdicts are binary and committed. The AI writes a personal paragraph — second person, specific to your taste — and ends with either Read It (green) or Skip It (red). Never "Maybe Later." Uncertainty lives in the copy ("this is a coin flip for your taste") not in a third button. After a "Read It" verdict, the app follows up 2 weeks later: "Did you read it? Was it good?" — one tap feeds your profile the most honest signal it will ever get.

Free tier: 10 verdicts. Paid: $7.99/month for 150 verdicts. Share generates a branded image card (cover + verdict + watermark), not plain text.

## Why This Is Worth Building (Solo + AI)

Every structural problem in v2 had a known fix — the fixes were just not applied. The data model (flat genre JSON), the paywall math ($7.99/150), the binary CTA, the sparse-signal fallback — none require novel engineering, only explicit decisions made before writing code. A solo dev with AI coding tools can ship this in 3–4 weeks because every API (Google Books, Claude/OpenAI, Stripe) has excellent docs and the only genuinely hard work is prompt engineering, which is iterative testing, not complex code. The "well-read friend paragraph" format is something no incumbent has shipped — Goodreads still shows star averages in 2026.

## Solo MVP Scope

- **What's in v1:**
  - Magic link auth (Resend)
  - 20-book swipe onboarding with sparse-signal genre fallback
  - Title search via Google Books API
  - Verdict generation: flat taste profile JSON → LLM prompt → Read It / Skip It paragraph
  - Thumbs up/down on verdict (silently updates profile)
  - 2-week post-read check-in nudge (simple in-app banner, no push notifications)
  - "Recent" strip on home (last 3 verdicts, no full history persistence)
  - Paywall at verdict 11 (free tier: 10 verdicts)
  - Stripe subscription ($7.99/month, 150/month cap enforced server-side)
  - Share as branded image card (canvas-generated on client)
  - Taste profile view (genre tags + author lists, removable, add-your-own)
  - PWA manifest + HTTPS (Add to Home Screen)

- **What's out of v1:**
  - Camera / OCR / cover scanning
  - Native iOS/Android app
  - Verdict history screen (beyond the 3-card Recent strip)
  - Series awareness / reading progress state
  - Social features, follows, community
  - Recommendations feed / discovery browse
  - Push notifications (use in-app banner for check-in nudge)
  - A/B testing infrastructure
  - Admin dashboard

- **Estimated build time with AI coding tools:** 18–22 days. Breakdown: auth + Stripe plumbing (3 days), onboarding swipe quiz (2 days), search + verdict flow (2 days), prompt engineering iteration (4 days — this is the product, not a subtask), taste profile data model + update logic (2 days), share card generation (1 day), paywall enforcement + edge cases (1 day), PWA setup + polish (1 day), manual test cases + QA (2–3 days).

## Open Questions

- **Target user clarification needed before prompt engineering begins:** Casual reader (4–6 books/year, needs help choosing) vs. voracious reader (2–3/month, wants quick validation). These users need different verdict tone and have very different paywall tolerance. Pick one to optimize the first 30 prompt test cases around.
- **How to handle Google Books API gaps gracefully:** Self-published books, translated titles, and obscure backlist will return bad or no metadata. Define the degradation path (show "limited info" warning and still generate verdict vs. block verdict entirely) before the first bug report.
- **Post-read check-in timing:** 2 weeks is a guess. Should this be configurable per-user or triggered by a second visit to the same book's verdict? Needs a decision before building the nudge logic.
- **Community seeding channel:** One subreddit, Bookstagram account, or Discord chosen before launch for early user feedback on verdict quality. Do not launch cold into the void.

## Design Handoff

List every screen/view the app needs:

- **Screen 1: Landing / Sign In** — purpose: first-time entry and returning auth. Key elements: app name + one-line value prop ("Is this book worth reading for YOU?"), email input field, "Send Magic Link" CTA button, minimal footer. No distractions.

- **Screen 2: Onboarding — Swipe Quiz** — purpose: build taste profile before first verdict. Key elements: single book card centered (large cover image, title, author, genre tag), three tap buttons below (Loved It / Meh / Haven't Read), progress bar at top ("Book 7 of 20"), no skip button anywhere, fast slide-out/slide-in card transition animation. Data: static list of 20 curated books (title, author, cover URL, genre tags) spanning 8 genres.

- **Screen 3: Onboarding — Genre Fallback (conditional)** — purpose: supplement sparse signal if fewer than 8 "Loved It" swipes. Key elements: headline "What do you read most?", grid of 8 genre tiles (Literary Fiction, Thriller, Romance, Sci-Fi, Fantasy, Nonfiction, History, Self-Help), multi-select tap (up to 3), "Continue" CTA. Only shown when sparse signal is detected; skipped otherwise.

- **Screen 4: Home / Search** — purpose: primary daily-use screen. Key elements: search bar prominent at top (focused by default, keyboard opens immediately), "Recent" strip below showing last 3 verdict cards (cover thumbnail, title, Read It/Skip It label with color), bottom nav (Home / Profile / Settings). Empty state for new users: "Search for a book to get your first verdict."

- **Screen 5: Search Results** — purpose: pick the right book edition. Key elements: list of results (cover thumbnail, title, author, year — from Google Books API), tap row to immediately trigger verdict (no confirm step), empty state for no results with manual title/author entry fallback.

- **Screen 6: Verdict Loading** — purpose: personality-forward loading screen while LLM generates. Key elements: book cover faded in background, sequential animated text steps ("Reading your taste profile..." → "Thinking about [Title]..." → "Almost there..."), no spinner, copy is book-specific. Duration: 5–15 seconds.

- **Screen 7: Verdict** — purpose: the core product screen. Key elements: large book cover at top, title + author below, one-paragraph personalized verdict (second-person, opinionated), bold full-width CTA button (Read It — green, or Skip It — red), thumbs up/down row below verdict text (pulse animation on tap, silently updates profile), share button (generates branded image card). No "Maybe Later" option anywhere on this screen.

- **Screen 8: Paywall / Upgrade** — purpose: intercept at verdict 11 attempt without disrupting flow. Key elements: shown inline over the verdict loading screen (no navigation away), headline "You've used your 10 free verdicts", single value prop line, price ($7.99/month), "Unlock Unlimited" CTA (Stripe Checkout), "Not now" ghost link. No feature comparison table.

- **Screen 9: Taste Profile** — purpose: transparency into how verdicts are personalized + manual corrections. Key elements: "Your Taste" header, genre tags section (inferred weights shown as filled pills, tap to remove), liked authors list (removable, tap + to add), disliked authors list (removable), "Last updated: [timestamp]", explanatory subtext ("Your verdicts get sharper as you rate more books").

- **Screen 10: Settings** — purpose: account management. Key elements: email display (read-only), subscription status ("Free — 3 verdicts remaining" or "Pro — 147 verdicts remaining this month"), "Manage Subscription" link (opens Stripe customer portal), "Sign Out" button.

- **Screen 11: Post-Read Check-In Banner (in-app nudge)** — purpose: collect highest-value feedback signal. Key elements: appears as a dismissible banner at top of Home screen, triggered 2 weeks after a "Read It" verdict with thumbs-up. Copy: "Did you read [Title]?" with two buttons: "Yes, loved it" / "Wasn't for me." Tapping either updates taste profile and dismisses. Dismissing without tapping snoozes for 7 days.

**Key interactions:**

- Swipe quiz cards animate left on "Haven't Read" / "Meh" tap, right on "Loved It" tap; swipe gesture also works as fallback
- After last swipe card (or genre fallback screen), transition directly to Home with a brief "Your taste profile is ready" confirmation flash
- Tapping a search result immediately triggers loading state — no intermediate confirm screen
- Verdict screen share button generates a canvas image (book cover + verdict label + "BookVerdict" branding) and triggers native share sheet
- Paywall intercepts inline at the loading screen transition — user does not leave the search flow
- Thumbs up/down on verdict screen shows 300ms pulse animation, no spinner, profile updates async in background
- Post-read check-in banner appears on next Home screen load after 2-week window; tapping either option fires a profile update and banner slides away

## Version History
- v1: Original idea — snap cover or search, personalized verdict in 30 seconds
- v2.1: Critique — cold start problem, free tier too low, "Maybe Later" CTA, undefined taste profile structure, LLM cost math broken
- v3: Improved — 10 free verdicts, $7.99/150/month, binary Read It/Skip It only, explicit taste profile JSON schema, sparse-signal fallback in onboarding, post-read feedback loop, branded share card
