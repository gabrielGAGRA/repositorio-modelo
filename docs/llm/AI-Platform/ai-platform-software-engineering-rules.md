# Python Engineering Rules

Repo-agnostic Python standards. Repo-specific layout, wiring, and contracts live in the architecture and domain guides.

---

## Editing this Doc

Use this document for decisions a linter/formatter cannot enforce reliably. Automate what is objective and stable; document what needs judgment, rationale, or an exception process.

### Lint vs this doc
- **Mechanical**: `pyproject.toml` is authoritative for mechanical rules: formatting and naming, imports, banned APIs, layer imports.
- **Lint on + one judgment line**: recurring legitimate exceptions (for example: broad except at isolation points that re-raise or `logging.exception` — allowed). Scoped per-layer ignores live in `per-file-ignores`.
- **Ignored in pyproject + judgment here**: ternaries; per-symbol docstring headers; when a function/class docstring is worth writing.
- **This doc only**: architecture, auth transport, data-modeling choice, wiring, comment necessity, error-message content.

*Why*: add a brief *Why* only when a rule deliberately counters a common model/tutorial default. A plain directive is enough for ordinary practice.

### Modal verbs
- **NEVER / ALWAYS** — hard constraints; violations need an explicit, documented exception.
- **Prefer / Avoid** — defaults when both options work; follow unless local style or a NEVER/ALWAYS instruction contradicts the design.

---

## 1. Imports and Packages

- **Absolute imports**: Default for project code.
- **Sibling relative imports**: Acceptable when absolute paths are unnecessarily verbose.
- **Local packages**: Prefer imports that make origin and meaning clear at the call site.
- **Module import** when it disambiguates a common name or exposes a deliberate namespace (`import package.models.user` then `user.User(...)`).
- **Direct symbol import** for stable, unambiguous types and public interfaces (`from package.models.user import User`).
- Avoid wildcard imports outside tightly controlled package re-exports.
- **Stdlib / third-party**: Symbol imports are fine (`from pydantic import BaseModel`).
- **No import-time side effects**: NEVER load configuration, create network/database clients, or perform environment-dependent registration at import time. Defer to the composition root.

---

## 2. Exceptions & Resource Management

- **Built-ins**: Raise `ValueError` (etc.) for bad arguments / preconditions.
- **Custom**: Subclass `CustomError` in `app/domain/exceptions.py` only — no parallel hierarchies. It carries `message`, `name`, and `details` (no HTTP status — domain must not know transport semantics).
- **Transport mapping**: Map domain exceptions to transport status and response shape only at the presentation/adapter boundary, via type-based lookup.
- **Translation layers**:
  - *Expected business failures* — normal, typed outcomes or domain/application exceptions.
  - *Expected infrastructure failures* — map to application-defined transient/permanent failures the use case can act on.
  - *Unexpected defects* — log with full context at the system/request boundary; propagate or map at the outer boundary.
- **Try scope**: Keep `try` bodies as small as possible.
- **`except Exception`**: Catch the provider/client hierarchy where possible; do not wrap every method in a broad catch. Use broad catches only at genuine isolation boundaries (an adapter translating driver errors, a rollback boundary, an outer gateway). ALWAYS re-raise or map to a domain error; never swallow. Chain the cause: `raise DomainError(...) from exc`. Do not disguise programming errors as operational failures.
- **Error messages**: Name the failed condition and relevant values; use exception types/codes for automation; NEVER include secrets.
- **Public disclosure**: Internal exceptions may carry diagnostic context; NEVER expose that context, secrets, or provider payloads in public API errors.
- **Resource ownership**: Owner closes sockets, DB connections, mmap, file-likes. Prefer context managers; `contextlib.closing()` when there is `close()` but no CM; async CMs for async. Do not close borrowed resources. Generators that own a resource: `try`/`finally` around `yield` (or iterate via a CM).

---

## 3. Language Features

### Symbol placement (variables / functions / classes)

