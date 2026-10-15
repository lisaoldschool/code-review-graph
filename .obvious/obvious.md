# Obvious repo guidance

<!-- obvious-install: skill=autobuild-setup, skill-version=1.0.3, template-version=1 -->

This repo uses `.obvious/` for reviewed Autobuild guidance.

Before editing, suggest the smallest relevant set of `.obvious` files for the task. Match candidate files by reading their frontmatter.

## Codebase Map

See `.obvious/codebase-map.md`.

## Repo Guidance for Autobuild

<!-- synthesized from: AGENTS.md, CLAUDE.md, CONTRIBUTING.md, GEMINI.md — agent-relevant rules only -->

### Development Commands

```bash
# Install (requires uv)
uv sync --extra dev

# Lint
uv run ruff check code_review_graph/

# Type check
uv run mypy code_review_graph/ --ignore-missing-imports --no-strict-optional

# Tests
uv run pytest tests/ --tb=short -q

# Tests with coverage (CI enforces >= 65%)
uv run pytest --cov=code_review_graph --cov-report=term-missing --cov-fail-under=65

# Single test file
uv run pytest tests/test_parser.py -v

# Build graph
uv run code-review-graph build

# Start MCP server
uv run code-review-graph serve
```

### Code Conventions

- **Line length:** 100 chars (ruff, rules: E, F, I, N, W)
- **Python target:** 3.10+
- **SQL:** Always use parameterized queries (`?` placeholders), never f-string values
- **Error handling:** Catch specific exceptions, log with `logger.warning/error`
- **Thread safety:** `threading.Lock` for shared caches, `check_same_thread=False` for SQLite
- **Node names:** Always sanitize via `_sanitize_name()` before returning to MCP clients
- **File reads:** Read bytes once, hash, then parse (TOCTOU-safe pattern)
- **Imports:** Sorted by ruff (isort-compatible)

### Security Invariants

- No `eval()`, `exec()`, `pickle`, or `yaml.unsafe_load()`
- No `shell=True` in subprocess calls
- `_validate_repo_root()` prevents path traversal via repo_root parameter

### Shell Usage

Use non-interactive flags with file operations to avoid hanging on confirmation prompts:
`cp -f`, `mv -f`, `rm -f`, `scp -o BatchMode=yes`, `ssh -o BatchMode=yes`

### CI Requirements

- Lint (ruff), type-check (mypy), security (bandit), schema-sync (Python/VSCode version match)
- Tests run on Python 3.10, 3.11, 3.12, 3.13
- Coverage threshold: 65%
- Windows-native job for daemon and file handle tests

## Local Verification

> **Warning:** Running full-repo typecheck, lint, or tests may OOM or timeout in the sandbox for large repos.
> Use the scoped commands below when verifying changes.

### Verified Commands (re-verified 2026-10-15)

- **Lint:** `uv run ruff check code_review_graph/` — all checks passed
- **Typecheck:** `uv run mypy code_review_graph/ --ignore-missing-imports --no-strict-optional` — no issues in 70 source files
- **Test:** `uv run pytest tests/ --tb=short -q` — 2398 passed, 5 skipped, 2 xpassed

### Primary User Flow (verified 2026-10-15)

```bash
uv run code-review-graph build    # parsed 261 files → 5254 nodes, 43441 edges, schema v9
uv run code-review-graph status   # 5202 nodes / 43091 edges, 34 languages
uv run code-review-graph search "parse_files"  # FTS search returns ranked results
```

### Scoped Workflow

1. **Lint changed files:** `uv run ruff check code_review_graph/<changed_file>.py`
2. **Typecheck changed files:** `uv run mypy code_review_graph/<changed_file>.py --ignore-missing-imports --no-strict-optional`
3. **Test changed files:** `uv run pytest tests/test_<module>.py -v`

## Sandbox Snapshot

- **Snapshot ID:** `5ilamfpjtht9cfa8lpfa:default`
- **Captured:** 2026-10-15T22:11:00Z
- **Dev stack healthy:** yes
- **Contents:** Python 3.13.14, uv 0.13.1, `.venv` synced via `uv sync --extra dev`, graph DB built at repo root

## Bibliography

7 Product Atlas nodes created (seed-level):

| Node | Type | Slug |
|---|---|---|
| code-review-graph | system | code-review-graph |
| VS Code Extension | system | code-review-graph-vscode |
| MCP Tools | feature | mcp-tools |
| Tree-sitter Parser | infrastructure | tree-sitter-parser |
| SQLite Graph Store | infrastructure | sqlite-graph-store |
| GitHub PR Review Action | integration | github-pr-review-action |
| Evaluation Framework | system | evaluation-framework |

> **Product Atlas:** The setup scan created seed-level nodes. For comprehensive coverage, load the `product-atlas-collection` skill.

## Runbooks

Populated by autobuild-runbooks skill when requested. See `.obvious/runbooks/` after that skill runs.
