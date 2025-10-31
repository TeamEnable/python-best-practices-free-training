# 07. Pre‑commit hooks — automate code quality

## Why this matters
- Consistent formatting and linting on every commit.
- Saves review time; fewer nitpicks.

## Commands (copy–paste)
```bash
uv add --dev pre-commit

cat > .pre-commit-config.yaml <<'YAML'
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.7.2
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.11.2
    hooks:
      - id: mypy
        additional_dependencies: [types-requests]
        args: [--ignore-missing-imports]
YAML

uv run pre-commit install
git add -A && git commit -m "chore: enable pre-commit"
```

## What to check
- A commit runs hooks and reports success/fixes.

## Common pitfall → fix
- **Hook fails** → run the reported tool locally (e.g., `uvx ruff .`) and fix issues.

## Next steps
- [08 — Logging with rotation](./08-logging.md)