Apply before inventing a new module or leaving a “sparse” constant beside unrelated code.

- **Variables / constants**
  - Env-tunable or deploy-overridable → the configuration/settings model. Parse derived values (comma lists, etc.) in the composition root, not on the settings class.
  - Used only inside one class or function → private on that owner (`_name` or a nested helper).
  - Used in more than one place → keep with the natural owner (enum member/set, domain rule module, contract module). Import it. NEVER create a file whose only job is holding unrelated constants.
  - Module-public only when another module or tests must read it; otherwise `_name`.
  - NEVER put domain invariants, transport/contract names, or persistence sentinels on the settings model — those are not env knobs.
- **Functions**
  - Used only by one class → instance / class / static method on that class.
  - Used only by one function → nest inside it (no module-level `_helper` for a single caller).
  - Used in more than one place → a helpers module or a domain policy module (for policies with no natural entity owner).
  - Module-level beside classes only when strongly justified (a composition hook or framework registry).
- **Classes**
  - Multiple classes in one file are fine.
  - Do not mix a pile of free functions with classes in one file unless justified. Put shared validators/normalizers in a helpers module; keep helpers modules functions-only.

- **Mutable globals**: Avoid process-wide mutable state; if needed, `_name` + accessors. Module constants that follow the placement rules above are fine.
- **Nested classes/functions**: Prefer nesting when the helper has a single caller (including helpers that do not close over locals). Prefer module-level `_name` only when shared by multiple call sites or when nesting hurts readability.
- **Module-level functions**: Put behavior that conceptually belongs to a type on that type. Prefer a dedicated helpers module over a pile of `_validate_*` / `_apply_*` helpers beside a use case that operate on domain objects (anemic-domain smell).
- **Comprehensions**: Keep readable at a glance. Use explicit loops or helpers for nested conditions, side effects, or complex expressions; keep comprehensions for selection/mapping. Move business logic into objects or named domain policies.
- **Iteration**: Do not mutate the container while iterating (snapshot or new collection; queue-drain OK).
- **Lambdas**: Short local callbacks OK; prefer `operator` when clearer.
- **Ternaries**: Short, side-effect-free only; no nesting; use `if`/`else` when a branch needs explanation.
- **Truthiness**: Explicit `is None` / `== 0` when those must not conflate with emptiness/falsey checks.
- **Static / class methods**: Prefer an instance method when behavior needs instance state; `@classmethod` for alternate constructors or class-aware behavior; `@staticmethod` when the operation belongs with the type but needs neither `self` nor `cls`. NEVER conceal global mutable state in classmethods; caches must be explicit and bounded by lifetime, invalidation, and isolation. NEVER perform I/O in decorator logic. Non-trivial decorators need documented effects, tests, and valid parameters that do not fail at import.
- **Power features**: Avoid `getattr`/metaclasses/bytecode/dynamic inheritance unless stdlib (`abc`, `enum`, …).
- **Properties**: Do not add getters/setters merely to wrap fields. Expose read-only properties where appropriate; use intention-revealing methods for state changes that enforce business rules.
- **Threads**: Do not assume container ops are atomic — use `Queue` / `Lock`. Use `Condition` only to wait on a shared predicate; otherwise `Event` / `Queue` / `Semaphore`.
- **Async**: NEVER run blocking I/O or subprocess work on the event loop, suppress cancellation, or create unbounded tasks/queues. ALWAYS set outbound timeouts. Bound concurrency and queues. Prefer structured ownership and lifecycle for spawned tasks. Retry only bounded, idempotent, clearly transient operations.
- **Data modeling** — choose by domain role first (entity / value object / DTO), then by validation and serialization needs:
  - **Entity / aggregate** — explicit class when the concept has identity, lifecycle, mutable state, business invariants, or behavior. Keep state private where practical; expose intention-revealing operations instead of arbitrary field mutation. Equality is identity-based unless documented otherwise. A public operation must leave the object valid.
  - **Value object** — immutable, value-based equality; validates itself at construction; may contain behavior. Prefer an explicit immutable class (or a frozen model when construction-time validation is valuable). Do not expose mutable internals.
  - **DTO** — a validation model at transport, configuration, and external-data boundaries. DTOs must not become domain entities merely because they validate input.
  - Use a validation library (e.g. Pydantic) at API/config boundaries and for untrusted or weakly typed payloads. Map DB rows explicitly to domain entities/value objects; validate at the mapper boundary when schema uncertainty warrants it. NEVER make the domain validation-library-shaped solely for transport. Avoid `@dataclass`; prefer a validation model or an explicit class.
  - `NamedTuple` — lightweight immutable record, no behavior. Do not choose it mainly for hashability.
  - attrs — only if you need frozen/slots + validators/converters and refuse a larger dependency (do not add by default).

