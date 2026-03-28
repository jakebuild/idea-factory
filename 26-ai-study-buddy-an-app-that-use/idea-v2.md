# Idea #26: AI Study Buddy (Improved)
## Version: v2
**Date:** 2026-03-28 18:53
**Status:** improved

## Original Idea
AI Study Buddy — An app that uses AI to listen and track if a kid is studying or eating. The phone's microphone listens to detect if the child is still working (keyboard typing, page turning, chewing sounds). If the AI detects the child has stopped for too long, it speaks aloud to remind them.

## What Changed and Why
- **Dropped ambient audio classification entirely.** Real-time audio ML to distinguish keyboard typing from chewing from silence in a noisy household is unreliable, privacy-invasive, and app-store-hostile. Continuous mic access will get rejected by Apple and freak out parents when they realize audio is being processed. Replaced with Pomodoro timer + voluntary check-in prompts — simpler, more honest, harder to legally challenge.
- **Dropped eating mode.** Detecting chewing sounds to confirm a child is eating is both creepy and solves a non-problem. Parents don't actually need AI for this. Cutting it sharpens the value prop: one job (study sessions) done well.
- **Made the "buddy character" the product, not the microphone.** The differentiator was never the audio detection — it was the patient, encouraging voice that keeps kids on track without a parent in the room. Lean fully into a named character with personality. Kids respond to characters, not surveillance tech. The character asks check-in questions ("What are you working on?") that are harder to fake than tapping a keyboard.
- **Solved the gaming problem with active check-ins.** Instead of passive sound detection (trivially defeated by any child over 7), the buddy periodically asks what the kid is working on. The response is logged for parents. This is engagement-based accountability, not surveillance.
- **Targeted teens (13+) to sidestep COPPA.** Child data regulations for under-13 require parental consent flows, data handling documentation, and legal review. Starting with teens removes the compliance trap while validating the core loop. Expand to younger kids in v2 with a proper consent flow.

## Improved Description

**AI Study Buddy** is a voice-based study companion app for teens that uses Pomodoro sessions, a named AI character, and active check-in prompts to keep kids focused without parents hovering.

The teen opens the app, tells the buddy what they're working on, and starts a session. The buddy (a named character with a consistent personality — think a chill, encouraging tutor, not a robot timer) runs a customizable Pomodoro: 25 minutes on, 5 minute break. During the session, at random intervals, the buddy asks a check-in question out loud: "Hey, still grinding on that essay? What's your progress?" The teen responds by tapping a quick status update (or speaking a short reply). The response is logged.

At break time, the buddy announces the break with character: "Okay, you earned it — 5 minutes, then back to it." At session end, the buddy gives a short encouraging recap and logs stats to the parent dashboard.

Parents configure sessions and view logs from a separate parent view on the same device (or a web dashboard). They see: session duration, check-in responses, breaks taken. No audio recordings — just structured activity data.

The buddy has a selectable personality/name (e.g., "Max" the laid-back mentor, "Spark" the energetic hype coach). This is the stickiness — kids bond with a character. The Pomodoro is the mechanism; the character is the product.

No continuous microphone. No audio uploads. Mic is used only during active check-in response windows (a few seconds at a time), and only for speech-to-text locally. Privacy-clean by design.

## Why This Is Worth Building (Solo + AI)

A solo dev can ship this in 2–3 weeks: it's a Pomodoro timer with TTS, speech-to-text for check-ins, a simple log, and a parent view — all solved problems with solid APIs (ElevenLabs or OpenAI TTS, Whisper for STT). The differentiator — the character personality and check-in system — is prompt engineering and UX, not novel ML. The market gap is real: Forest and Focus@Will are adult-focused; nothing has a compelling character-driven experience for teens specifically.

## Solo MVP Scope

