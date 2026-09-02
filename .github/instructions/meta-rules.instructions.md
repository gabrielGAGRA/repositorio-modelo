---
description: Use for rule priority, where to find docs, and when to invoke skills (planning, review, commit, and rule governance).
applyTo: **
---
<!-- TEMPLATE: Inject-ready rule. Replace every FILL block with target-repo facts; delete this banner when done. -->
# Meta rules
## Priority (highest -> lowest)
\t1.\tAGENTS.md - persona and behavioral constraints
\t2.\tproject-rules - repo scope, tech stack
\t3.\tagents-feature-checklist - change order (before, during, and after implementation)
\t4.\tproject-architecture / project-domain-api - layout and sandbox explanations and pointers
\t5.\tproject-tests - test layout and pointers
\t6.\tdocs/llm/software-engineering-rules.md - coding standards (passive; read only when implementing)
## Skill Invocation (single registry)
| Trigger / Intent | Action |
| --- | --- |
| Commit, branch, or any git write | @commit |
| Create, edit, or self-heal agent rules / conventions | @project-rules-writing |
| User asks for a PR code review | @PR-code-review |
| User explicitly invokes catchup | @catchup |
| Ambiguous or relevant behavior change, before planning | @interview-plan |
| Any diff before delivery or git write | @adversarial-review |
## Navigation - when to read what
| Question | Read |
| --- | --- |
| Persona, communication style | AGENTS.md |
| Tech stack, venv | project-rules |
| Before an ambiguous or relevant change (interview + plan) | @interview-plan + agents-feature-checklist |
| After a code or contract change (tests, docs, quality gate) | agents-feature-checklist |
| Validating any diff before delivery | @adversarial-review |
<!-- FILL: Navigation row — HTTP contract, validation, external I/O, AI message format. Discover: project-domain-api rule + routers/schemas. Keep: project-domain-api + docs/llm/domain-api-guide.md. -->
<!-- FILL: Navigation row — layer map / package layout. Discover: app/ tree. Keep: project-architecture + docs/llm/architecture-guide.md. -->
<!-- FILL: Navigation row — env, secrets, deploy runtime config. Discover: app/config.py, pipeline/, infra yaml. Keep: list concrete config paths. -->
| Python patterns, imports, DIP | docs/llm/software-engineering-rules.md |
<!-- FILL: Navigation row — test tree and pytest markers. Discover: tests/, pytest.ini. Keep: project-tests + docs/llm/tests-guide.md + tests/pytest.ini. -->
| Rule create / update / self-healing | @project-rules-writing skill |