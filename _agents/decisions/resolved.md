# Resolved Decisions

## Agent Workspace

Decision: Agent-facing collaboration memory lives under `_agents/`.

Source: Human discussion on 2026-05-31.

## Agent Write Policy

Decision: Agents may read all repository files, but should write only under
`_agents/` unless explicitly asked to edit manuscript files.

Source: Human discussion on 2026-05-31.

## Agent Context Structure

Decision: `_agents/state.md` records current factual state;
`_agents/focus.md` records what collaborators should inspect next;
`_agents/changelog/` records detailed progress; `_agents/decisions/` manages
suggestions, questions, and accepted decisions.

Source: Human discussion on 2026-05-31.

## Conclusion Inclusion

Decision: `sections/conclusion.tex` should be included by `main.tex`.

Source: Human instruction on 2026-05-31.

## Legacy Agent Folder

Decision: Remove the legacy `agents/` directory instead of migrating it. The
maintainer has already backed up the old contents.

Source: Human instruction on 2026-05-31.
