# Contributing Guide

Thanks for improving this free training!

## How to propose a lesson
1. Copy `lessons/_template.md` to a new file: `NN-short-title.md` (use a 2‑digit index).
2. Fill the sections. Keep it **practical** and **≤ 2 screens**.
3. If you add code, prefer one file per lesson in `samples/NN-*`.
4. Open a PR with a short description and a checklist of what you tested.

## Style
- Commands first, rationale second. Each command should have a one‑line “Why this matters” explanation.
- Prefer `uv`, `uvx`, `pytest`, `ruff`, `black`, `mypy`, `typer`, `httpx`, `pydantic`, `python-dotenv`.
- Keep videos optional; text must stand on its own.
- Use neutral English; avoid OS‑specific paths unless needed.

## Pre-commit
This repo ships a `pre-commit` config. Local use is optional:
```bash
uvx pre-commit install
```