- **What's in v1:**
  - Session setup: subject, duration, break interval
  - Active session screen with Pomodoro timer and buddy character
  - TTS voice for session start, break announcements, session end encouragement
  - Random check-in prompts during session (text response, no mic required in v1)
  - Session log: duration, check-in responses, break count
  - Parent view (same device, PIN-locked) showing session history
  - 2 selectable buddy personalities (different name, tone, phrase sets)

- **What's out of v1:**
  - Microphone / speech-to-text (add in v1.1 after core loop validated)
  - Cross-device parent dashboard (web or separate device — save for v2)
  - Push notifications to parent device
  - Pattern learning / adaptive scheduling
  - Under-13 / COPPA consent flows
  - Eating mode (permanently cut)
  - Ambient audio detection (permanently cut)

- **Estimated build time with AI coding tools:** 2–3 weeks (React Native or Flutter, OpenAI TTS API, local storage for session data, simple PIN-protected parent view)

## Open Questions

- Which platform first — iOS or Android? iOS-first is safer for App Store consistency; Android opens faster for side-loading during beta.
- What's the right check-in frequency? Too often feels like surveillance; too rare loses value. Needs user testing — start with 1 check-in per 15 minutes.
- Does TTS voice quality matter enough to pay for ElevenLabs vs. using free OS TTS? Run a quick test with real teens before committing to API costs.
- How does the parent view work if parent and child share one device? Is a PIN-locked tab enough, or does this need two separate apps?
- Is there willingness to pay? Freemium (free for 1 buddy, pay for more personalities + longer history) or one-time purchase?

## Design Handoff

List every screen/view the app needs:

- **Screen 1: Onboarding** — Welcome screen, buddy selection (2 characters shown with name + personality blurb), age gate (13+ confirmation), parent PIN setup. Key elements: character illustrations, short personality description, PIN input.
- **Screen 2: Home / Session Setup** — Logged-in home. Shows today's session count, streak, last session summary. Big "Start Session" CTA. Subject input field, duration picker (15/25/45 min), break interval picker (5/10 min). Buddy character shown idle/animated.
- **Screen 3: Active Session (Studying Phase)** — Full-screen timer countdown. Buddy character animated (idle studying pose). Subject label at top. Session progress bar. Pause button. Check-in prompt appears as a speech bubble overlay at random intervals with 3 quick-tap response options ("Making progress", "Stuck on something", "Almost done") + optional free-text.
- **Screen 4: Break Screen** — Timer countdown for break. Buddy character in relaxed pose. Encouraging message from buddy ("You crushed that block!"). Auto-returns to studying phase when break ends. Skip break button.
- **Screen 5: Session Complete** — Summary card: time studied, breaks taken, check-in responses shown as a mini log, buddy congratulation message. "Start Another" and "Done for Now" buttons. Share stats (optional).
- **Screen 6: Parent View (PIN-locked)** — Accessed via gear icon + PIN. Shows: list of recent sessions (date, subject, duration, check-in summary), per-subject breakdown, streak calendar. Read-only. No audio data.
- **Screen 7: Settings** — Buddy selection (switch character), session defaults (default duration/break), parent PIN change, notification preferences (session reminders), app version/about.

**Key interactions:**
- Tap "Start Session" → subject input → duration picker → session begins with buddy voice greeting
- During active session: check-in speech bubble appears → tap one of 3 response options → buddy acknowledges and session continues
- Timer hits 0 → buddy announces break → break screen auto-starts → break ends → buddy voice prompts back to studying
- Pause button → session pauses, buddy says "Taking a quick break?" → resume or end session
- Session complete → summary screen → tap "Done" → returns to Home, streak updates
- Home gear icon → PIN prompt → Parent View (no navigation back without PIN exit)

## Version History
- v1: Original idea
- v1.1: Critique — audio classification unreliable, privacy nightmare, kids will game passive detection, eating mode is creepy/useless
- v2: Improved — dropped audio surveillance entirely; repositioned as character-driven Pomodoro companion with active check-ins; teens-first for COPPA; TTS + check-in prompts replace microphone detection
