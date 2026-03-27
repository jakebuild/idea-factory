Verdict: WORTH BUILDING
Score: 7/10

What's Actually Good:
- Photo-proof mechanic is genuinely differentiated — most habit trackers are honor-system checkbox apps, this adds accountability without needing a social network
- The constraint is the product: "your streak only counts if you show proof" is a clear, marketable promise you can put on an App Store screenshot
- People already do this manually — they text friends gym photos, post to Instagram Stories — you're formalizing a behavior that exists
- No AI verification required for v1: you can ship with self-attestation (you took the photo, you verify it's real) and add verification later
- Streak psychology is proven — Duolingo built a billion-dollar business on it, adding friction via photo makes the streak feel more earned
- Small, focused scope — one core loop: take photo → check in → streak increments

Brutal Feedback:
- "Proof" is theater without verification. Who's checking? If it's self-verified, the photo is just a fancy checkbox. You're adding friction without adding real accountability. Someone can take a photo of anything and check in.
- You're building yet another habit tracker in the most crowded app category on mobile. Habitica, Streaks, HabitBull, Done, Way of Life — they've all been around for years with polish you can't match as a solo dev vibe-coding.
- The photo storage problem is expensive and ignored. Users checking in daily = thousands of photos per user per year. Where do they live? S3/Cloudflare R2 costs money. Are you compressing? Deleting old ones? This isn't a detail, it's infrastructure you need on day 1.
- No answer to "why would I use this instead of just posting to Instagram?" Instagram already does photo proof + social validation + streaks (via Highlights). You need a reason to exist that Instagram doesn't cover.
- Photo friction will kill retention. The core insight is also the core liability — most habit tracker churn happens because check-in is too annoying. Adding a mandatory camera step will make a significant chunk of users quit after 3 days.
- What happens when streaks break? This is emotionally the most important moment in any streak app and you haven't thought about it. Do you lose everything? Is there a grace period? This decision alone shapes whether your app feels fair or punishing.
- No social layer = no virality. The photo is doing nothing if only you see it. The whole point of "showing proof" implies an audience. Without social features, you're just adding friction.

Key Questions:
- Who verifies the photo is real and on-topic? Self? Friends? AI? A random stranger? Each answer is a completely different product.
- Where are photos stored, and who pays for it?
- Is this a private accountability tool or a social/public one? Pick one — trying to be both will kill the focus.
- What's the minimum photo required? A gym selfie? A photo of your running shoes? A picture of the book you're reading? The ambiguity is a UX nightmare.
- Do photos get deleted after X days to control storage costs?
- What is your monetization model — subscription, one-time purchase, freemium?

Suggestions:
- V1 killer scope: ship it as "accountability partner" mode — you invite one friend, you send them the photo proof, they tap "approve" in the app. No AI, no storage problem at scale, dead-simple social layer, and "proof" actually means something.
- Skip general habits for v1, own one niche: gym workouts, reading, cold showers — pick one and build the whole thing around that community's language and motivation.
- Solve the storage problem upfront: store only a thumbnail locally, delete originals after 30 days, don't offer a gallery. This caps your costs and keeps the app lightweight.
- Add a streak freeze mechanic on day 1 — one free freeze per month, buy more. This is table stakes for streak apps and a natural monetization hook.
- "Streak wall" feature: your streak photos auto-assemble into a grid over time. This is the Instagram Highlights equivalent and makes the photo history feel rewarding, not just obligatory.

Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — The core loop (camera → photo saved → streak incremented) is shippable in 2 weeks as a mobile app. But photo storage, push notifications for daily reminders, and any social/sharing layer add meaningful complexity. If you ruthlessly cut to: local photo storage only + solo accountability mode, it's a 2-week build. Add a friend-approval flow and you're at 4-6 weeks.
- Biggest solo complexity traps:
  - Photo storage at scale: S3/R2 integration, compression, deletion policies — easy to ignore, expensive to fix later
  - Push notification scheduling: daily reminders sound simple, are surprisingly fiddly across iOS/Android with background permissions
  - Streak edge cases: timezone handling, missed days, grace periods — these are logic bugs waiting to happen and users will be furious when streaks reset incorrectly
  - Social/friend invite flow: if you add it, it's a full auth system + friend graph + notification system — that's a month of work alone
  - App Store camera permissions: both iOS and Android have review scrutiny around camera access, make sure your permission request copy is airtight

Design Handoff:
- Screens needed:
  - Onboarding screen — pick your habit (name + icon + target frequency), set a daily reminder time
  - Home / Dashboard screen — list of active habits, each showing current streak count, last check-in photo thumbnail, and a "Check In" CTA button
  - Camera / Check-in screen — full-screen camera view with a single capture button, optional short caption field, confirm/retake flow
  - Check-in Success screen — streak number animation ("Day 14!"), option to share to Instagram Stories
  - Streak History / Proof Wall screen — grid of past check-in photo thumbnails for a selected habit, tappable to expand
  - Habit Detail screen — streak stats, longest streak, current streak, check-in calendar heatmap, edit habit settings
  - Settings screen — notification time, edit habits, delete habit, account (if any)
  - Streak Broken screen — emotional moment, show what streak was lost, option to use a streak freeze or restart
- Key UI interactions:
  - Swipe or tap to open camera directly from Home (minimize friction to check in)
  - Streak number animates/pulses on success screen — this moment needs to feel rewarding
  - Long-press photo thumbnail to preview full image
  - Haptic feedback on successful check-in
  - Streak freeze prompt appears immediately when a streak is at risk (end of day, not checked in)
- Data each screen needs:
  - Home: list of habits (name, icon, streak count, last check-in timestamp, last photo thumbnail URL)
  - Camera: active habit context (name, what counts as proof — hint text)
  - Check-in Success: updated streak count, habit name, whether a milestone was hit (7, 30, 100 days)
  - Streak History: ordered list of check-ins (date, photo URL, caption) for selected habit
  - Habit Detail: full habit object (streak history, longest streak, creation date, frequency target, check-in log)
  - Streak Broken: last streak length, habit name, number of streak freezes remaining