---

## 4. Logging & Request Context

- **Startup**: Log process/service context only — not a synthetic request id.
- **Structured logs**: Emit structured (JSON) logs through one logging setup and shared event/payload models. NEVER use `basicConfig` or ad-hoc f-string log lines.
- **Request fields**: Include request/correlation/trace ids only when available from request context or incoming headers.
- **Operation events**: Use a consistent per-operation event vocabulary (start / success / failure).
- **Tenant / principal ids**: Log identifiers only when policy allows; never credentials or PII-rich payloads.
- **Exceptions**: Log with full context once at the owning boundary; inner layers emit a brief failure event only — no duplicate stack traces per layer.
- **Payloads**: When logging request bodies, exclude credential headers and token fields.
- **Observability**: Add metrics/traces for latency and error rates when needed; align trace attributes with OpenTelemetry conventions.

---

## 5. Type Annotations

- Parameters: prefer `collections.abc` abstractions (`Sequence`, `Mapping`) over concrete `list`/`dict`.
- **`TYPE_CHECKING`**: Prefer fixing real import cycles (move shared helpers, invert ownership, pass primitives) over `TYPE_CHECKING` or lazy imports. Use `TYPE_CHECKING` only when the symbol is genuinely unnecessary at runtime **and** a cycle cannot be removed cleanly.
- **Runtime-inspected boundaries**: Validation models, framework endpoint signatures, serializers, and ORM relationships inspect annotations — ensure referenced types resolve in the namespace those frameworks use.
- **Quoted annotations**: Use only when runtime must not resolve the type. Do not treat them as a universal circular-import fix; they can defer a failure to framework startup or runtime.
- **Deferred evaluation (Python 3.14+)**: Changes *when* annotations resolve, not whether frameworks need them. Introspection paths remain sensitive to namespace and framework behavior.
- **Annotations are not validation**: At API and integration boundaries, annotations state intended types; they do not validate runtime data. Explicit validators or adapter mappers enforce runtime contracts.
- `Self` for fluent/self-returning APIs; CapWords type aliases; `T` / `UserT` for TypeVars.
- Annotate assignments only when inference is ambiguous, public, or contractual — not every local.
- `tuple` vs `list` by mutability / fixed vs variable length.

---

## 6. Hexagonal Architecture

### SOLID

