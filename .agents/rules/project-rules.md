---
description: >
  Workspace facts. Use for tech stack, hexagonal layout overview,
  primary codebase path, virtualenv location, and deferred work (TODO.md).
alwaysApply: true
---

<!-- TEMPLATE: Inject-ready rule. Replace every FILL block with target-repo facts; delete this banner when done. -->

# Project Core

## Repository & Tech Stack

- **Primary Codebase**: <!-- FILL: Root package path (e.g. app/). Discover: repo tree, pyproject.toml. Keep: one path or short phrase. -->
- **Architecture**: <!-- FILL: One-line style (e.g. hexagonal / layered FastAPI). Discover: docs, app/ layout. Keep: short label. -->
- **Virtualenv**: `.venv` at the repository root.
- **Deploy**: <!-- FILL: Deploy target and entry (e.g. Lambda + pipeline/aws). Discover: pipeline/, README, Dockerfile. Keep: one line. -->


# Deferred work (TODO.md)

When you or the user defer something for later, record it in `TODO.md` at the repository root so the team can track it.

- Add a bullet with a short description and enough context to resume work (file, area, or reason deferred).
- Update `TODO.md` in the same turn you agree to defer.
- If `TODO.md` does not exist yet, create it with a short heading and the first item.