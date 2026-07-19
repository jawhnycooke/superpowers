---
name: orchestrating-workflows
description: Use when a task fans out across many similar items (5+ files, findings, sources), runs in stages (find then verify then synthesize), has unknown size (keep going until nothing new), or requires grading your own prior work - decides between inline subagent dispatch and a scripted dynamic workflow
---

# Orchestrating Workflows

## Overview

A dynamic workflow is a script your harness executes that orchestrates subagents at scale (in Claude Code: the Workflow tool). The script holds the loop, the branching, and the intermediate results — your context holds only the final answer, and the orchestration itself becomes a reviewable, rerunnable artifact.

**Core principle:** Move orchestration into a script when the task is too big for one context, too parallel to drive turn-by-turn, or too prone to self-grading. Prose discipline enforced by willpower becomes control flow that cannot be rationalized away.

If your harness has no workflow tool, use superpowers:dispatching-parallel-agents for everything below.

## Inline Subagents vs. Workflow

| Signal | Choose |
|--------|--------|
| 2-4 known, independent tasks | Inline dispatch — superpowers:dispatching-parallel-agents |
| 5+ similar items (files, routes, findings, sources) | Workflow |
| Staged fan-out: find → verify → synthesize | Workflow (pipeline, not barriers) |
| Unknown scope — search until nothing new turns up | Workflow (loop-until-dry) |
| Reviewing or verifying work you produced | Workflow — independent agents resist self-preferential bias |
| Needs user input mid-run | Inline — workflows cannot pause for questions |
| Interactive skills (brainstorming, finishing-a-development-branch) | Inline, always |

## Quality Patterns

- **Fan-out-and-synthesize** — one agent per item, one agent to merge and rank
- **Adversarial verification** — independent skeptics try to REFUTE each finding before it's reported; kill findings a majority refute
- **Generate-and-filter** — produce candidates broadly, quality-filter survivors
- **Judge panel** — draft N independent approaches, score, synthesize from the winner
- **Loop-until-dry** — keep spawning finders until K consecutive rounds find nothing new
- **Classify-and-act** — route items to specialist prompts by classification

## Mechanics That Replace Prose Discipline

- **Structured output schemas** — statuses and findings return as validated objects, not prose to parse
- **Worktree isolation per agent** — parallel file edits stop conflicting; each agent works an isolated copy
- **Model/effort per stage** — cheap models for mechanical stages, strongest for judging
- **Resume** — a stopped run resumes with completed agents cached
- **Save proven runs** — a workflow that worked becomes a reusable command (`.claude/workflows/` in Claude Code)

## Red Flags

**Never:**
- Drive a 10-item fan-out by hand, turn by turn — that is a workflow written in willpower
- Let the run's return value substitute for verification — apply superpowers:verification-before-completion to workflow output like any agent report (check the diff, not the summary)
- Convert interactive skills to workflows — no mid-run user input exists
- Fan out before scoping — discover the work-list inline first, then script the fan-out

## Integration

- **superpowers:dispatching-parallel-agents** — the inline path for small, known fan-outs
- **superpowers:writing-skills** — automated skill pressure-testing runs as a workflow
- **superpowers:verification-before-completion** — applies to workflow results unchanged
