# 14. Publish to TestPyPI — dry‑run your release safely

**Video:** (add Loom link)

## Why this matters
- Validate your packaging **end‑to‑end** without touching real PyPI.
- Catch metadata/install issues **before** you publicize your package.
- Share a pip‑installable preview with teammates.

## Commands (copy–paste)
```bash
# Prereqs: you have a valid pyproject.toml from Lesson 13
# Ensure the name is unique on TestPyPI (e.g., append -demo or -yourhandle)
sed -n '1,120p' pyproject.toml

# Build fresh artifacts
uv build

# Publish to TestPyPI (will prompt for creds if needed)
# For token auth: username is __token__, password is your TestPyPI token
uv publish --repository testpypi
```

## Verify install from TestPyPI
```bash
# New shell or clean venv
uv venv .venv-installtest && . .venv-installtest/bin/activate

# Install from TestPyPI; add --extra-index-url so deps still resolve from PyPI
pip install --index-url https://test.pypi.org/simple --extra-index-url https://pypi.org/simple yourpkg

# Try the command
yourcmd --version || yourcmd --help
```

## What to check
- `uv publish` reports a successful upload with URLs to TestPyPI.
- `pip install` from TestPyPI succeeds and the command runs.

## Common pitfall → fix
- **`ERROR: 403 The name is already taken`** → pick a more unique `project.name`.
- **Install fails due to missing deps** → ensure `dependencies = [...]` are listed in `[project]` and rebuild.
- **Private tokens** → never commit tokens; paste when prompted or use env variables in CI.

## Next steps
- (Optional) Repeat with real PyPI once happy: `uv publish` (no `--repository`).
- [15 — Robust HTTP with httpx + retry](./15-httpx-robust-http.md)
