# Idea Factory

This repo is a product development pipeline powered by the `idea-factory` openclaw skill. Ideas are submitted, critiqued, and improved through AI agent rounds, then tracked as GitHub issues.

## How It Works

The `idea-factory` CLI (installed via openclaw) drives the workflow:

```
idea-factory submit "your idea description"
```

This triggers a multi-round pipeline:
1. **Submit** — creates local files under `~/workspace/idea-factory/<n>-<slug>/`, creates a GitHub issue, and pushes to git
2. **Critique** — spawns a `claude` agent in a tmux session to roast the idea, write `critique-v<n>.md`, and score it
3. **Improve** — spawns another agent to address critique and write `idea-v<n+1>.md`
4. **Refine** — runs 2 rounds of critique+improve by default; stops early if score ≥ 8/10
5. **Validate** — marks idea as `ready-for-design`, updates the GitHub issue label

## Repo Structure

Each numbered folder is a synced snapshot of an idea at some refinement stage:

```
<n>-<slug>/
  idea-v1.md          # original submission
  idea-v2.md          # improved version (after round 1)
  critique-v1.md      # critique of v1 (Verdict, Score, Brutal Feedback, Design Handoff)
  critique-score.txt  # e.g. "7/10"
  github-issue-num.txt
  status.txt          # submitted | in-review | improved | ready-for-design
```

The canonical working directory for pipeline artifacts is `~/workspace/idea-factory/`. This is also the git repo — ideas are committed and pushed automatically on submit.

## GitHub Integration

Issues are created in `jakebuild/idea-factory`. Critique and improved versions are posted as comments. Labels: `idea`, `ready-for-design`.

## Monitoring Agents

```bash
tmux list-sessions                    # see running agents
tmux attach -t idea-critique-<n>      # watch critique agent
tmux attach -t idea-improve-<n>       # watch improve agent
```

## Commands Reference

| Command | Description |
|---------|-------------|
| `idea-factory submit <idea>` | Submit + auto-run 2-round pipeline |
| `idea-factory critique <n>` | Run critique agent for idea #n |
| `idea-factory improve <n>` | Run improve agent for idea #n |
| `idea-factory refine <n> [rounds]` | Manual multi-round refine |
| `idea-factory validate <n>` | Mark ready for design |
| `idea-factory list` | Show all ideas and status |

## Critique Format

Each critique follows this structure:
- **Verdict:** WORTH BUILDING / NEEDS MAJOR WORK / KILL IT
- **Score:** X/10
- What's Actually Good
- Brutal Feedback
- Key Questions
- Suggestions
- Solo Dev Reality Check (can one person ship in 2–4 weeks with AI tools?)
- Design Handoff (screens needed, key interactions, data per screen) — only if WORTH BUILDING

## Context for AI

- **Builder:** solo developer, vibe coding with AI (fast iteration)
- **Scope:** simple apps shippable in 2–4 weeks with AI coding tools
- **Goal:** get from raw idea → validated spec with design handoff
- Ideas that require a team, massive infra, or months of solo grind should be flagged in critique
