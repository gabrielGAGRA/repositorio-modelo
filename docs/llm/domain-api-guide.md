<!-- TEMPLATE: Inject-ready guide. Replace every FILL block with target-repo facts; delete this banner when done. -->

# AI Guide: Domain Invariants, API Contract, and External Integration

---

## 1. Request Context & Routing

<!-- FILL: Primary HTTP endpoints (method + path), health check if any, and router tags. Discover: app/presentation/routers/, OpenAPI, tests/presentation/. Keep: bullet list per endpoint with brief purpose. -->

- **Identity & Context in Payloads**:
<!-- FILL: Required and optional JSON/body fields for tenant/user/session/context (field names, precedence, defaults). Discover: presentation schemas, router handlers, tests. Keep: bullet list with field name, source (body/header), and default behavior. -->

- **Downstream / External API Headers (if applicable)**:
<!-- FILL: Headers forwarded to external services; secret loading (env, Secrets Manager, etc.). Discover: infrastructure adapters, app/config.py, pipeline config. Keep: bullet list; omit section if no external API. -->

---

## 2. Domain Validation Invariants

<!-- FILL: Describe validate-first rules before side effects (AST checks, schema rules, ban lists). Discover: app/domain/services/, validators, tests/domain/. Keep: subsection per validator with banned constructs/modules and violation handling. Omit entire section if no pre-execution validation. -->

### Violation Handling

<!-- FILL: What happens on validation failure (abort, soft-fail result type, message shape). Discover: use cases and formatters. Keep: bullet list. -->

---

## 3. External I/O & Path Rules

<!-- FILL: File/catalog path resolution, upload/download naming, sanitization, metadata. Discover: domain services (catalog_paths, normalizers), storage adapters, tests. Keep: resolution table or bullets; omit section if no file I/O. -->

---

## 4. Execution / Isolation

<!-- FILL: Sandbox, subprocess, or remote execution constraints (env allowlist, timeout, prelude injection). Discover: infrastructure runners, sandbox_constants, tests/infrastructure/. Keep: bullet list per constraint. Omit section if no isolated execution. -->

---

## 5. Execution Result & Message Formatting

<!-- FILL: Soft-fail vs exception strategy; result model fields; AI/client message structure (tags, markers, instructions). Discover: use case return types, presentation/formatters/, tests/presentation/. Keep: bullet list of message sections and ordering. -->
