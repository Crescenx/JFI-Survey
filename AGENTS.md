# Agent Collaboration Guide

This project uses agents as a collaboration and memory layer for a paper. Agents
may read the whole repository, but should write only inside `_agents/` unless a
human explicitly asks for manuscript or bibliography edits.

## Project Layout

Main manuscript files:

- `main.tex`
- `ref.bib`
- `sections/`

Review and markup helpers:

- `review.tex`

Agent-facing workspace:

- `_agents/`

## `_agents/` Structure

The root of `_agents/` should stay small:

```text
_agents/
  state.md
  focus.md

  changelog/
    YYYY-MM-DD-topic.md
    auto/
      YYYY-MM-DD-git-<commit>.md

  materials/
    descriptive-name.md

  decisions/
    open.md
    resolved.md
    items/
      YYYY-MM-DD-topic.md
```

## File Roles

### `_agents/state.md`

`state.md` records the current factual state of the paper. It should answer:
"What is the project state right now?"

Keep it concise. Do not store discussion history here.

Recommended sections:

```md
# State

Last reviewed commit:
Last updated:

## Manuscript Structure
- `sections/intro.tex`: ...
- `sections/recap.tex`: ...
- `sections/ai4gt.tex`: ...
- `sections/gt4ai.tex`: ...

## Current Direction
...

## Stable Decisions
- ...
```

### `_agents/focus.md`

`focus.md` tells a collaborator what to pay attention to when they open the
project. It should answer: "What should I look at first?"

Recommended sections:

```md
# Focus

## Immediate Attention
1. ...
2. ...

## Read First
- ...

## Risks
- ...

## Needed Feedback
- ...
```

### `_agents/changelog/`

The changelog is the detailed collaboration history. Each major human or agent
work session should get its own file.

Standard changelog template:

```md
---
date:
author:
source: human / agent / git-auto
base_commit:
head_commit:
scope:
status:
---

# Change: ...

## 1. What Changed
...

## 2. Future Plan / Attention
...

## 3. Advice Needed
...
```

If a collaborator changes the paper directly, for example in Overleaf, and does
not write a changelog entry, agents may generate a fallback entry from git
history and place it under `_agents/changelog/auto/`. Mark such entries as
`source: git-auto` and treat them as lower-context summaries.

### `_agents/materials/`

`materials/` stores collaboration materials produced or received during the
paper-writing process, such as review memos, author responses, planning notes,
and source documents that inform later changelog, state, focus, or manuscript
updates. Keep filenames descriptive and prefer linking to these files from
`state.md`, `focus.md`, changelog entries, or decisions instead of copying long
passages.

### `_agents/decisions/`

`decisions/` manages suggestions, questions, and decisions. Entries may come
from humans or agents.

Use one file per item:

```md
---
date:
source: human / agent
type: suggestion / question / decision
status: open / accepted / rejected / superseded
scope:
related_files:
---

# ...

## Context
...

## Proposal or Question
...

## Rationale
...

## Needed Response
...
```

`_agents/decisions/open.md` is an index of unresolved items. Keep it short.

```md
# Open Decisions / Questions

| Item | Type | Source | Status | Need |
|---|---|---|---|---|
| `items/YYYY-MM-DD-topic.md` | question | human | open | feedback |
```

`_agents/decisions/resolved.md` records only accepted decisions, not the full
discussion process.

```md
# Resolved Decisions

## Section 2
Decision: ...
Source: `items/YYYY-MM-DD-topic.md`
```

## Fixed Agent Workflow

When asked to summarize progress or update agent-facing context, follow this
three-step workflow.

### 1. Generate changelog

- Read `_agents/state.md` for `Last reviewed commit`.
- Compare that commit with the current git state.
- Read relevant manuscript files.
- If there is human-written context, create a normal changelog entry.
- If not, create a fallback entry under `_agents/changelog/auto/` from git
  log/diff and mark it as `source: git-auto`.

### 2. Update state

- Read the latest changelog entries, current manuscript files, and resolved
  decisions.
- Update `_agents/state.md` with the current factual state.
- Keep it concise and avoid discussion history.

### 3. Generate focus

- Read `_agents/state.md`, open decisions, and the latest changelog entries.
- Update `_agents/focus.md` with what collaborators should inspect next.
- Keep it actionable and short.

## Operating Rules

- Agents may read all repository files.
- Agents should write only inside `_agents/` unless a human explicitly requests
  edits elsewhere.
- Manuscript files remain the source of truth.
- `_agents/changelog/` records history.
- `_agents/materials/` stores collaboration materials and source documents.
- `_agents/state.md` records the current state.
- `_agents/focus.md` guides the next collaborator.
- `_agents/decisions/` manages suggestions, questions, and accepted decisions.
- Prefer references to file paths and commits over copying long manuscript
  passages.
