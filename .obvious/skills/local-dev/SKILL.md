---
name: local-dev
---

# Local Dev — lisaoldschool/code-review-graph

Recorded by the autobuild onboarding run of 2026-10-15. Everything below was
executed and verified live in the sandbox.

## Stack

- Pure Python CLI + MCP server. Python >=3.10 (CI matrix: 3.10–3.13).
- Package manager: **uv** (`uv.lock` committed). Build backend: hatchling.
- Storage: local SQLite graph DB (`*.db`, repo-local `.code-review-graph/`).
- **No external services** — no Postgres, Redis, Docker, or env vars required.
  Nothing to bring up before the app runs.
- Sub-app: `code-review-graph-vscode/` (Node/TypeScript VS Code extension,
  esbuild; independent of the Python dev loop).

## Setup

```bash
pip install uv          # if uv is not already on PATH (sandbox: Python 3.13.14)
uv sync --extra dev     # creates .venv, installs runtime + dev deps
```

## Verify the stack (canonical commands)

```bash
uv run ruff check code_review_graph/                                          # lint
uv run mypy code_review_graph/ --ignore-missing-imports --no-strict-optional  # typecheck
uv run pytest tests/ --tb=short -q                                            # tests (~30s)
```

## Primary user flow (end-to-end proof)

```bash
uv run code-review-graph build    # parse repo → SQLite graph (261 files, 5254 nodes, 43441 edges)
uv run code-review-graph status   # node/edge/file/language counts
uv run code-review-graph search "parse_files"   # FTS search over the graph
```

Observed 2026-10-15: build ran migrations to schema v9 and postprocessed
(flows, FTS, resolvers); status listed 34 languages; search returned ranked
JSON results. Lint clean, mypy clean (70 files), pytest 2398 passed /
5 skipped / 2 xpassed.

## Gotchas

- `tree-sitter-language-pack` download is the slow part of `uv sync`; uv cache
  makes re-syncs fast.
- The build command writes `*.db` files into the repo — gitignored, but they
  bloat sandbox disk if rebuilt repeatedly.
- Ruff line-length is 100, target py310; mypy runs with
  `--ignore-missing-imports --no-strict-optional` — match CI flags exactly.
- Full test suite needs `pytest-asyncio` (asyncio_mode = "auto" in
  pyproject.toml) — installed by `uv sync --extra dev`.
