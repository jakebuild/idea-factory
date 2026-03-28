# Critique v1 — ShakeShot

**Verdict:** WORTH BUILDING
**Score:** 7/10

## What's Actually Good
- The core mechanic is genuinely novel — "one shake, no do-overs" creates a Wordle-like daily ritual with low time commitment but high shareability
- Prediction vs reality pair is the killer feature: it naturally creates social content without asking users to "make content"
- Shake intensity as art style input is delightfully analog in a digital context — easy to explain in one sentence
- Calendar synthesis gives personalization without requiring manual input, which is the holy grail for daily apps
- The "no reshakes" constraint is smart product design — scarcity creates value and prevents decision paralysis
- Natural daily loop: morning shake → evening caption → share. Sticky by design.

## Brutal Feedback
- **Calendar permission is a huge drop-off cliff.** A significant portion of users will bounce the moment they see "connect your calendar." People are paranoid about this. You need calendar data to make the idea work, but it's also the #1 reason people won't try it.
- **The AI art is gimmicky without the social layer.** If nobody sees the prediction vs reality pair, the app is just "cool art generator for your day" — which is cute but not compelling enough to retain users beyond day 3.
- **Shake gesture on mobile is notoriously unreliable.** Accelerometer readings vary wildly across devices. "Gentle vs hard shake" thresholds are hell to calibrate. Users will pocket-shake and get a chaotic portrait of a chill Tuesday and never understand why.
- **AI image generation costs money per generation.** Free tier will bleed you dry if it takes off. Each morning generation = API cost. You need a monetization plan from day one or a tight free/paid split.
- **"One-word caption" at end of day depends on habitual return.** Evening retention is brutal. Most daily apps fail at getting users to complete the loop. Without a push notification and a reason to care, this half of the mechanic dies quietly.
- **The art output might just be bad.** Calendar keywords like "standup," "1:1," "dentist" don't obviously map to interesting visual prompts. The prompt engineering to make calendar events produce genuinely striking art is non-trivial.
- **No clear monetization path.** Premium styles? Subscription? Ad-free? You need this figured out before you launch because the cost structure (API per generation) punishes growth.

## Key Questions
- How do you handle users with no calendar events? Empty day = blank prompt = bad art = bad first impression.
- What happens to users who don't return in the evening? Does the card stay incomplete forever? Does it auto-caption?
- Is the share mechanic strong enough to drive organic growth, or does it require users to already care about the app?
- How do you handle Google Calendar vs Apple Calendar vs Outlook? Each OAuth flow is its own integration headache.
- What's the image generation cost at 1,000 DAU? At 10,000? Does the unit economics work?

## Suggestions
- **Start with Apple Calendar only** (iOS-first) to avoid multi-provider OAuth hell. Google Calendar second. Skip Outlook entirely for v1.
- **Make the share card gorgeous by default.** The prediction vs reality split-panel needs to look incredible out of the box — this is your viral loop. Invest disproportionately here.
- **Add a "no calendar? manual keywords" fallback.** Let users type 3 words describing their planned day if they don't want to connect calendar. Removes the permission barrier and broadens audience.
- **Solve the evening return problem early.** A single well-timed push notification ("How did your day actually go? 👀") is load-bearing for retention. Don't ship without it.
- **Cap the free tier at image quality, not generation count.** One free generation per day at lower resolution; premium unlocks HD + style packs. This keeps the mechanic intact while monetizing.
- **Pre-generate some example portrait pairs** to show on the onboarding/store page. Users need to see what they're getting before they hand over calendar access.

## Solo Dev Reality Check
- **Can one person ship this in 2-4 weeks with AI coding tools?** MAYBE — The core loop is buildable (React Native + Calendar API + image gen API), but the shake gesture calibration, calendar OAuth for multiple providers, and prompt engineering to make art actually good will each eat more time than expected. 4-6 weeks is realistic for a solid MVP on one platform.
- **Biggest solo complexity traps:**
  - Multi-provider calendar OAuth: Google + Apple have completely different auth flows and data schemas. Each is a weekend.
  - Shake gesture reliability: getting thresholds right across device models is tedious QA work with no shortcut.
  - Prompt engineering for calendar → art: this sounds easy but producing consistently interesting images from "Standup, Code Review, Lunch, 1:1 with Sarah" requires real iteration.
  - Image generation cost management: need rate limiting, abuse prevention, and cost caps before any public launch.
  - Push notification timing + evening re-engagement: technically simple but UX/permission flow adds scope.
  - Image storage and sharing: generated images need to be stored somewhere, shared via deep link, rendered nicely in social previews (Open Graph).

## Design Handoff

### Screens needed
1. **Onboarding / Permissions** — Calendar access request with value prop explanation, skip/manual option
2. **Morning Home Screen (pre-shake)** — Today's date, calendar summary teaser (e.g. "3 meetings, packed afternoon"), shake prompt UI, no portrait yet
3. **Shake Interaction Screen** — Full-screen shake animation / loading state while portrait generates, intensity feedback visual
4. **Portrait Reveal Screen** — Full-screen AI portrait of the day, shake intensity label (e.g. "Gentle"), calendar context shown below, "Save" and "Share" actions, locked until evening
5. **Evening Caption Screen** — Portrait shown again, one-word caption input field, "Complete My Day" CTA
6. **Prediction vs Reality Card** — Split or stacked view of morning portrait + evening caption, shareable as image, social share button
7. **History / Archive Feed** — Scrollable grid or list of past portrait pairs with dates and captions
8. **Settings** — Calendar connection management, notification preferences, premium upgrade

### Key UI interactions
- Shake gesture: physical phone shake triggers generation, intensity meter shows during shake, locks immediately after
- Portrait reveal: animated entrance (fade/scale in) for the generated image
- Caption input: simple single-word text field with character limit enforcement, keyboard up = portrait shifts up
- Share card: tap-to-generate shareable image (portrait + caption as a styled card), native share sheet
- History swipe: horizontal swipe between prediction and reality on past cards

### Data each screen needs
- **Onboarding:** calendar permission status, onboarding completion flag
- **Morning Home:** today's calendar events (count, keywords, density score), current date, whether shake has been done today
- **Shake/Reveal:** calendar-derived prompt, shake intensity value (from accelerometer), generated image URL, generation timestamp
- **Evening Caption:** morning portrait URL, caption text (user input), whether caption has been submitted
- **Share Card:** portrait image, caption, date, shake intensity label, calendar summary text
- **History Feed:** array of completed day entries (date, portrait URL, caption, intensity)
- **Settings:** connected calendar provider(s), notification time preference, subscription status
