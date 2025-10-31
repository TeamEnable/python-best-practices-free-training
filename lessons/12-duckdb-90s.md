# 12. DuckDB in 90s — SQL analytics without a server

## Why this matters
- **Analytics engine:** columnar storage + vectorized execution = fast on medium/large data.
- **No server:** runs in‑process; great for notebooks, scripts, and local ETL.
- **Query data where it lives:** Parquet/CSV/JSON directly, no import step required.

## Commands (copy–paste)
```bash
uv add duckdb pandas

# sample CSV
cat > data.csv <<'CSV'
city,temp
Paris,18
Lyon,19
Nice,23
CSV

cat > duckdemo.py <<'PY'
import duckdb, pandas as pd
df = pd.read_csv('data.csv')
# Filter & sort using SQL over a DataFrame
q = duckdb.query("SELECT city, temp FROM df WHERE temp >= 19 ORDER BY temp DESC")
print(q.df())
PY

uv run python duckdemo.py
```

## What to check
- A small table with Lyon/Nice ordered by temp.
- DuckDB ran a SQL query **over a pandas DataFrame** (no DB server).

## Common pitfall → fix
- **Package version clashes** → pin versions in `pyproject.toml` and keep `uv.lock` committed. In CI, use `uv sync --frozen`.

## Next steps
- Explore `duckdb.sql("SELECT * FROM 'file.parquet'")` to query files directly.

