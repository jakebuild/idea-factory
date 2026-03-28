Verdict: NEEDS MAJOR WORK
Score: 6/10

What's Actually Good:
- The "365 portraits = year as art" hook is genuinely compelling and has obvious shareability — end-of-year gallery posts are a proven viral format
- Daily habit loop is simple and sticky: one selfie per day, low friction
- Style variety (oil, watercolor, anime, pixel art) gives users something fresh each day and reduces churn
- The gallery-as-collection mechanic creates ownership and sunk-cost that keeps people coming back
- There's a real emotional payoff: watching your face change through a year rendered in art is legitimately cool

Brutal Feedback:
- "The app learns your face across time so each portrait connects to the last" — this is vaporware-level hand-waving. What does this actually mean technically? Consistent face embedding across styles is a hard, unsolved UX problem. Most image-gen models don't preserve facial identity reliably across different style prompts. You need ControlNet, IP-Adapter, or similar fine-tuning infrastructure, not just "AI learns you."
- Lensa, Facetune, Snapchat, TikTok filters, and a dozen iOS apps already do AI-style portraits. The App Store is a graveyard of these. What is the actual differentiator beyond the streak mechanic?
- The streak/daily habit framing is the real product here — but you haven't described how you enforce or reward the habit. No streak counter, no reminders, no consequence for missing a day mentioned.
- "It's not a filter — it's an AI painting of you" is a marketing claim, not a technical reality. Most users can't tell and won't care about the distinction. The people who do care will evaluate the actual output quality, which lives or dies on which model you use and how much you spend per generation.
- Per-generation API costs will destroy unit economics fast. If you're hitting an image generation API (Replicate, fal.ai, Stability) at $0.05–0.20 per image, a free tier is financially toxic at scale. 365 generations per user per year = real money. You need a monetization model from day one.
- Face recognition privacy angle is a landmine. Storing facial data, especially longitudinally ("learns your face over time"), triggers GDPR, CCPA, BIPA (Illinois biometric law). A solo dev with no legal budget shipping in 2–4 weeks should not be touching persistent facial embeddings. This could sink the whole thing.
- The "year-end gallery" payoff is 365 days away. Most users will churn within 2 weeks before ever seeing that reward. There's no mid-term payoff described.

Key Questions:
- Which image generation API preserves facial identity across wildly different styles without fine-tuning? Have you actually tested this?
- What does the "face learning" feature actually do in v1? Is it just using the same selfie as a reference image each time?
- How are you handling API costs? Is this a paid app, subscription, credits, or burn-and-pray freemium?
- What happens if a user misses a day? Does the streak die? Can they backfill?
- How do you differentiate from Lensa's "Magic Avatars" feature, which already did this exact concept and peaked in 2022?

Suggestions:
- Strip the "face learning" claim entirely from v1. Just use a reference image for style transfer per generation. Market it as "your face, painted in a new style every day." Ship that first, prove retention, then improve consistency.
- Add a streak mechanic with real consequences and rewards — miss a day and the streak counter resets, hit 30 days and unlock a new style pack. Habit loop or die.
- Consider a "weekly portrait" instead of daily to reduce API costs 7x and lower user burden. Daily is aspirational; weekly is achievable.
- Hard paywall: charge $2.99/month or $9.99/year. Free tier = 5 portraits, then subscribe. Image gen costs require monetization from launch.
- Don't store facial embeddings. Process images on the fly, discard after generation. Sidestep the biometric data legal nightmare entirely.
- Add a mid-term milestone: "30-day mini gallery" shareable card at one month. Don't make users wait a year for the payoff.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — The core loop (selfie → API call → styled image → gallery display) is buildable in 2 weeks with Expo/React Native + fal.ai or Replicate. The "face learning across time" feature as described is NOT shippable in that window. You need to scope down to a reference-image style transfer, not persistent identity modeling.
- Biggest solo complexity traps:
  - Facial identity consistency across styles: requires ControlNet/IP-Adapter pipelines, not just prompt engineering — this alone can eat weeks
  - Mobile camera + image upload + API call + result polling: async UX is messy, especially with slow generation times (15–60 seconds per image)
  - API cost management: need rate limiting, queuing, and a payment system from the start or you'll get wrecked
  - App store review: AI face transformation apps get extra scrutiny; Apple may push back on privacy grounds
  - Legal/privacy: biometric data storage without a lawyer is a real liability — must be addressed before launch, not after

Design Handoff:
- Screens needed:
  1. Onboarding / Welcome — value prop, "your face as art, every day", CTA to start
  2. Camera / Selfie Capture — full-screen camera, capture button, preview + retake
  3. Style Selector — grid or horizontal scroll of style options (oil painting, watercolor, anime, pixel art, etc.) with preview thumbnails; option for "daily random"
  4. Generation Loading — progress state while API processes (15–60s), show animation or prior portrait while waiting
  5. Portrait Result — full-screen generated portrait, save/share buttons, "add to gallery" CTA
  6. Daily Gallery / Home — calendar or grid view showing all generated portraits, streak counter, "today's portrait" CTA if not yet done
  7. Single Portrait Detail — full-screen view of one portrait, date, style label, share/download options
  8. Year Gallery / Milestone View — special view for 30-day, 90-day, 365-day milestones; shareable collage
  9. Settings — notification preferences (daily reminder), account, subscription status
  10. Paywall / Subscribe — shown after free tier exhausted; pricing tiers

- Key UI interactions:
  - Daily prompt notification → deep link to camera screen
  - Swipe through gallery in chronological order
  - Long-press portrait to see original selfie vs. painted comparison
  - Share sheet for individual portraits and milestone collages
  - Style picker with live preview or past-portrait example in that style
  - Streak counter prominently on home screen with "days until milestone" indicator

- Data each screen needs:
  - Camera: nothing (just device camera access)
  - Style Selector: list of available styles with name, preview image, unlock status
  - Generation Loading: job ID, polling status, estimated wait time
  - Portrait Result: generated image URL, style used, date, original selfie reference
  - Gallery / Home: array of portrait objects (date, image URL, style, thumbnail), current streak count, today's completion status
  - Portrait Detail: single portrait object + original selfie (if stored client-side)
  - Year Gallery: filtered portrait array, milestone thresholds
  - Settings: user preferences, notification toggle, subscription tier
  - Paywall: pricing data, current usage count vs. free tier limit