- **SRP — use cases**: A use case coordinates a single business capability and transaction boundary. It loads domain objects, invokes domain behavior, persists changes, and coordinates ports. Business invariants and state transitions belong in entities, value objects, or explicit domain policies — not in use cases by default.
- **OCP**: Add or modify a use case when the business capability changes; add or replace an adapter when an external mechanism or provider changes. Keep provider-specific branching in infrastructure or composition code, not domain/application logic. Do not introduce abstractions merely because a second implementation is theoretically possible.
- **LSP / ISP**: Honor Protocols; keep ports small.
- **DIP**: Depend on port/repository Protocols — never on infrastructure concretes outside the composition root. ALWAYS make each adapter/repository concrete implement the Protocol it fulfills. Wire concretes only in the composition root.
- **Package ownership**: `domain` owns concepts/invariants; `application` owns ports, commands, DTOs, and orchestration; `infrastructure` owns adapters, facades, and other I/O; `presentation` owns transport. Add `helpers/` folders inside layers as needed. Avoid generic dumping packages; a cross-cutting package is acceptable only for truly neutral primitives (IDs, time, result/error primitives, observability interfaces) with explicit ownership. Cross-cutting I/O helpers belong in infrastructure.
- **Domain**: models, pure logic, exceptions only. Put identity, invariants, and state transitions on entities/value objects (aggregates own navigation and invariants over their own state). Use domain services/policies only when behavior is domain-significant and has no natural entity owner. “Pure” means no concrete infrastructure — the domain may depend on injected abstractions (clock, policy, deterministic ID generator). Domain NEVER imports settings; pass narrow policy values as parameters.
  - **Encapsulation**: Entity state must not be publicly mutable by default. State transitions go through named operations that preserve invariants. Do not expose mutable internal collections — return immutable views/copies or expose domain operations.
  - **Enums**: Keep alias maps and membership helpers inside the enum type; do not leave post-class module dicts or duplicate frozensets across layers.
- **Ports vs repositories**: External I/O Protocols are ports. Reserve `repositories` for entity persistence; blob/file storage is a port.
  - *Why:* The default AI label for any storage is “repository.”

### Composition root

- Each executable entry point owns a composition root (HTTP server, worker/consumer, scheduled job, CLI, migration task, or test fixture/application factory). NEVER force non-HTTP entry points through the HTTP bootstrap; tests use an equivalent bootstrap.
- Prefer one composition class/function that **inlines** session/client/adapter/use-case/router construction — always in the composition root, NEVER in presentation. Avoid a parallel wiring module in the application layer that imports infrastructure concretes.
- ALWAYS inject adapters and clients from the composition root; do not construct them deeper in the graph. Adapter-internal SDK clients from **supplied** config are OK, but configuration, retry policy, credentials, and lifecycle must be explicit and testable. Prefer composition-root construction when sharing pools or sessions.
- Group related infrastructure (sessions, clients) at the same composition-root level.
- **Settings**: Configuration is a dependency, not an ambient global. The settings model is **fields only** — no methods, cached properties, domain imports, or parsing helpers on the class. Load it once at the composition root and never reload ambient settings deeper in the graph.
  - Domain/application receive typed ports and narrow policy values, never a process-global settings object threaded through the graph.
  - Infrastructure may receive a narrow config object or scalars in `__init__`; never reach into globals from deep call paths.
  - Tests construct settings explicitly; prefer injection over patching ambient settings.
  - Ignoring unknown env vars is acceptable when a shared deployment env supplies irrelevant variables, but requires startup validation or tests so misspellings fail visibly. Defaults are only for non-sensitive developer ergonomics; missing secrets, auth endpoints, or auth mode MUST fail startup.
- **Entry module**: logging setup + app construction only.

---

## 7. Presentation

- **Routers/handlers**: Prefer the framework's function-based handlers with dependency injection for use cases and request context. Class-based handlers are acceptable in existing modules — mirror the local style when extending them.
- **Composition mounts routers**: The composition root mounts already-built routers — no separate presentation-layer aggregator, no infrastructure imports in presentation.
- **Transport contract constants** (header names, auth role strings, correlation/trace header lists): keep in presentation. Do not move them to settings.

### Auth transport

- Resolve auth, tenant, and delegated credentials with framework dependency injection — not ad-hoc request-context objects threaded through application code.
- Credential this service validates → `Authorization: Bearer <token>` when Bearer semantics apply.
- NEVER put secrets in URLs or query strings.
- Delegated/pass-through credentials follow the downstream protocol’s required transport; NEVER persist or log token values. Require explicit delegation authorization. Prefer short-lived scoped tokens or token exchange over forwarding long-lived secrets.
- State the service’s role when relevant: token relay, delegated-credential broker, or OAuth client — those are materially different responsibilities.
  - *Why:* Bearer is for auth we validate; downstream credentials follow their own protocol and carry higher leakage risk.

