# 13. Packaging & console scripts — turn your app into a command

**Video:** (add Loom link)

## Why this matters
- Ship your tool as an **installable package** (wheel/sdist).
- Provide a **nice CLI command** (no `python app.py` required).
- Enable **pipx/global install** and easy versioning.

## Commands (copy–paste)
```bash
# Start from a project with app.py (e.g., Lesson 05 Typer app)
# Add minimal package structure
mkdir -p src/yourpkg
git mv app.py src/yourpkg/__main__.py  # keep module runnable via python -m yourpkg

# Add a tiny wrapper so console script has a function to call
cat >> src/yourpkg/__main__.py <<'PY'

def main():
    # entry point for console script; delegates to Typer app()
    # your Typer() instance should be named `app` above
    app()
PY

# Create/extend pyproject.toml with metadata + console script
cat > pyproject.toml <<'TOML'
[project]
name = "yourpkg"            # change to a unique name on (Test)PyPI
version = "0.1.0"
description = "Demo CLI with Typer"
readme = "README.md"
requires-python = ">=3.12"
dependencies = ["typer", "rich"]

# Console command "yourcmd" will call yourpkg.__main__:main
scripts = { yourcmd = "yourpkg.__main__:main" }

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
TOML

# Build distributable artifacts (wheel + sdist)
uv build

# Try it locally without polluting system Python
uv venv .venv-test && . .venv-test/bin/activate
pip install dist/*.whl
yourcmd --help

# (Optional) Install globally for your user with pipx (recommended for CLIs)
# pipx install dist/*.whl
```

## What to check
- `dist/yourpkg-0.1.0-py3-none-any.whl` exists.
- Running `yourcmd --help` shows your CLI help text.
- `python -m yourpkg --help` also works (module entry point).

## Common pitfall → fix
- **ImportError in `main()`** → ensure the import path matches your package layout (`yourpkg/__main__.py` and `scripts = { yourcmd = "yourpkg.__main__:main" }`).

## Next steps
- [14 — Publish to TestPyPI](./14-publish-to-testpypi.md)
