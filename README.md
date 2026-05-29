# diffscribe 🖊️

> **Turn your staged diff into a perfect conventional commit message — instantly, locally, no API keys required.**

`diffscribe` analyzes your staged git diff, categorizes every changed file, detects scope, determines commit type (`feat`, `fix`, `docs`, `perf`, `refactor`, …), and outputs a ready-to-use conventional commit message.

No LLM. No cloud. No dependencies. Just smart heuristics and your diff.

## Features

- 🏷️ **Auto-detects commit type** — `feat`, `fix`, `docs`, `perf`, `refactor`, `test`, `ci`, `style`, `chore`
- 📦 **Auto-detects scope** — infers the most relevant directory/module from your changed files
- 🔬 **File-level classification** — each file is analyzed for fix/perf/refactor/doc/test patterns
- 🔡 **Language detection** — recognizes 50+ file types by extension
- 🎨 **Emoji prefixes** — ✨ feat, 🐛 fix, 📝 docs, ⚡ perf, and more
- ✏️ **Interactive mode** — accept, customize, or override the suggested message
- 📋 **JSON output** — machine-readable format for CI/build scripts
- ⚡ **Zero dependencies** — pure Python 3.10+ stdlib

## Installation

### From source

```bash
git clone https://github.com/IndraTensei/diffscribe.git
cd diffscribe
cp diffscribe /usr/local/bin/diffscribe  # or ~/.local/bin/diffscribe
chmod +x /usr/local/bin/diffcribe
```

### Quick one-liner

```bash
curl -fsSL https://raw.githubusercontent.com/IndraTensei/diffscribe/main/diffscribe \
  -o /usr/local/bin/diffscribe && chmod +x /usr/local/bin/diffscribe
```

### pip (placeholder)

```bash
pip install diffscribe  # not yet published — clone is recommended for now
```

## Quick Start

Stage your changes and run:

```bash
git add src/api/handlers.py tests/test_api.py
diffscribe
```

Output:

```
✨ feat(api): update 2 Python files

Changes:
- src/api/handlers.py (+42/-8)
- tests/test_api.py (+31/-0)

[diffscribe] 73 additions, 8 deletions across 2 file(s)
```

Commit with it:

```bash
diffscribe --commit
```

## Usage

```
diffscribe [options]
```

| Flag | Short | Description |
|------|-------|-------------|
| `--type TYPE` | `-t` | Override detected type (`feat`, `fix`, `docs`, `refactor`, `perf`, `test`, `ci`, `style`, `chore`) |
| `--scope SCOPE` | | Override detected scope (e.g., `api`, `auth`, `cli`) |
| `--short` | `-s` | Print only the one-line message |
| `--commit` | | Analyze and immediately `git commit` |
| `--interactive` | `-i` | Interactive: accept, edit, override, or skip |
| `--json` | | Output full analysis as JSON |
| `--emojis` | | Include emoji prefixes (default: on) |
| `--no-emojis` | | Disable emoji prefixes |
| `--cta` | | Add tracking tail (`Reviewed-by: diffscribe`) |
| `--verbose` | `-v` | Show repo name, branch, stats before message |
| `--help` | `-h` | Show help |

## Examples

### Short message only

```bash
diffscribe -s
# => fix(auth): update 3 TypeScript files
```

### Override type

```bash
git add src/cache.py
diffscribe --type perf
# => ⚡ perf: add Python module cache.py
```

### JSON output for scripting

```bash
diffscribe --json | jq '.type'
# => "feat"
```

### Interactive mode

```bash
diffscribe -i
# Suggested commit message:
# ──────────────────────────────────────────────────
# ✨ feat(api): update 2 Python files
# ──────────────────────────────────────────────────
# Options: [a]ccept  [e]dit  [d]escribe  [s]kip
# >
```

### Full commit with verbose info

```bash
git add .
diffscribe --commit --verbose
# Branch:      main
# Repository:  myproject
# Files:       4
# Additions:   87
# Deletions:   12
# Suggested:   feat(api)
#
# ✨ feat(api): update 4 files
#
# Changes:
# - src/api/routes.py (+30/-2)
# - src/api/models.py (+22/-5)
# - src/api/auth/__init__.py (+18/-0)
# - tests/test_api.py (+17/-5)
#
# [diffscribe] 87 additions, 12 deletions across 4 file(s)
# ✅ Committed successfully!
```

## How It Works

1. **Reads your staged diff** via `git diff --cached --stat` and `git diff --cached`
2. **Parses each file** — additions, deletions, new/deleted/renamed status
3. **Classifies each file** using heuristics:
   - Path-based: `test/`, `.github/`, `Dockerfile`, `.eslintrc`, etc.
   - Content-based: regex scans of diff hunks for fix/perf/refactor patterns
4. **Aggregates** — picks the dominant commit type and most common scope
5. **Generates** a conventional commit message following the [Conventional Commits v1.0.0](https://www.conventionalcommits.org/) spec

## Requirements

- Python 3.10+
- Git 2.20+
- Works on Linux, macOS, and Windows

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-thing`)
3. Commit your changes (`git commit -m '✨ feat: add amazing thing'`)
4. Push to the branch (`git push origin feature/amazing-thing`)
5. Open a Pull Request

## License

[MIT](https://opensource.org/licenses/MIT) © 2026 IndraTensei
