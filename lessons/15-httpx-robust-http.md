# 15. Robust HTTP with httpx + retry — timeouts, backoff, and errors done right

## Why this matters
- Network calls **fail**: timeouts, DNS issues, 5xx. You need predictable behavior.
- Good defaults (timeouts) + **bounded retries** = resilient clients.
- A small helper keeps your code clean and testable.

## Commands (copy–paste)
```bash
uv add httpx

cat > fetch.py <<'PY'
from __future__ import annotations
import time
from typing import Any, Dict, Optional
import httpx

DEFAULT_TIMEOUT = httpx.Timeout(5.0, connect=5.0)  # per op timeouts

def get_json(
    url: str,
    params: Optional[Dict[str, Any]] = None,
    max_retries: int = 3,
    backoff_base: float = 0.3,
) -> Dict[str, Any]:
    """
    GET JSON with timeouts + bounded retries on transient errors.
    Retries on network errors and 5xx. No retry on 4xx by default.
    """
    attempt = 0
    while True:
        try:
            with httpx.Client(timeout=DEFAULT_TIMEOUT, follow_redirects=True) as client:
                r = client.get(url, params=params, headers={"Accept": "application/json"})
            if 500 <= r.status_code < 600:
                raise httpx.HTTPStatusError("server error", request=r.request, response=r)
            r.raise_for_status()
            return r.json()
        except (httpx.RequestError, httpx.HTTPStatusError) as exc:
            attempt += 1
            # Do not retry on 4xx (client errors)
            if isinstance(exc, httpx.HTTPStatusError) and 400 <= exc.response.status_code < 500:
                raise
            if attempt > max_retries:
                raise
            sleep = backoff_base * (2 ** (attempt - 1))  # 0.3, 0.6, 1.2...
            time.sleep(sleep)
PY

# Try it against a public JSON API (replace with your own)
uv run python - <<'PY'
from fetch import get_json
data = get_json("https://httpbin.org/get", params={"q":"uv"})
print("ok:", bool(data.get("url")))
PY
```

> For async workloads: switch to `httpx.AsyncClient` with `asyncio`.

## What to check
- Output `ok: True` (or your API returns expected keys).
- Changing `max_retries=0` triggers faster failure.

## Common pitfall → fix
- **Hanging requests** → always set timeouts (per‑op or overall).
- **Retry storms** → bound retries (3–5), exponential backoff, and avoid retrying on 4xx.
- **Inconsistent SSL/certs behind corporate proxies** → configure system CA bundle or set `verify=` path (last resort).

## Next steps
- Add auth (API keys via `.env` from Lesson 09).
- Add structured logging (Lesson 08b).
- [16 — Git tricks + gh for speed](./16-git-tricks-and-gh-for-speed.md)