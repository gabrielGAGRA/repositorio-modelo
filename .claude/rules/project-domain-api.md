---
description: Use when changing tenant/auth headers, catalog isolation, storage backends, File.url, Graph token handling, or create_file/create_folder parent path rules.
paths:
  - "app/domain/**/*.py"
  - "app/application/use_cases/**/*.py"
  - "app/presentation/**/*.py"
---

<!-- TEMPLATE: Inject-ready rule. Replace every FILL block with target-repo facts; delete this banner when done. -->

# Domain Map

**Identity**: <!-- FILL: Context fields (user, session, tenant) and where they come from (body, headers). Discover: schemas, routers, adapters. Keep: one sentence. -->

**Validate-first**: <!-- FILL: Pre-execution validation rule and what it blocks. Discover: domain validators, tests/domain/. Keep: one sentence; omit if N/A. -->

<!-- FILL: External I/O bullets (path resolution, upload naming, metadata). Discover: catalog_paths, storage adapters. Keep: short bullets; omit section if N/A. -->

**Soft-fail**: <!-- FILL: Use case result type and formatter for client/AI message. Discover: use cases, formatters/. Keep: one sentence with module paths. -->

**Router**: <!-- FILL: Tags, health route, logging events, async/threading pattern. Discover: routers/. Keep: one sentence. -->

<!-- FILL: Env isolation or execution constraint. Discover: sandbox/runner modules. Keep: one bold label + sentence; omit if N/A. -->

<!-- FILL: Optional domain-specific prelude or timeout rules. Discover: use case input, runner. Keep: short bullets; omit if N/A. -->

More edge cases: `docs/llm/domain-api-guide.md`.