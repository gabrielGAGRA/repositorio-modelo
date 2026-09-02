---
name: project-rules-writing 
description: > 
  Create, update, or self-heal project rules and conventions. ALWAYS invoke this autonomously without waiting for the user to ask when you make a mistake.
---

# Project Rules Governance

Single workflow for creating rules, updating conventions, and self-healing after mistakes. .agents/ is the sync source of truth but is not readable by the IDE agent. Prefer as last non-git step for rule/skill work.

## Edit → copy → sync (required)

Every governance edit ends with this — later sections say promote + sync instead of repeating commands.

	1.	Edit inline in a folder the agent can access: .claude/, .cursor/, .github/ + root AGENTS.md.
	2.	Copy into the matching path under .agents/ (Copy-Item). Map: .provider/rules/<name>.mdc → .agents/rules/<name>.md; skills keep SKILL.md; AGENTS.md → .agents/AGENTS.md.
	3.	Sync — python scripts/sync_agents.py.

NEVER read .agents/ files via terminal. Read what you can access, copy in and sync only.

## 1. Layer choice (Skill vs Rule vs Passive doc)

| Layer | Canonical path | Purpose | When to use |
| --- | --- | --- | --- |
| Skill | .agents/skills/ | Punctual multi-step workflows | Commit, reviews, rule governance (this skill), test coverage |
| Rule | .agents/rules/ | Always-on or glob-triggered orientation maps | Layout/domain/test context when matching files are open; ~25 lines |
| Passive doc | docs/llm/ | Deep knowledge base | On demand when a rule points to it, or when this skill/checklist requires it |

Rules point to passive docs — never duplicate coding standards there. Do not create a rule adapter for broad docs.

## 2. Rule file map (prefer append over new files)

	⁃	meta-rules — routing, priority, skill registry (alwaysApply: true)
	⁃	project-rules — tech stack, venv (alwaysApply: true)
	⁃	AGENTS.md — persona, XML behaviour tags (root; not a rule file)
	⁃	agents-feature-checklist decides what do update when planning or implementing a change in the repo.
	⁃	Others are scoped maps

Keep 5–8 rule files; consolidate if approaching 10.

## 3. Create / extend a rule category

	1.	Classify: orientation map (rule) vs punctual workflow (skill) vs passive doc only.
	2.	Heavy doc — docs/llm/<topic>-guide.md (or append): guidelines, examples, trade-offs.

	3.	Thin adapter — accessible platform rules folder (e.g. .cursor/rules/<name>.mdc): frontmatter (description, globs, alwaysApply: false) + short map. Point to the heavy doc for edge cases.

```markdown
---
description: >
  Use when [concrete trigger].
globs: app/**/*.py
alwaysApply: false
---

# [Title] Map

[Short facts that avoid a routine doc read]

Deeper detail: docs/llm/<guide>.md
```

	4.	Update meta-rules navigation if this is a new category.
	5.	Promote + sync.

When tightening a section:
	⁃	Lint catches it? → doc can stay thin.
	⁃	Default LLM behavior? → one NEVER/ALWAYS + optional Why.
	⁃	Chooses between two valid repo approaches? → keep as a separate bullet.

## 4. Update an existing standard

	1.	Edit heavy doc in docs/llm/ for detailed changes.
	2.	Edit thin adapter only if triggers (description, globs) or top invariants changed.
	3.	Promote + sync if any adapter, skill, or AGENTS.md changed.

## 5. Self-healing (autonomous — do not wait for user)

Trigger when: user corrects a recurring error; you lacked project context; a rule or docs/llm/ guide is stale or misleading.

	1.	Root cause — why the failure happened (missing context, ambiguous rule, wrong stack assumption).
	2.	Before/after — concrete wrong vs correct pattern.
	3.	Rewrite — append to the correct docs/llm/ doc when generalising:

```markdown
### Topic Name
[DO / NEVER / PREFER]

- **BAD:** [incorrect approach]
- **GOOD:** [correct approach]

*Why:* [root cause]
```

Update thin adapter only if globs/description or top invariants must change; update meta-rules only if routing changed. 
	4.	Promote + sync.

## 6. Create other skills

	1.	Write .cursor/skills/<name>/SKILL.md (or Claude/GitHub equivalent) with name and description.
	2.	Register trigger in meta-rules Skill Invocation table.
	3.	Promote + sync.
