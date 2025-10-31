# 08b. Structured logging (JSON) — logs that are easy to query

## Why this matters
- Logs become **data**: key–value pairs you can filter with `jq`, ship to ELK/OpenSearch, etc.
- Stable fields → actionable alerts and dashboards.
- Works locally and in prod; pairs with rotation from Lesson 08.

## Option A — stdlib JSON (no extra deps)
```python
# file: jsonlog.py
import json, logging, time
from logging.handlers import RotatingFileHandler
from pathlib import Path

class JsonFormatter(logging.Formatter):
    _skip = {"args","asctime","created","exc_info","exc_text","filename","funcName",
             "levelname","levelno","lineno","module","msecs","message","msg","name",
             "pathname","process","processName","relativeCreated","stack_info",
             "thread","threadName"}
    def format(self, record: logging.LogRecord) -> str:
        payload = {
            "ts": time.strftime("%Y-%m-%dT%H:%M:%S%z", time.localtime(record.created)),
            "level": record.levelname,
            "msg": record.getMessage(),
            "logger": record.name,
        }
        if record.exc_info:
            payload["exc"] = self.formatException(record.exc_info)
        for k, v in record.__dict__.items():  # include extra= fields
            if k not in self._skip:
                payload[k] = v
        return json.dumps(payload, ensure_ascii=False)

log_dir = Path("logs"); log_dir.mkdir(exist_ok=True)
handler = RotatingFileHandler(log_dir / "app.jsonl", maxBytes=200_000, backupCount=3)
handler.setFormatter(JsonFormatter())

root = logging.getLogger()
root.setLevel(logging.INFO)
root.handlers[:] = [handler, logging.StreamHandler()]  # file + console

logging.info("app_started", extra={"version": "0.1.0"})
logging.warning("rate_limit", extra={"user_id": 42, "attempts": 5})
try:
    1/0
except ZeroDivisionError:
    logging.exception("calc_failed", extra={"op": "divide"})
print("Done")
```

**Run & inspect**
```bash
uv run python jsonlog.py
tail -n +1 logs/app.jsonl
# pipe-friendly examples
uvx jq -r 'select(.level=="WARNING") | .user_id' logs/app.jsonl
```

**Pitfalls → fixes**
- **Secrets in logs** → redact or avoid sensitive fields.
- **Multiline** → keep **one JSON per line** (`.jsonl`) to simplify parsing.

## Option B — structlog (clean API + context)
```bash
uv add structlog
```

```python
# file: structjson.py
import logging, sys, structlog

logging.basicConfig(
    level=logging.INFO,
    handlers=[logging.StreamHandler(sys.stdout)],
)

structlog.configure(
    processors=[
        structlog.contextvars.merge_contextvars,
        structlog.processors.add_log_level,
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.processors.JSONRenderer(),
    ],
    wrapper_class=structlog.make_filtering_bound_logger(logging.INFO),
    context_class=dict,
)

log = structlog.get_logger()
log.info("app_started", version="0.1.0")

# Bind request-level context (propagates to subsequent logs)
import structlog.contextvars as ctx
ctx.bind_contextvars(request_id="abc123")
log.warning("rate_limit", user_id=42, attempts=5)
```

**Write to file with rotation**
- structlog emits via stdlib; add a `RotatingFileHandler` to the root logger (see Option A) if you want a JSON file on disk.

## Next steps
- Use `.env` (Lesson 09) to control `LOG_LEVEL` and log destinations.
- In Lesson 15, send key request/response fields into logs for API calls.
- [09 — Config with python‑dotenv](./09-config-dotenv.md)
