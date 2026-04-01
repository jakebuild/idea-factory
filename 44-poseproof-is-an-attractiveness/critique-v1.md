Verdict: NEEDS MAJOR WORK
Score: 4/10
What's Actually Good:
- The value prop is instantly legible: "record a short video, get the best dating-app frame" is clearer than generic attractiveness coaching.
- The reveal moment is stronger than a plain selfie rater because users can see a concrete before/after instead of a fake-feeling number.
- A constrained coaching loop like "chin lower" or "camera higher" is smarter than dumping ten vanity metrics on the user.
Brutal Feedback:
- "Copies the UMax model" is not a strategy. It is a confession that you are entering a shallow, copycat category with no moat, no trust advantage, and no reason users pick you over the app they already know.
- The core promise is built on UNVERIFIED computer vision quality. Picking the "best" frame based on angle, smile, eye openness, and posture sounds neat until the model starts choosing the creepiest over-sharpened half-blink frame and calls it elite. If the output feels wrong even 20% of the time, the whole app becomes a joke.
- "Attractiveness app" is doing a lot of reckless work here. Users do not actually want an attractiveness score; they want more matches. Those are not the same thing. You are proposing a confidence casino with no proof it improves real outcomes.
- The scoring system will look deeply made up unless you have real training data or at least a convincing evaluation framework. "Angle 82, smile 74, posture 68" is numerology with a camera roll.
- The retention story is thin. Why does the user come back after day 1? Most people need one decent profile photo burst, not a recurring subscription to be told their chin is weird.
- Subscription is optimistic bordering on delusional. This is a utility, not obviously a habit. People will use it in a panic before uploading photos, then churn immediately.
- A five-second selfie video is more friction than a photo upload, not less. You are asking users to perform for the algorithm before they even know if the app is credible.
- The "shareable" claim is flimsy. People share funny results, brutal roasts, or glow-up transformations. They do not automatically share "this app picked frame 37 and told me to lift my phone."
- Dating-photo advice is crowded with free alternatives: friends, Reddit roast threads, TikTok tips, existing photo rankers, and just taking 50 shots manually. Your app has to beat free and familiar, not just function.
- Hinge, Tinder, and Instagram are named like built-in relevance, but there is no actual platform integration here. Without verified deep-links, import flows, or outcome tracking, those brand names are cosmetic.
- The idea hides ugly implementation work. Video capture, frame extraction, face/pose detection, score normalization, on-device vs server processing, latency, privacy messaging, bad-light handling, multi-face rejection, and retries are not "just use a vision API."
- Privacy is a real conversion killer. You are asking people to upload face videos to an attractiveness scorer. That is exactly the kind of thing users hesitate to trust, and exactly the kind of thing app reviewers can scrutinize.
- The app is vulnerable to instant feature-cloning by any existing photo app. If this works, a bigger player can add "best frame for dating profile" in one sprint and erase you.
Key Questions:
- What evidence says users want an attractiveness score instead of simply "pick my best dating photo"?
- Which exact API or model decides the best frame, and how will you verify it is not garbage? UNVERIFIED until tested.
- Why does the user come back after day 1?
- What makes this different enough from manual burst-photo selection, Apple/Google best-shot logic, or existing selfie rating apps?
- How will you avoid making users feel insulted, creeped out, or falsely judged when the score is obviously subjective?
- Can you prove any recommendation improves actual match rate, not just app-generated vanity metrics?
Suggestions:
- Cut the pseudo-scientific attractiveness framing and reposition this as a narrow "best profile photo picker" with one actionable retake tip.
- Drop subscription for MVP. Charge once, offer credits, or make it a cheap utility. Recurring billing before retention exists is fantasy.
- Start with photo batch ranking before video. It is less creepy, easier to build, easier to debug, and closer to existing user behavior.
- If you insist on video, keep the first version brutally simple: capture, extract frames, reject blurry/bad-eye-closed shots, let the user compare top 3. Do not pretend the model understands attractiveness.
- Validate manually before coding the full product: run 30 users through a concierge workflow and see whether they trust the selected frame enough to post it.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — a crude MVP is possible, but only if you slash it down to video capture, frame extraction, basic quality heuristics, and a simple compare screen. The full pitch with credible scoring, retake coaching, subscriptions, privacy confidence, and polished mobile UX is not a clean 2-4 week solo build.
- Biggest solo complexity traps: unreliable vision-model output; tuning frame quality heuristics so results do not feel random; mobile video upload and processing latency; privacy/security handling for face video; App Store review risk around attractiveness claims; subscription work before retention is proven; building outcome credibility without real user data.
