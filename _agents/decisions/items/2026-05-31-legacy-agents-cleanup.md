---
date: 2026-05-31
source: agent
type: question
status: accepted
scope: repository-organization
related_files: agents/, _agents/
---

# What Should Happen to the Legacy `agents/` Folder?

## Context

The intended agent workspace is now `_agents/`. The working tree currently shows
deleted files under `agents/outlines/`.

## Proposal or Question

Should the legacy `agents/` directory be removed, migrated into `_agents/`, or
left alone?

## Rationale

Having both `agents/` and `_agents/` may confuse future collaborators and agents.

## Needed Response

Resolved: remove the legacy `agents/` directory. Do not migrate the contents
because the maintainer has already backed them up.
