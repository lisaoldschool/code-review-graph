# Codebase Map

| Directory | Purpose |
|---|---|
| `code_review_graph` | Core Python package — MCP server, CLI, parser, graph store, tools |
| `code_review_graph/tools` | 30 MCP tool implementations split by domain |
| `code_review_graph/eval` | Evaluation benchmarks — impact accuracy, retrieval, token efficiency |
| `code_review_graph/assets` | Static assets (D3.js for visualization) |
| `code_review_graph/docs` | Inline package documentation |
| `code-review-graph-vscode` | VS Code extension — TypeScript, reads from SQLite graph DB |
| `code-review-graph-vscode/src` | Extension source — backend, features, views, webview |
| `tests` | Test suite with language fixture files |
| `tests/fixtures` | Sample source files for multi-language parser testing |
| `skills` | Shipped AI tool skills (build-graph, debug-issue, explore-codebase, etc.) |
| `docs` | Project documentation — architecture, schema, usage, troubleshooting |
| `scripts` | Utility scripts — PR comment renderer, PyPI diagnostics |
| `hooks` | Git hooks — session-start script and hooks.json |
| `diagrams` | Demo GIFs and architecture diagrams |
| `evaluate` | Evaluation result CSVs from benchmark runs |
| `.github/workflows` | CI — lint, type-check, security, test, eval, PR review, publish |
| `.github/ISSUE_TEMPLATE` | GitHub issue templates (bug, feature, platform request) |