---

## 8. Application

- **Names**: One public `execute()` per use case; descriptive module names; shared helpers may be unsuffixed. Keep helpers modules functions-only; use-case base classes live with the use cases.
- Routers call use cases directly; NEVER orchestrate in presentation.
- NEVER import infrastructure concretes from the application layer (wiring belongs in the composition root). A port name may say “router” for strategy selection without implying HTTP.
- **Module helpers**: `_name` for module-internal naming; single leading `_` only (`__name` is class name-mangling). Do not accumulate `_validate_*` / `_map_*` / `_build_*` helpers beside a use case that operate on domain objects — move that behavior onto the entity, value object, or a named domain policy.
- **Naming**: Descriptive; no ambiguous abbreviations; avoid type-redundant names (`users` not `user_list`). Short names (`i`, `x`, `T`) OK in tiny scopes. Test names discoverable by the runner; `.py` filenames use `_`, never `-`.
- **`__all__`**: On package re-export `__init__.py` or any module with an intentional public surface. Prefer thin barrels — do not re-export across layer boundaries for convenience.
- **Ports**: Capability-focused and minimal. Prefer methods for operations; read-only properties only when fundamental to the capability and not exposing mutable state.
- **DTOs and validation ownership**:
  - Prefer DTOs as field-only models — no methods beyond validators. Shared DTO normalizers live in a helpers module, not beside the DTO classes.
  - *Presentation DTOs* — validate transport representation and syntax.
  - *Application commands/DTOs* — validate use-case input shape where input can originate from multiple transports; validators here must not contain business logic.
  - *Domain entities/value objects* — enforce business invariants near the state they protect.
  - *Infrastructure adapters/facades* — validate and map external provider or persistence representations.
  - Validation must not substitute for domain invariants. Use cases orchestrate; they must not become validation scripts for domain rules.

---

## 9. Testing

- **Prefer test-first** for new behavior and defect fixes (especially domain/application). Behavior changes need automated coverage before merge; spikes/emergency fixes need follow-up tests + tracked rationale.
- **CI**: fakes only — no real external services in developer or PR/CI runs.
- AAA; match the surrounding test style.
- No coverage inflation that bypasses business contracts.

---

## 10. Comments & Docstrings

### Comments — minimal by default
- Add comments only for an invariant, constraint, tradeoff, workaround, or external contract that the code and types cannot express. NEVER narrate control flow or restate adjacent code.
- Mandatory Line 1: a **one-line** module docstring stating what the file owns.

### Function / class docstrings
- Default: **no** docstring — types and names are the local documentation.
- **Write one** when types/names do not carry the contract: public API behavior, non-obvious domain rules, externally visible side effects, or integration-adapter quirks.
- Do not restate the signature or function name in prose.
- Structured sections (`Args` / `Returns` / `Raises` / …) only when they add information beyond types.

---

## Parting Words

BE CONSISTENT.

If you’re editing code, take a few minutes to look at the code around you and determine its style. If they use _idx suffixes in index variable names, you should too. If their comments have little boxes of hash marks around them, make your comments have little boxes of hash marks around them too.

The point of having style guidelines is to have a common vocabulary of coding so people can concentrate on what you’re saying rather than on how you’re saying it. We present global style rules here so you know the vocabulary, but local style is also important. If code you add to a file looks drastically different from the existing code around it, it throws readers out of their rhythm when they go to read it.

However, there are limits to consistency. It applies more heavily locally and on choices unspecified by the global style. Consistency should not generally be used as a justification to do things in an old style without considering the benefits of the new style, or the tendency of the codebase to converge on newer styles over time. 
* If the user wants to move to a new, better style, they should have the option.
