# Idea #1: shake phone for kids to collect stars

## Version: v1 (Original)
**Date:** 2026-03-19 20:30
**Status:** submitted

## Description
shake phone for kids to collect stars

## Criteria Check
- [ ] Fun for kids
- [ ] Works offline
- [ ] Simple UI
- [ ] Motion-based

### Critique
**Verdict:** WORTH BUILDING | **Score:** 6/10

The core mechanic is physically fun for young kids and technically trivial to build. Shake detection + star animations = real weekend MVP. The fatal flaw as described: zero retention loop. Stars collect toward nothing, meaning kids bounce in 90 seconds. The fix is one mechanic away — add a visual goal container (jar/rocket/chest) that fills up, triggers a "prize moment" celebration at 100%, then resets. That's the entire product. Biggest technical risks: iOS DeviceMotion permission gate (silent failure on device), audio autoplay browser restrictions, and performance on cheap Android tablets. Ship as a PWA first to skip App Store compliance theater. Design handoff in critique-v1.md.
