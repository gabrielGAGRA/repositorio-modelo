---
name: catchup
description: Manual-only. Re-ground a resumed task from repository evidence when the user invokes @catchup.
---

# Catchup

Manual invocation only. Do not run automatically at session start or when context is uncertain.

## Goal

Recover an accurate, compact task state from the repository. Do not rely on chat history or an unverified task/plan artifact.

## Workflow

1. Read the task request and inspect the current branch, status, relevant diff, and recent commits.
2. If an active task or plan exists for this work in the current environment, reconcile it against repository evidence. Never treat chat history or an unverified task/plan as ground truth.
3. Inspect only the touched source, tests, contracts, and prior verification output needed to establish the current state.
4. Report: goal; changed scope; decisions/invariants; verification performed; blockers/risks; and one next verifiable action.
5. Ask the user one focused question if a missing decision blocks progress.

## Constraints

- Read-only. Never edits code or governance artifacts.
- Do not summarize the full conversation or manufacture decisions.
- Invoke `@interview-plan` if the recovered state exposes an unresolved relevant decision.
- If catchup surfaces a recurring mistake or stale governance assumption, invoke `@project-rules-writing` self-healing instead of logging ad-hoc session notes.