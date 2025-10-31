# 04. Tooling via uvx — ruff, black, mypy without local installs

**Video:** 

## Why this matters
- Zero-install tools; clean system.
- Same tool versions in CI and locally.

## Commands (copy–paste)
```bash
# run tools ad-hoc
uvx ruff --version
uvx black --version
uvx mypy --version

# try them on the codebase
uvx ruff .
uvx black .
uvx mypy .
```

## What to check
- Tools run without creating a global venv.

## Common pitfall → fix
- **`No module named ...`** in mypy → add types stubs: `uv add --dev types-requests` etc.

## Next steps
- [05 — Typer micro‑CLI](./05-typer-cli.md)
