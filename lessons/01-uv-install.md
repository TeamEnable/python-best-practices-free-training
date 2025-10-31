# 01. Install uv safely — stop touching system Python

**Video:** 

## Why this matters
- Keeps system Python pristine; no `sudo pip`.
- Faster installs, built-in Python version management.
- Reproducible per‑project environments.

## Commands (copy–paste)
```bash
# install uv (user scope)
curl -LsSf https://astral.sh/uv/install.sh | sh

# verify install
uv --version
which uv  # should be in ~/.cargo/bin or ~/.local/bin depending on installer

# safety proof: show system vs project python
python3 -V
uv run python -V
```

## What to check
- `uv --version` prints a version number.
- `uv run python -V` works even outside a venv.

## Common pitfall → fix
- **`uv: command not found`** → Open a new shell or add the printed PATH snippet to your shell rc.

## Next steps
- [02 — Hello, uv project](./02-hello-uv-project.md)
