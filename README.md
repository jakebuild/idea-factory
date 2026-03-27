# Idea Factory 🏭

A product development pipeline that takes raw ideas through AI-powered critique and improvement rounds, producing validated specs ready for design.

Powered by the [`idea-factory`](https://github.com/jakebuild/idea-factory) openclaw skill.

---

## How It Works

```
idea-factory submit "your idea"
       ↓
   GitHub Issue created
       ↓
   Critique agent (tmux) → critique-v1.md + score
       ↓
   Improve agent (tmux) → idea-v2.md
       ↓
   Round 2 (repeat, stops early if score ≥ 8/10)
       ↓
   Validate → ready-for-design label
```

Each idea lives in its own numbered folder with versioned files at every stage.

---

## Quick Start

```bash
# Submit an idea — triggers the full 2-round pipeline automatically
idea-factory submit "math quiz game for kids"

# Watch the critique agent run
tmux attach -t idea-critique-1

# Check results
cat ~/openclaw_workspace/idea-factory/1-*/critique-v1.md

# View on GitHub
gh issue list --repo jakebuild/idea-factory
```

---

## Idea Folder Structure

```
<n>-<slug>/
  idea-v1.md            # original submission
  critique-v1.md        # critique: Verdict, Score, Brutal Feedback, Design Handoff
  idea-v2.md            # improved version after round 1
  critique-v2.md        # critique of v2
  idea-v3.md            # final improved version
  critique-score.txt    # e.g. "8/10"
  github-issue-num.txt  # GitHub issue number
  status.txt            # submitted | in-review | improved | ready-for-design
```

---

## Commands

| Command | Description |
|---------|-------------|
| `idea-factory submit <idea>` | Submit + run full pipeline |
| `idea-factory critique <n>` | Run critique agent for idea #n |
| `idea-factory improve <n>` | Run improve agent for idea #n |
| `idea-factory refine <n> [rounds]` | Manual multi-round refine |
| `idea-factory validate <n>` | Mark ready for design |
| `idea-factory list` | Show all ideas and status |

---

## Builder Context

- **Solo developer** — one person, no team
- **Vibe coding with AI** — fast iteration, AI-assisted
- **Scope:** shippable in 2–4 weeks with AI coding tools
- Ideas requiring a team or months of grind get called out in the critique

---

## GitHub

Issues tracked at: `jakebuild/idea-factory`
Labels: `idea`, `ready-for-design`
