# 02. Hello, uv project — from empty folder to runnable app

**Video:**

## Why this matters
- Standard layout in seconds; avoids manual setup.
- Lockfile ensures teammates get the same env.

## Commands (copy–paste)
```bash
mkdir hello-uv && cd hello-uv
uv init --package

# Optional but recommended, so we'll do it:
# Create an in-project virtualenv
uv venv
# then, activate it
source .venv/bin/activate

# add a dependency and run a quick check
(.venv) uv add requests
(.venv) uv run python -c "import requests; print('ok')"

# show files
ls -la
cat pyproject.toml
```

> We will talk again about these commands in lesson 3, because they are important!

## What to check
- `pyproject.toml` and `uv.lock` exist.
- The import prints `ok`.

## Common pitfall → fix
- **SSL or network errors** → retry `uv add` later; proxy users: set `HTTPS_PROXY` env.

## Next steps
- [03 — Materialize env & editor integration](./03-materialize-env-and-editor.md)
