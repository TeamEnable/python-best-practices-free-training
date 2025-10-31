# 10. SQLite in 90s — lightweight DB you already have

## Why this matters
- **Zero setup:** ships with Python (`sqlite3` in the stdlib).
- **File-based, transactional:** perfect for small apps, CLIs, caching, tests.

> Note: For analytics use cases, you can later swap to DuckDB. See [DuckDB in 90s](./12-duckdb-90s.md).

## Commands (copy–paste)
```bash
# Create and query a tiny DB from Python (no extra deps)
cat > sqldemo.py <<'PY'
import sqlite3, csv, pathlib
db = "app.db"

# sample CSV
pathlib.Path("data.csv").write_text("city,temp
Paris,18
Lyon,19
Nice,23
", encoding="utf-8")

con = sqlite3.connect(db)
cur = con.cursor()

# idempotent table creation + clean slate for demo
cur.execute("CREATE TABLE IF NOT EXISTS weather (city TEXT, temp INTEGER)")
cur.execute("DELETE FROM weather")

# load CSV -> table
with open("data.csv") as f:
    for row in csv.DictReader(f):
        cur.execute("INSERT INTO weather(city,temp) VALUES (?,?)", (row["city"], int(row["temp"])))

# index for faster filtering/sorting
cur.execute("CREATE INDEX IF NOT EXISTS idx_weather_temp ON weather(temp)")

# parameterized query (safe) + order by temp desc
for row in cur.execute("SELECT city, temp FROM weather WHERE temp >= ? ORDER BY temp DESC", (19,)):
    print(row)

con.commit()
con.close()
PY

uv run python sqldemo.py
```

## What to check
- `app.db` exists (a single portable file you can git-ignore or ship).
- Output shows: `('Nice', 23)` then `('Lyon', 19)`.

## Common pitfall → fix
- **`database is locked`** during concurrent writes → keep one writer connection per process; use short transactions (already committed above).

## Next steps
- [11 — Notebooks via uvx (Jupyter)](./11-notebooks-via-uvx-(jupyter).md)
