# Idea #16: an app that imports your ebook metadata (titles, authors, genres) to build a personal taste profile, then uses AI + Goodreads reviews to generate personalized book previews when you scan a book at bookstores, helping you decide if it's worth buying

## Version: v1 (Original)
**Date:** 2026-03-25 22:39
**Status:** submitted

## Description
an app that imports your ebook metadata (titles, authors, genres) to build a personal taste profile, then uses AI + Goodreads reviews to generate personalized book previews when you scan a book at bookstores, helping you decide if it's worth buying

### Critique
**Verdict:** NEEDS MAJOR WORK | **Score:** 5/10

The bookstore scan moment is real and the personalized AI summary angle is genuinely differentiated — but the foundation is rotten. Goodreads killed its public API in 2020, so the core data source doesn't exist. Ebook metadata import is a different nightmare on every platform (Kindle, Kobo, Apple Books). The target user — someone who buys ebooks AND shops physical bookstores regularly — is a tiny Venn diagram. The taste profile from titles/authors alone is shallow genre-matching, not real personalization. For a solo dev, the only shippable path is stripping it down to: scan ISBN → AI summary tuned to manually-stated genre preferences, using Open Library/Google Books instead of Goodreads. That's a 2-4 week build. The full vision is months of integration pain with data you don't control.
