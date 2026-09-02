<!-- TEMPLATE: Inject-ready guide. Replace every FILL block with target-repo facts; delete this banner when done. -->

# AI Guide: Hexagonal Architecture & Package Layout

---

## Layer Map & Package Responsibilities

<!-- FILL: Composition root path (e.g. app/http_bootstrap.py). Discover: search wire_app, bootstrap, or factory in app/. Keep: single sentence naming the file and wire_app function if present. -->

### Domain Layer (app/domain/)
- **models/**: Domain entities and value objects (identity, invariants, and state transitions live here).
<!-- FILL: List key model modules (e.g. user_model.py, order_model.py). Discover: app/domain/models/. Keep: bullet list with brief role per file. -->
- **repositories/**: Persistence Protocols for entity load/save contracts owned by the aggregate.
<!-- FILL: List repository protocol modules. Discover: app/domain/repositories/. Keep: bullet list with interface purpose. -->
- **ports/** / **policies/**: Domain-owned abstractions (clocks, pricing policies, deterministic ID generators used inside domain rules).
<!-- FILL: List domain port/policy modules or note "omit if none". Discover: app/domain/ports/, app/domain/policies/. Keep: bullet list or omit. -->
- **services/**: Pure domain logic, domain invariants, and calculations when behavior has no natural entity owner.
<!-- FILL: List domain service modules. Discover: app/domain/services/. Keep: bullet list with responsibility per service. -->
- **exceptions.py**: Domain exception hierarchy (e.g., CustomError and subclasses). Never raise HTTP/FastAPI exceptions here.
<!-- FILL: Root exception class and key subclasses. Discover: app/domain/exceptions.py. Keep: one-line hierarchy summary. -->

### Application Layer (app/application/)
- **use_cases/**: Single-responsibility use case classes implementing execute(...) (or equivalent).
<!-- FILL: List primary use case modules. Discover: app/application/use_cases/. Keep: bullet list with orchestration role. -->
- **ports/**: Application orchestration and external I/O Protocols (aggregate loaders, blob/file storage, publishers, gateways).
<!-- FILL: List port modules. Discover: app/application/ports/. Keep: bullet list with port purpose. -->
- **dtos/**: Pydantic input/output DTOs for use case boundary transfer (field-only models).
<!-- FILL: List key DTO modules or patterns. Discover: app/application/dtos/. Keep: bullet list or note if DTOs live elsewhere. -->

### Infrastructure Layer (app/infrastructure/)
- **database/** (or equivalent): Adapters implementing domain repository protocols.
<!-- FILL: List adapter classes/modules. Discover: app/infrastructure/. Keep: bullet list with protocol implemented. -->
- **clients/** (or equivalent): Third-party API clients and blob adapters implementing application ports.
<!-- FILL: List client/adapter modules. Discover: app/infrastructure/. Keep: bullet list with external system targeted. -->

### Presentation Layer (app/presentation/)
- **routers/**: Function-based endpoint handlers (prefer DI for use cases and request context). Class-based handlers are acceptable when extending existing modules.
<!-- FILL: List router modules and primary routes. Discover: app/presentation/routers/. Keep: bullet list with route prefix or tag. -->
- **schemas/**: HTTP request and response DTOs.
<!-- FILL: List key schema modules. Discover: app/presentation/schemas/. Keep: bullet list. -->
- **formatters/**: Custom response formatters or narrative message builders when applicable.
<!-- FILL: List formatter modules or note "omit if none". Discover: app/presentation/formatters/. Keep: bullet list or omit section. -->
