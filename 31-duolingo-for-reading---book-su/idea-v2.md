# Idea #31: (Improved)
## Version: v2
**Date:** 2026-03-30 17:09
**Status:** improved

## Original Idea
Duolingo for Reading - Book summaries daily

## What Changed and Why
- The product is no longer a generic summary feed. v1 becomes a 14-day "reading retention" course for one narrow topic, delivered as one lesson per day from public-domain or creator-permission nonfiction. This fixes the weakest part of the original idea: commodity summaries with no real reason to exist.
- Passive consumption is replaced with an active loop: short excerpt, 3 key idea cards, 2 recall questions, 1 reflection, and a saved action takeaway. This directly addresses the critique that summaries create fake productivity instead of measurable comprehension and recall.
- The scope is cut to something one person can actually ship: one web app, one curated topic track, 14-21 handcrafted lessons, local or simple account-based progress, and a review queue. The library/catalog/recommendation problem, licensing fog, and fake "Duolingo" complexity are removed from v1.

## Improved Description
Build a focused web app called "5-Minute Reading Retention" for people who want to remember and apply nonfiction ideas instead of skimming summaries once and forgetting them. The MVP is not "all books" and not a summary marketplace. It is one curated 14-day course around a single niche, for example decision-making or focused work, built from legally safer source material: public-domain nonfiction, essays, or direct creator-permission texts.

Each day has one lesson that takes under 5 minutes:
- a short source excerpt or tightly edited idea summary
- 3 key idea cards
- 2 recall questions with instant feedback
- 1 reflection prompt
- 1 "do this today" takeaway the user can save

The product's job is not to help users feel caught up on books. Its job is to improve comprehension and recall from short nonfiction reading sessions. Progress is measured by lessons completed, recall accuracy, and review cards cleared.

Why users come back in week 2: the app creates unfinished value. Saved takeaways and missed recall questions resurface in a spaced review queue, so the second week is about keeping what you learned, not consuming more content. Users are also progressing through a finite 14-day track, which gives a clear reason to return until the course is completed.

This is viable because it removes the two structural traps from the original idea: copyright-risky summary scale and empty retention mechanics. Instead of pretending to be a better Blinkist, it offers a narrower but more defensible use case: "help me remember and use what I read in 5 minutes a day."

3 biggest assumptions that could kill the product:
- UNVERIFIED: Busy nonfiction readers actually want a daily recall-and-reflection product, not just faster summaries. Fallback plan: if retention is weak, reposition it as a companion tool for newsletters, essays, and personal reading notes instead of book-based learning.
- VERIFIED: A solo developer can prepare enough legally safer content for MVP if the scope is limited to one topic and 14-21 lessons. This is realistic only with manual curation and cleanup included in the build.
- FALSE: Streaks and daily notifications alone are enough to retain users. The critique is right; retention has to come from the review queue and course progression, not from cosmetic gamification.

## Why This Is Worth Building (Solo + AI)
This is small enough for one person to ship with AI coding tools because the product is a tight lesson-and-review loop, not a giant catalog business. It also has a clearer testable promise than the original: if users finish lessons and come back to clear review cards in week 2, the product is creating real learning value instead of summary theater.

## Solo MVP Scope
- What's in v1: a responsive web app with onboarding, one 14-day topic track, 14-21 manually prepared lessons, lesson player, recall quiz, reflection capture, saved takeaways, review queue, progress tracking, and simple reminder settings
- What's out of v1: all-books library, AI-generated summaries at scale, recommendations, social features, streak-centric gamification, user-generated content, comments, marketplaces, payments, partnerships, mobile app, audio, and any copyrighted-content pipeline that is not already permissioned or public-domain
- Estimated build time with AI coding tools: 2-4 weeks total, including 5-7 days for content prep and fact-checking of 14-21 lessons, 7-10 days for UI/app logic, and 2-4 days for storage/auth/reminder integration and QA

## Open Questions
- Which niche has the strongest demand for the first course: decision-making, psychology, focus, or startup thinking?
- Which legally safe source set will be used for v1: public-domain books only, public-domain essays, or a small set of creator-permission texts?
- UNVERIFIED: Will users complete the daily reflection step, or should it be optional in the first build?
- UNVERIFIED: Is simple email reminder delivery worth including in the first 2-4 week build, or is an in-app review queue enough for early validation?

## Design Handoff
List every screen/view the app needs so a designer or AI design tool can start immediately:
- Screen 1: Landing / Value Prop — explains the product in one glance, shows the 5-minute lesson loop, names the first 14-day course, and includes CTA to start
- Screen 2: Onboarding / Topic Intro — introduces the first course, explains daily lesson + review queue, asks for goal and reminder preference, shows expected time per day
- Screen 3: Home / Today — main dashboard showing today's lesson status, pending review count, current course day, recent saved takeaway, and CTA to continue
- Screen 4: Lesson Reader — displays the short excerpt or edited lesson text, progress indicator, source attribution, and button to move into key idea cards
- Screen 5: Key Idea Cards — shows 3 core idea cards one by one, with short examples and navigation to the recall step
- Screen 6: Recall Quiz — presents 2 questions with instant feedback, explanation, and score for the lesson
- Screen 7: Reflection / Action Capture — asks one short reflection question and one "what will you do today?" prompt, then saves the takeaway
- Screen 8: Lesson Complete — confirms completion, shows recall score, saved takeaway, next review timing, and next-day preview
- Screen 9: Review Queue — lists due review cards and missed questions from prior lessons, lets users clear them quickly, and updates retention progress
- Screen 10: Takeaways Library — shows all saved action takeaways by date and lesson, with simple search/filter by status
- Screen 11: Course Progress — visual timeline of days 1-14, completed lessons, average recall score, and remaining lessons
- Screen 12: Settings — reminder toggle, account/local progress settings, reset progress, and source/legal info about the content used
Key interactions:
- User starts from Landing or Home, enters today's lesson, completes cards, quiz, and reflection, then lands on Lesson Complete
- Incorrect or older items move into Review Queue and reappear until cleared
- Saved takeaways can be revisited from Home and Takeaways Library
- Course Progress links back into any completed lesson or the next available lesson

## Version History
- v1: Original idea
- v1.1: Critique - generic summary app, fake differentiation, weak retention, content/licensing risk, no real learning loop
- v2: Improved - narrowed to a legally safer 14-day nonfiction learning course with active recall, reflection, and spaced review instead of passive summaries
