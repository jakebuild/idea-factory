Verdict: WORTH BUILDING
Score: 6/10
What's Actually Good:
- This version finally stops pretending a phone can read your soul. Cutting pulse, fake calm detection, and multi-context scope was the adult decision.
- Interviews are a real wedge. People already rehearse for them, tolerate awkward prep rituals, and have a short-term reason to come back.
- The core loop is understandable in one sentence: answer, get metrics, do one reset, retake, compare.
- For a solo builder, transcript + pace + filler words is a sane enough MVP surface. It is not magic, but at least it is shippable.
Brutal Feedback:
- The product is still dangerously close to being a prettier voice memo with a filler-word counter. That is useful for five minutes, not obviously worthy of a dedicated app.
- Your moat is thin to nonexistent. "Curated interview questions plus transcript metrics" is exactly the kind of thing a bigger interview-prep app, an AI coach, or even ChatGPT with voice can clone in a weekend.
- The retention story is better, but still soft. Why does the user come back after day 1? "Because they have more interviews" is not a product loop, it is borrowed urgency from the job market.
- The reset drills are doing more work than you admit. If those drills are generic fluff, the retake improvement will feel fake and users will conclude the app is grading random noise.
- Pace and filler words are blunt instruments. A candidate can game both and still give a rambling, weak, generic answer. The app risks optimizing users toward sounding slower rather than sounding better.
- "Build a reusable answer library" sounds nice until you remember most people hate listening to themselves and do not want to maintain a personal archive of mediocre interview recordings.
- The fixed question bank is finite, but it is also fragile product content. If the questions feel too generic, people bounce. If you make them too broad, the advice becomes mush. If you customize them later, scope expands again.
- Mobile-first is not automatically smart here. Interview practice often happens at a desk, with notes, job descriptions, and a laptop open. Forcing mobile may make the product feel more constrained than focused.
- Transcription latency and accuracy are still UNVERIFIED at the exact moment the product needs to feel snappy. If the processing step drags or the transcript mangles speech, the reveal dies.
- The name and original framing still drag anxiety-app baggage behind the product. If users think this is emotional diagnosis in disguise, trust drops immediately.
- You are undercounting polish work. Audio permission edge cases, interrupted recordings, bad mics, background noise, transcript edits, and storage/privacy UX are where solo projects go to waste two extra weeks.
- The "simple progress dashboard" is the first place this becomes fake SaaS garnish. If the trend view is shallow, it adds clutter. If it is deep, it adds scope. Right now it smells like roadmap inflation.
Key Questions:
- Why does the user come back after day 1 when one or two rehearsals might already feel "good enough"?
- What is the real value beyond metrics: better answer structure, better confidence, or just accountability to practice?
- Are the reset drills actually effective, or are they just a ceremonial pause between two takes?
- Is mobile-first genuinely better for this use case, or is that just a build-style bias?
- What happens when transcription is wrong or filler words are counted badly? Can the user correct anything, or do they just stop trusting the app?
- Is the MVP actually 20 high-quality questions, not 40 average ones, because content quality matters more than catalog size?
Suggestions:
- Cut the dashboard from MVP unless testing proves people care. Keep history at the question level only.
- Start with 15-20 excellent interview questions and 5 strong reset drills. A smaller bank with sharper guidance beats a bloated generic bank.
- Make the actual promise harsher and clearer: "practice tighter interview answers," not "feel calmer" and not "improve communication" in general.
- Add one lightweight structure check before adding any new sensing. Even a simple rubric like STAR / direct answer / missing outcome is more valuable than another raw speech metric.
- Consider shipping as a web app first unless mobile recording quality is clearly better enough to justify the extra friction and polish burden.
- Test whether users will repeat the loop across multiple days before investing in trend charts, session archives, or any "answer library" narrative.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — if the MVP is aggressively cut to question bank, record/transcribe, filler + pace metrics, one reset flow, and basic per-question history. The current spec is close, but the dashboard/content/polish layer will try to bloat it.
- Biggest solo complexity traps: transcription latency and cost choices; mobile audio permission and recording reliability; making reset drills feel credible instead of generic; content curation quality; avoiding fake precision in weak metrics; deciding between mobile and web early enough to avoid rebuilding core flows; privacy/storage UX for recordings and transcripts.
Design Handoff:
- Screens needed: onboarding/positioning, question bank, question detail/prep, record answer, processing, first take result, reset drill, retake comparison, question history, session detail, settings/privacy
- Key UI interactions: pick a question, record first take, wait through processing, review transcript and metrics, start reset drill, record retake, compare deltas, save session, reopen prior attempts by question
- Data each screen needs: onboarding state and permission state; question categories, prompts, and prior-attempt counts; selected question metadata and prior best metrics; live recording state and timer; transcription/analysis job status; first-take transcript, pace, filler count, length, and self-rating; selected reset drill content and countdown/checklist state; side-by-side take metrics and improvement deltas; per-question session list with dates and best/latest stats; full session transcript/metrics for each attempt; privacy preferences, retention settings, and delete actions
