Verdict: NEEDS MAJOR WORK
Score: 4/10
What's Actually Good:
- The user problem is real. People absolutely do agonize over which selfie to post, especially for dating apps.
- The reveal moment is sharper than generic "AI photo rating" sludge. "We found your best frame from a 5-second turn" is at least a clearer hook than dumping a vague attractiveness score on one image.
- The scope can be compressed into a narrow MVP if you stop pretending the app understands beauty and treat it like a frame-picker plus basic pose feedback.
Brutal Feedback:
- "Attractiveness app" is a terrible starting point. It makes the product sound shallow, creepy, and easy to mock before anyone even tries it.
- "Copies the UMax model" is not a strategy. It is a confession that you have no moat and no original insight beyond changing the input from photo to video.
- The whole differentiator is UNVERIFIED. You are betting the product on a vision API being able to reliably pick the best frame from a 5-second selfie turn and give useful retake advice. If that output is mediocre, the entire app collapses.
- The promise is fake-precise. Scoring angle, smile, eye openness, and posture sounds scientific, but "best dating photo" is not a clean measurable truth. It is subjective, context-heavy, and culture-dependent. You are dressing taste up as math.
- The comparison gimmick is manipulative. "Here is how much better this frame is than the photo you were about to post" assumes the app can judge romantic outcome from pixels alone. It cannot. That delta score will be arbitrary unless you have real outcome data, which you do not.
- The retake loop is weaker than you think. Why does the user come back after day 1? Most people will record once, grab a decent frame, maybe retry twice, and leave. This smells like a one-session novelty, not a subscription business.
- Dating-app optimization is not the same as Instagram optimization. A strong Hinge first photo and a strong Instagram selfie do not obey the same rules. Bundling Tinder, Hinge, and Instagram into one score is lazy positioning.
- Video capture adds friction, not magic. Uploading a selfie is easy. Recording a 5-second slow-turn video with decent lighting, framing, posture, and patience is more effort. You are asking for more work up front while claiming the app is simpler.
- The product gets dangerous the second it is blunt. "Chin lower" sounds crisp in the pitch. In reality, a lot of users will hear "your face looks worse from this angle" and feel insulted by a robot with zero credibility.
- Privacy and moderation are not side quests here. You are collecting face videos for attractiveness scoring. That is a high-trust ask. You will get minors, couples, shirtless clips, sexually suggestive content, and people uploading others without consent. Solo-dev "ship fast" confidence dies right there.
- Subscription is wishful thinking. This is not obviously recurring value. It is closer to a one-off paid utility or impulse purchase. Trying to force a subscription onto a weak repeat-use loop is how you get churn in 48 hours.
- The solo-dev estimate is sugar high nonsense. A polished funnel with capture, video processing, frame extraction, scoring, retake guidance, privacy/deletion flows, payments, and platform-specific result pages is not "a few weeks" unless the quality bar is low enough to embarrass you.
- There is still no proof users want AI judgment over a friend, group chat, or their own taste. "Send three selfies to a friend" is free, trusted, and already installed in every phone.
Key Questions:
- Why does the user come back after day 1?
- What evidence will make anyone trust that your "best frame" actually performs better on Hinge or Tinder?
- Can the core frame-picking and retake advice work well enough with one real vision API, on real user videos, before you build anything else?
- Are you willing to drop "attractiveness" and reposition this as profile-photo optimization instead of pretending to score human desirability?
- Is this a one-time utility, a paid credit product, or a subscription? Right now the monetization logic is hand-waving.
- How will you handle minors, consent, deletion, and face-video storage without turning the product into a compliance headache?
Suggestions:
- Cut the word "attractiveness" from the product. It creates backlash and overpromises model intelligence you do not have.
- Narrow the MVP to one job: "record a short clip, get the cleanest first-profile photo for Hinge." Not Tinder, Hinge, and Instagram all at once.
- Validate the core differentiator before building the app shell. Run 30-50 real clips through the exact API pipeline and check whether humans consistently agree the picked frame is actually better.
- Replace fake precision scoring with simpler output: best frame, two concrete reasons it won, one retake instruction.
- Drop subscription for MVP. Try paid credits or a cheap one-time unlock first, because the repeat loop is weak.
- If the frame picker is not clearly better than manual scrubbing through the video roll, kill the idea immediately.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — a rough demo, yes; a product that feels trustworthy enough to pay for, probably not. The technical shell is shippable, but the product lives or dies on model quality, privacy friction, and whether users believe the output.
- Biggest solo complexity traps: unreliable frame scoring from an external vision API; mobile video upload and frame extraction performance; storage and deletion of sensitive face videos; moderation for minors and sexual content; fake-scientific scoring that users reject; weak retention making subscription economics collapse; platform-specific advice logic for Hinge versus Instagram; onboarding friction from forcing users to record a staged turn video instead of uploading an existing photo.
