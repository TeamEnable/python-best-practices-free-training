# 06. Testing with pytest — fast, readable tests
**Video:** (add Loom link)

## Why this matters
- Confident refactors; CI‑friendly.
- Fewer regressions in demos and real apps.

## Commands (copy–paste)
```bash
uv add --dev pytest

mkdir -p tests
cat > tests/test_sample.py <<'PY'
def test_math():
    assert 2 + 2 == 4
PY

uv run pytest -q
```

## What to check
- `1 passed` in the output.

## Common pitfall → fix
- **No tests collected** → Ensure files are named `test_*.py` or `*_test.py` in `tests/`.

## Next steps
- [07 — Pre‑commit hooks](./07-pre-commit.md)
