<!-- TEMPLATE: Inject-ready guide. Replace every FILL block with target-repo facts; delete this banner when done. -->

# AI Guide: Test Layout & Harness Reference

Where tests live, how to run them, available fakes/fixtures, and testing conventions.

---

## Configuration and commands

Config file: `tests/pytest.ini` (`pythonpath`, markers, coverage omit patterns).

<!-- FILL: Coverage gate percentage and where enforced (pytest.ini, pyproject.toml). Discover: tests/pytest.ini, pyproject.toml [tool.coverage] or pytest addopts. Keep: single percentage; default template uses 80% unless repo differs. -->

Coverage gate: **80%**.

```bash
python -m pytest tests -m unit -v --tb=short
python -m pytest tests --cov=app
```

Markers: 

---

## Example patterns

### Application layer

<!-- FILL: One representative unit test snippet for a primary use case. Discover: tests/application/. Keep: short pytest function showing fakes, execute input, and key assertion. -->

### Presentation layer

<!-- FILL: One representative HTTP/router or formatter test snippet. Discover: tests/presentation/. Keep: short pytest function with TestClient or router wiring and key assertion. -->

---

## Fakes and fixtures

### `tests/utils/` (or equivalent)

<!-- FILL: Table of Fake/Mock classes — name, purpose, key members. Discover: tests/utils/fakes.py, mocks, stubs. Keep: markdown table. Omit subsection if no shared fakes. -->

### `tests/conftest.py`

<!-- FILL: Table of fixtures/helpers — name, what they provide. Discover: tests/conftest.py. Keep: markdown table. -->

### API / app factory helpers

<!-- FILL: build_test_app, build_client, or equivalent — one line each describing wiring. Discover: tests/utils/api_factory.py or conftest. Keep: bullet list. Omit if none. -->