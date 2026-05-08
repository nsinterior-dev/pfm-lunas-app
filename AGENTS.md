# Multi-Agent Orchestration — Lunas

## Overview

Lunas uses a multi-agent pipeline for issue triage, investigation, planning, implementation, and review. The **lead agent** orchestrates everything — you invoke it, it dispatches the right agents based on risk scoring.

## Agents

| Agent | Role | Tools | Edits Files? |
|-------|------|-------|-------------|
| **lead** | UCE triage, routing, parallel execution via worktrees | Read, Grep, Glob, Bash, Agent, GitHub MCP | No |
| **intake-router** | Classifies issues → bugfix/feature/improvement/chore, applies labels | GitHub MCP, Read | No |
| **investigator** | Read-only codebase analysis, Gemini CLI for broad scans, outputs Impact Report | Read, Grep, Glob, Bash (gemini only) | No |
| **planner** | Scope-tiered plans (XS/S/M/L) with In Scope / Out of Scope / Risk-if-Expanded | Read, Grep, Glob | No |
| **developer** | Implements approved plans, scope constraint is non-negotiable | Read, Edit, Write, Bash, Grep, Glob | Yes |
| **code-reviewer** | Runs checklist, REQUEST CHANGES on Blocker/Major, APPROVE otherwise | Read, Grep, Glob, Bash (git/lint only) | No |
| **debugger** | Bug investigation, root-cause hypothesis, translation layer awareness | Read, Grep, Glob, Bash (test/log only) | No |

## UCE Triage Model

The lead agent scores every issue on three axes (1-3 each):

| Axis | 1 (Low) | 2 (Medium) | 3 (High) |
|------|---------|------------|----------|
| **Uncertainty** | Clear repro, known area | Vague but identifiable | No repro, unclear behavior |
| **Complexity** | 1 file, 1 layer | Cross-layer, 3-5 files | Cross-feature, translation layer |
| **Effort** | < 20 lines | 20-100 lines | 100+ lines or new patterns |

### Routing by UCE Sum

| UCE | Route | Human Gate |
|-----|-------|------------|
| 3-4 | Full pipeline, autonomous (worktree-isolated) | Merge PR only |
| 5-6 | Investigation + plan, pause for approval | Approve plan + merge |
| 7-9 | Investigation + Impact Report, stop | You drive from planner onward |

## Pipeline Flows

### Feature / Improvement (standard lane)
```
Router → Investigator → Planner → [you approve] → Developer → Code-Reviewer → [you merge]
```

### Bugfix
```
Router → Investigator → Debugger → Planner → [you approve] → Developer → Code-Reviewer → [you merge]
```

### Chore (fast lane)
```
Router → Developer → Code-Reviewer → [you merge]
```
Skips investigator and planner. If the developer discovers feature-level changes are needed, it escalates to the full pipeline.

### High UCE (you drive)
```
Router → Investigator → [full Impact Report handed to you] → you drive from here
```

## Design Agent (Planned)

A future agent that sits between planner and developer:
```
Planner → Design Agent (Storybook mockups) → [you approve designs] → Developer
```

Uses `/ui-designer`, `/ux-designer`, and `/ui-ux-researcher` skills. Produces component variants in `client/stories/`. Decisions recorded in `designs/decisions/`.

## Branch & PR Rules

| Context | Action |
|---------|--------|
| On `development` or `main` | Commit directly after local code review |
| On feature/fix branch | PR to `development`, human merges |
| Release | PR from `development` to `main`, human only |

## CI Review

GitHub Actions runs a Gemini 2.5 Flash review on every PR to `development` or `main`. Posts findings as a PR comment with severity tiers. See `.github/workflows/agent-review.yml`.

## Skills (Referenced by Agents)

| Skill | Used By | Path |
|-------|---------|------|
| `/frontend` | developer, planner | `.claude/skills/frontend/SKILL.md` |
| `/backend` | developer, planner | `.claude/skills/backend/SKILL.md` |
| `/deployment` | developer | `.claude/skills/deployment/SKILL.md` |
| `/code-review` | code-reviewer | `.claude/skills/code-review/SKILL.md` |
| `/ui-designer` | design agent (future) | `.claude/skills/ui-designer/SKILL.md` |
| `/ux-designer` | design agent (future) | `.claude/skills/ux-designer/SKILL.md` |
| `/ui-ux-researcher` | design agent (future) | `.claude/skills/ui-ux-researcher/SKILL.md` |

## Observability

Hooks in `.claude/settings.json` forward tool-use and agent lifecycle events to a local server. Set `LUNAS_OBSERVABILITY_URL` env var (defaults to `http://localhost:3001`). Server ships in a separate PR.

## Quick Start

```bash
# Triage a batch of issues
claude --agent lead -p "Review open issues and triage them."

# Classify a single issue
claude --agent intake-router -p "Classify issue #12."

# Investigate a task
claude --agent investigator -p "Investigate LUN-011: Data source toggle UI."

# Plan implementation
claude --agent planner -p "Plan LUN-011 based on the Impact Report."

# Implement (after plan approval)
claude --agent developer -p "Execute the approved plan for LUN-011."

# Review changes
claude --agent code-reviewer -p "Review the changes for LUN-011."

# Debug a bug
claude --agent debugger -p "Investigate bug #15: sheet mapping fails on multi-section budget."
```

## Notes

- **Template research** — Before LUN-009 (Create New Sheet), research curated budget/debt/savings templates (YNAB, PH-specific from r/phinvest, GCash/Maya communities). Parked for pre-Sprint 2.
- **Sheet-first flow** — Users must connect or create a Google Sheet before using the app. No Firestore-based financial data. Firestore is glue only.
