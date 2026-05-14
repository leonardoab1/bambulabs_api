# CLAUDE.md — bambulabs_api conventions

## Rules
- Never use cd command, as it's redundant. Run all commands directly in the root directory using relative/absolute paths

## Language & runtime
- Python >= 3.10; use modern union syntax (`str | None`, not `Optional[str]`)
- All public methods must have full type annotations and return type declarations

## Module conventions
- Every module must define `__all__`
- Import order: stdlib → third-party → local (use relative imports within the package)
- No bare `print()` — use `bambulabs_api.logger` (`logging.getLogger("bambulabs_api")`)

## Style
- Linting: `flake8` — must pass with zero errors before any commit
- Docstrings on all public methods/classes — NumPy style (`Parameters` / `Returns` sections)
- Enums: extend both value type and `Enum` (e.g. `class Foo(str, Enum)`) and define `__str__` returning `self.value`
- Structured data: use `@dataclass`, not plain dicts

## Architecture
- **Facade pattern**: `client.py::Printer` is the public entry point; it delegates to protocol clients
- Three protocol clients: `PrinterMQTTClient` (command/state), `PrinterFTPClient` (files), `PrinterCamera` (video)
- **New printer models**: add to `PrinterType` enum in `printer_info.py` — no other change needed if using the same MQTT stack
- **New protocols**: create a new `*_client.py` following the same class/decorator patterns as the existing clients

## Testing
- Framework: `pytest`
- Use `PrinterMQTTClient.manual_update()` for state-parsing tests — no real printer required
- Test files live in `tests/`, named `test_<module>.py`
- Both `pytest tests/` and `flake8 bambulabs_api/` must pass before opening a PR

## Dependencies
- Runtime deps: `pyproject.toml` → `[project] dependencies`
- Dev deps: `requirements.txt`
- Do NOT introduce heavy dependencies without discussion in the PR

## Commits / PR
- Upstream target: `BambuTools/bambulabs_api` `main` branch
- Feature branches: `feat/<short-name>`
- Each commit should independently pass `flake8` and `pytest`
- Do NOT commit: `PLANNING.md`, `.env` files, `*.json` (already in `.gitignore`)

## Fork-specific files
- `CLAUDE.md` and `.gitignore` changes are committed to `main` on the user's own fork — they are not part of any PR to upstream
- Keep fork-only changes on `main`; feature branches contain only the code changes intended for the upstream PR
- This means PRs will never include `CLAUDE.md` or fork-local `.gitignore` additions in their diff
