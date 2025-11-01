# 16. Git tricks + gh for speed — ship faster with cleaner history

This lesson shows a fast, reproducible Git/Hub workflow and points to an appendix with 15 Python‑specific Git tips.

> **Appendix:** See [resources/git-for-python.md](../resources/git-for-python.md).

## Why this matters
- Faster repo setup, cleaner diffs, and safer reviews.
- `gh` (GitHub CLI) removes browser hops for common tasks.
- Python‑friendly patterns (src layout, hooks, lockfiles).

## Commands (copy–paste)

### A) Create a public repo in an organization and push (1 command)
```bash
# from an initialized repo (git init + first commit done)
# replace ORG/REPO
gh repo create ORG/REPO --public --source . --remote origin --push
```

### B) Protect your history with quality gates (pre-commit)
```bash
uvx pre-commit install
pre-commit run --all-files
git add -A && git commit -m "chore: run hooks"
```

### C) Open a PR from the terminal
```bash
git checkout -b feat/cli-help
# or git switch -c feat/cli-help

# edit files…
git add -A && git commit -m "feat(cli): improve --help output"

git push -u origin HEAD
gh pr create --fill --web   # opens PR in a browser tab pre-filled
```

### D) Review locally: fetch, checkout, test, and comment
```bash
gh pr checkout <PR_NUMBER_OR_URL>
uv run pytest -q
gh pr comment <PR_NUMBER> --body "All tests green locally ✅"
```

### E) Tidy commits with autosquash (polish before merge)
```bash
# create fixups targeting earlier commits
git commit --fixup <SHA1>
git commit --fixup <SHA2>

# autosquash into a clean story
git rebase -i --autosquash origin/main
```

### F) Bisect a failing test (automatic)
```bash
git bisect start
git bisect bad
git bisect good <known_good_tag_or_sha>
git bisect run uv run pytest -q
git bisect reset
```

### G) Worktrees for parallel branches (e.g., test Python 3.12 vs 3.11)
```bash
git worktree add ../repo-3.12 feat/py312
# open both folders in two editors/terminals and compare behavior
```

## What to check
- PR was opened with correct title/body.
- Hooks run automatically on commit and fix formatting/linting.
- `gh pr checkout` pulled the PR code locally.

## Common pitfall → fix
- **`gh: command not found`** → install GitHub CLI and authenticate: `gh auth login`.
- **Hooks didn’t run** → re-run `uvx pre-commit install` in the repo root.
- **Autosquash rebase conflicts** → resolve, `git add .`, then `git rebase --continue`.

## Next steps
- Read the appendix: [Git for Python developers — 15 practical tips](../resources/git-for-python.md).
