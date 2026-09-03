---
description: Use when writing or editing tests - test tree layout, fakes/mocks, pytest markers, and how to run unit vs integration suites.
globs: tests/**/*.py
alwaysApply: false
---

<!-- TEMPLATE: Inject-ready rule. Replace every FILL block with target-repo facts; delete this banner when done. -->

# Tests

| Directory | Scope |
| --- | --- |
<!-- FILL: One row per tests/ subdirectory. Discover: tests/ tree. Keep: directory path + what is tested there. -->

Markers: `@pytest.mark.unit` (default fast tests), `@pytest.mark.integration` (live infra — rare).

<!-- FILL: Doubles guidance — which fakes/mocks for use-case, HTTP, and adapter tests. Discover: tests/utils/, conftest. Keep: one or two sentences. -->

Categories + isolation: `docs/llm/tests-guide.md` §5. TDD + CI policy: `docs/llm/software-engineering-rules.md` §9.