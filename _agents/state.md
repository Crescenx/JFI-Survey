# State

Base commit before `_agents` initialization: 08910f3
Last updated: 2026-05-31

## Manuscript Structure

- `main.tex`: currently inputs `sections/intro`, `sections/recap`,
  `sections/ai4gt`, `sections/gt4ai`, and `sections/conclusion`.
- `sections/intro.tex`: introduction.
- `sections/recap.tex`: Section 2 recap of game theory and deep learning.
- `sections/ai4gt.tex`: AI for game theory.
- `sections/gt4ai.tex`: game theory for strategic AI.
- `sections/conclusion.tex`: synthesis/outlook section, now included by
  `main.tex`.
- `ref.bib`: bibliography file used by `main.tex`.

## Current Direction

The paper is organized as a survey of the interaction between game theory and AI:
introductory framing, a recap section for the two fields, AI methods for game
theoretic objects, and game-theoretic tools for strategic AI.

## Stable Decisions

- Agent-facing context lives under `_agents/`.
- Agents may read the full repository, but should write only under `_agents/`
  unless explicitly asked to edit manuscript files.
- `_agents/changelog/` records detailed history.
- `_agents/state.md` records current factual state.
- `_agents/focus.md` tells collaborators what to inspect next.
- `_agents/decisions/` manages suggestions, questions, and accepted decisions.
- `sections/conclusion.tex` should be part of the current manuscript.
- The legacy `agents/` directory should be removed rather than migrated; the
  user has already backed it up.

## Working Tree Notes

- This initialization introduces `AGENTS.md`, `_agents/`, and
  `sections/conclusion.tex`.
- Legacy files under `agents/outlines/` should remain deleted.
