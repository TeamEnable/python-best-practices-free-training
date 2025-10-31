# 03. Materialize env & editor integration — smooth day‑to‑day dev

**Video:** 

## Why this matters
- Your tools (VS Code, PyCharm, linters) need a **visible** interpreter.
- A materialized env (like `.venv/`) removes guesswork and CI drift.

## Commands (copy–paste)
```bash
# If you haven't yet, create or update the project env from the lockfile
uv sync

# Alternatively, make a venv explicitly (uv-managed) in the project
uv venv
source .venv/bin/activate

# Prove the interpreter lives inside .venv
(.venv) uv run python -c "import sys,site; print(sys.executable); print(site.getsitepackages())"

# Editor integration (VS Code example)
# 1) Open Command Palette → "Python: Select Interpreter"
# 2) Pick: ./.venv/bin/python
# Linting/formatting on demand:
uvx ruff .
uvx mypy .
```

## What to check
- `.venv/` folder exists with `bin/python` (or `Scripts\python.exe` on Windows).
- `uv run python -c "import sys; print(sys.executable)"` points inside `.venv`.

## Common pitfall → fix
- **Editor still uses system Python** → re-select interpreter in the editor; ensure workspace settings aren’t overriding it.

## Notes
- `uv add <pkg>` updates `pyproject.toml` and **installs** into the project env by default. Use `--no-sync` to skip immediate install, or `--active` to target your currently activated venv.
- In CI, prefer: `uv sync --frozen` to enforce your `uv.lock`.

## Next steps
- [04 — Tooling with uvx](./04-tooling-uvx.md)
