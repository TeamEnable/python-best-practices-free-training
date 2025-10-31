# 08. Logging with rotation — printable diagnostics, not prints

## Why this matters
- Persistent logs for debugging and support.
- Avoids noisy terminals in production.

## Commands (copy–paste)
```bash
cat > logdemo.py <<'PY'
import logging
from logging.handlers import RotatingFileHandler
from pathlib import Path

log_dir = Path("logs"); log_dir.mkdir(exist_ok=True)
handler = RotatingFileHandler(log_dir / "app.log", maxBytes=20_000, backupCount=3)
logging.basicConfig(level=logging.INFO, handlers=[handler], format="%(asctime)s %(levelname)s %(message)s")

logging.info("App started")
logging.warning("Mind the gap")
print("Done")
PY

uv run python logdemo.py
ls -la logs && tail -n 5 logs/app.log
```

## What to check
- `logs/app.log` exists and contains entries.

## Common pitfall → fix
- **Permission denied** → write logs under project folder or `$XDG_STATE_HOME`.

## Next steps
- [08b — Structured logging (JSON)](./08b-structured-logging.md)
