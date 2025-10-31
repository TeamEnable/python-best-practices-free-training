# 09. Config with python‑dotenv — secrets without hardcoding

**Video:** (add Loom link)

## Why this matters
- Keep secrets out of code.
- Easy local overrides; works in CI.

## Commands (copy–paste)
```bash
uv add python-dotenv

cat > .env <<'ENV'
API_TOKEN=abc123
ENV

cat > cfgdemo.py <<'PY'
import os
from dotenv import load_dotenv
load_dotenv()
print("Token present:", bool(os.getenv("API_TOKEN")))
PY

uv run python cfgdemo.py
```

## What to check
- Output prints `Token present: True`.

## Common pitfall → fix
- **`.env` committed to git** → add to `.gitignore` or use secret managers in prod.

## Next steps
- [10 — SQLite in 90s](./10-sqlite-90s.md)
