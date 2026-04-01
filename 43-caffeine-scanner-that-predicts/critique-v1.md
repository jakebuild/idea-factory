Verdict: NEEDS MAJOR WORK
Score: 5/10
What's Actually Good:
- The core pain is real. People absolutely do regret late caffeine and would like a quick "is this going to wreck tonight?" answer.
- The framing is stronger than a generic caffeine tracker because "sleep damage before you drink" is emotionally legible and instantly useful.
- A narrow MVP is possible if you stop pretending to predict biology with confidence and instead deliver rough guidance for common packaged drinks.
Brutal Feedback:
- "Predicts when you will actually fall asleep tonight" is the kind of claim that sounds amazing in a pitch and turns into bullshit the second a real human uses it twice. Sleep onset depends on stress, food, alcohol, exercise, tolerance, medication, sleep debt, and whether the user is doomscrolling at 1:00 a.m. You are selling fake precision on top of noisy inputs.
- The scanner idea is much messier than the sentence makes it sound. Packaged drinks sometimes have caffeine labels, sometimes don't, cafes often don't, pre-workouts are chaos, and menu items are wildly inconsistent. Your magic moment depends on extracting reliable caffeine dose data from unreliable packaging and even worse menu text. That is UNVERIFIED.
- "Recent sleep" sounds harmless until you ask where it comes from. Manual input is friction. Apple Health integration is extra work. Android health data is another path. If you skip integration, the prediction gets dumber. If you add integration, solo scope expands immediately.
- The output "how hard tomorrow's energy will crash" is hand-wavy nonsense unless you have a model backed by something better than vibes. Users will sniff out made-up wellness math fast.
- The retention loop is weak in its current form. Yes, caffeine regret is common. No, that does not automatically create daily retention. Ask the hard question: Why does the user come back after day 1? If they already know "don't drink a giant cold brew at 5 p.m.," the app becomes a one-week novelty.
- The moat is thin. A calculator, OCR, and a scary result screen can be cloned in a weekend. If the answer is basically "caffeine half-life plus bedtime," dozens of apps, blog posts, and AI chats can approximate it.
- Liability and trust are not trivial here. The more medical and predictive the language gets, the more you invite users to treat it like health advice while your model is basically educated guesswork.
- Scan-anything scope is solo-dev bait. Coffee cans, energy drinks, cafe menus, pre-workouts, custom orders, dose adjustments, tolerance, planned bedtime, sleep history, safe alternatives, and tomorrow crash scoring is too much surface area for a 2-4 week build unless the product is mostly smoke and mirrors.
- The swap-to-a-safer-option flow is underspecified. Safer option from where? A curated database? Nearby products? Generic suggestions like "half-caf" and "smaller size"? If the substitute layer is weak, the app ends at "this is bad for you," which is useful exactly until the user ignores it.
- OCR on cafe menus sounds cool and demos well, but real-world menus are messy, abbreviated, seasonal, image-heavy, and often do not include caffeine amounts at all. Then what? Are you estimating caffeine from drink type and size? If yes, say that, because the app is really a guess engine wearing scanner makeup.
Key Questions:
- What is the actual MVP input source: barcode scan of packaged drinks, manual search, camera OCR of menus, or all three?
- Where does caffeine dose data come from for cafes and pre-workouts when labels are missing or inconsistent?
- What is the real prediction model beyond "dose plus half-life plus bedtime"? Be honest.
- Why does the user come back after day 1?
- What specific behavior changes because of this app: smaller size, earlier timing, alternative product, skipped purchase?
- How will you communicate uncertainty so the app feels useful instead of fake?
Suggestions:
- Cut the science-fiction language. Replace "predicts when you will actually fall asleep" with a risk range like low / medium / high sleep disruption and latest recommended cutoff time.
- Cut cafe menu scanning from MVP unless you verify a data source. Start with packaged drinks plus manual caffeine entry for everything else.
- Kill the "tomorrow energy crash" score unless you can justify it with something better than vibes. It weakens trust.
- Make the value prop behavioral, not clinical: "Can I drink this now?" "What is the latest safer size?" "What happens if I choose half?"
- Add a simple simulation UI that lets users compare full size vs half size vs decaf vs earlier timing. That is more actionable than fake biometrics.
- If you want retention, build history and personalized cutoff learning: "You usually miss your bedtime when you go above X mg after Y p.m." That at least creates a reason to return.
- Position it as a decision aid, not a physiological oracle.
Solo Dev Reality Check:
- Can one person ship this in 2-4 weeks with AI coding tools? MAYBE — but only if the MVP is brutally cut down to packaged drinks, manual bedtime input, simple heuristics, and honest uncertainty. The full "scan anything and predict tonight plus tomorrow" version is not a 2-4 week solo build.
- Biggest solo complexity traps: cross-platform camera/barcode/OCR work; building or licensing a caffeine database; inconsistent cafe/pre-workout data; Apple Health or Android health integrations; calibration/personalization logic; presenting uncertain predictions without destroying trust; feature creep from "scanner" into search, substitutions, history, reminders, and health analytics.
