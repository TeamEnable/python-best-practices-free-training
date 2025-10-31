# 05. Typer micro‑CLI — friendly help in one file

**Video:** 

## Why this matters
- Users discover features via `--help`.
- Easy to package as a console script later.

## Commands (copy–paste)
```bash
# add to your virtual env
uv add typer rich

cat > app.py <<'PY'
import typer
from rich import print

app = typer.Typer(help="Demo CLI")

@app.command()
def greet(name: str):
    print(f"👋 Hello, [bold]{name}[/]!")

if __name__ == "__main__":
    app()
PY

uv run python app.py --help
uv run python app.py greet world
```

## What to check
- Help screen shows command and options.
- `greet world` prints a styled message.

## Common pitfall → fix
- **`ModuleNotFoundError: rich`** → Run inside the project: `uv run python app.py ...`

## Next steps
- [06 — Testing with pytest](./06-testing-pytest.md)
