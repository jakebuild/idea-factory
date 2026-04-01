Verdict: NEEDS MAJOR WORK
Score: 4/10
What's Actually Good:
- The pain is real. People absolutely obsess over picking the least bad dating photo.
- "We found your best frame from a 5-second turn" is a clearer hook than generic AI attractiveness sludge.
- There is a narrow utility hiding in here if you stop pretending the app can measure human desirability and just make it a frame picker with light coaching.
Brutal Feedback:
- "Copies the UMax model" is not positioning. It is you admitting the whole concept starts as a derivative clone with a camera-input gimmick.
- The core differentiator is UNVERIFIED. Everything depends on a vision API reliably picking the best frame from a moving selfie and producing advice that does not feel random. If the picks are mediocre, the product is dead on arrival.
- "Attractiveness app" is a self-own. It sounds shallow, creepy, and embarrassingly easy to dunk on. It also invites trust problems because users know attractiveness is subjective and context-dependent.
- Your scoring categories sound fake-scientific. Angle, smile, eye openness, and posture are proxies, not truth. You are packaging taste as math and hoping people do not notice.
- The "better than the photo you were about to post" comparison is especially flimsy. Better by what standard? Swipe rate? Message rate? Human vote? You have no ground truth, so that delta score is theater.
- The retention story is weak. Why does the user come back after day 1? Most people will use this once before updating a profile, maybe reshoot twice, then disappear. That is not subscription behavior. That is a disposable utility.
- Hinge, Tinder, and Instagram should not be lumped together like they are the same use case. They are not. Different contexts, different photo norms, different user intent. One score for all three is lazy product thinking.
- Recording a 5-second slow-turn video is more friction than uploading a selfie. You are adding effort and calling it innovation. A lot of people will quit before the magic moment.
- "One blunt retake instruction" sounds neat until the advice is wrong, obvious, or insulting. Then the app feels dumb and mean at the same time.
- Users can already scrub a video in their camera roll and pick a frame manually for free. Your product only matters if the auto-pick is consistently better than manual selection, which you have not proven.
- Privacy is not some footnote here. You are asking people to upload face videos for dating optimization. That immediately raises deletion, consent, minors, and creep-factor problems that a solo dev does not want.
- The monetization claim is hand-wavy. A weak-retention utility with an untrusted model is not a clean subscription business just because UMax charges money.
Key Questions:
- Why does the user come back after day 1?
- What evidence will make anyone trust that your chosen frame performs better than the one they would pick themselves?
- Can one real external API actually handle video frame extraction, face feature quality checks, and useful retake advice at consumer-grade reliability?
- Are you willing to kill the fake precision and reposition this as "profile photo picker" instead of "attractiveness scoring"?
- What happens when the app picks a frame the user hates or gives advice that feels insulting or obviously wrong?
- How are you handling face-video storage, deletion, minors, and moderation without turning this into a support and compliance mess?
Suggestions:
- Drop the word "attractiveness." It makes the product sound gross and overclaims intelligence you do not have.
- Narrow the MVP to one job and one context: pick the best first-profile photo for one platform, probably Hinge.
- Validate the core differentiator before building the app shell. Run 30-50 real selfie clips through the exact pipeline and compare the chosen frames against human picks.
- Replace the fake dashboard with simpler output: best frame, two reasons it won, one concrete retake instruction.
- Do not start with subscription. Test paid credits or a one-time unlock because the repeat loop looks weak.
- If the result is not clearly better than manual scrubbing in the phone gallery, kill the idea immediately.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — a rough demo is plausible, but a product people trust enough to pay for is not. The UI is easy. The hard part is proving the output is not random and handling sensitive video responsibly.
- Biggest solo complexity traps: unverified external vision API quality; mobile video upload and frame extraction performance; camera permission and compression headaches; storing and deleting sensitive face videos safely; moderation around minors and sexual content; weak retention that makes subscription economics nonsense; trying to support Hinge, Tinder, and Instagram advice in one MVP; user backlash if the scoring feels insulting or inaccurate.
