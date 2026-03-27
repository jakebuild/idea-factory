# Idea #14: AI advisor that tell me if a book is worth reading based on my taste, snap cover or search by title, get personalized verdict in 30 seconds

## Version: v1 (Original)
**Date:** 2026-03-25 21:29
**Status:** submitted

## Description
AI advisor that tell me if a book is worth reading based on my taste, snap cover or search by title, get personalized verdict in 30 seconds

### Critique
**Verdict: WORTH BUILDING — Score: 7/10**

The core use case is sharp and relatable. Personalized book verdicts ("is this good FOR ME?") is genuinely unsolved vs. generic review sites. Repeat usage is high — every book pickup is a trigger. The cold start problem is brutal though: a new user has zero taste data, so early verdicts feel generic and users churn before personalization kicks in. StoryGraph is a real competitor. Camera/OCR recognition is harder than it looks and will eat a week of solo dev time — skip it for v1. Lead with web (not native app), solve cold start with a fast onboarding taste quiz (Tinder-swipe over 10 books), and nail the verdict format (opinionated paragraph from a "well-read friend", not a star rating). Doable in 2-4 weeks web-first without camera.
