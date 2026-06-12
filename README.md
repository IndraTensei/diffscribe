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
- 🌿 **Branch-aware detection** — reads type/scope from branch names like `feature/api-auth` or `fix/memory-leak`
- 📋 **Clipboard support** — `--copy` copies the suggested message to your system clipboard
- ✏️ **Amend support** — `--amend` amends the last commit with the generated message
- ⚙️ **Config file** — persistent settings via `.diffscribe.toml` or `.diffscribe.json`
- 🛠️ **Git hook integration** — `--hook` installs a `prepare-commit-msg` hook for automatic commit messages
- 🔍 **Dry-run mode** — `--dry-run` previews the commit message without actually committing
- 📊 **ASCII diff bar** — `--verbose` now shows an ASCII visualization of additions vs deletions
- 🏷️ **Type breakdown** — `--verbose` shows a per-type count (e.g. `feat:2, docs:1`)
- ✍️ **Sign-off support** — `--signoff` adds a rich body with type-grouped files and a `Signed-off-by` line
- 🎨 **Custom templates** — `--template` with placeholders like `{type}`, `{scope}`, `{subject}`, `{hash}`, `{date}`
- 📜 **Commit history** — `--history` shows recent commits for style consistency
- 🔍 **Commit linter** — `--lint` validates any message against the Conventional Commits v1.0.0 spec

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
| `--amend` | | Analyze and `git commit --amend` the last commit |
| `--copy` | | Copy the generated message to clipboard |
| `--interactive` | `-i` | Interactive: accept, edit, override, or skip |
| `--json` | | Output full analysis as JSON |
| `--emojis` | | Include emoji prefixes (default: on; configurable via `.diffscribe.toml`) |
| `--no-emojis` | | Disable emoji prefixes |
| `--cta` | | Add tracking tail (`Reviewed-by: diffscribe`) |
| `--verbose` | `-v` | Show repo name, branch, stats, ASCII diff bar, type breakdown, and branch hints |
| `--hook` | | Install diffscribe as a `prepare-commit-msg` git hook |
| `--force` | | Force overwrite when used with `--hook` |
| `--dry-run` | | Preview the commit message without actually committing |
| `--signoff` | | Add a rich body with type-grouped files and `Signed-off-by` line|
| `--template T` | `-T` | Custom output template (placeholders: `{emoji}` `{type}` `{scope}` `{subject}` `{files}` `{additions}` `{deletions}` `{branch}` `{repo}` `{hash}` `{date}`)|
| `--history` | | Show recent commit history for style reference|
| `--lint [MSG]` | | Lint a commit message against Conventional Commits spec (reads stdin if no argument, or pass a message/file path)|
| `--help` | `-h` | Show help|
| `--version` | | Show version number|

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

### Copy to clipboard

```bash
git add src/api/routes.py
diffscribe --copy
# 📋 Copied to clipboard: ✨ feat(api): add Python module routes.py
```

### Amend the last commit

```bash
git add src/auth/patch.py
diffscribe --amend --verbose
# Branch:      fix/auth-bug
# Repository:  myproject
# Files:       1
# Additions:   3
# Deletions:   1
# Branch hint: type=fix, scope=auth-bug
# Suggested:   fix(auth-bug)
#
# 🐛 fix(auth-bug): update patch.py
#
# Changes:
# - src/auth/patch.py (+3/-1)
#
# [diffscribe] 3 additions, 1 deletions across 1 file(s)
# ✅ Amended last commit successfully!
```

### Custom templates

```bash
git add src/api/routes.py
diffscribe -T "{type}{scope}: {subject} [{hash}]"
# => feat(api): add Python module routes.py [a1b2c3d4]

diffscribe -T "{date} {type}{scope}: {subject} (+{additions}/-{deletions})"
# => 2026-06-12 feat(api): add Python module routes.py (+42/-0)

diffscribe --no-emojis -T "{type}{scope}: {subject} ({branch})"
# => feat(api): add Python module routes.py (feature/api-auth)
```

### Commit history

```bash
diffscribe --history
# 📜 Recent 10 commit(s) for style reference:
#
#   a1b2c3d4  3 days ago   feat(api): add user authentication
#   e5f6g7h8  5 days ago   fix(auth): resolve token expiry bug
#   ...
```

### Lint a commit message

```bash
# Lint a message directly
diffscribe --lint "feat(api): add user authentication"
# ✅ Commit message looks good!
#    type=feat, scope=api, subject="add user authentication"

# Lint from stdin
echo "WIP: working on stuff" | diffscribe --lint
# ❌ Commit message does NOT conform to Conventional Commits:
#    ✗ Header does not match Conventional Commits format: 'WIP: working on stuff'
#    ✗ Expected format: <type>[(scope)][!]: <subject>
#    ✗ Allowed types: build, chore, ci, docs, feat, fix, perf, refactor, revert, style, test
#    ⚠ Starts with 'wip' — conventional commits should describe the result, not work-in-progress

# Lint a file
echo "fix: resolve memory leak" > /tmp/commit-msg.txt
diffscribe --lint /tmp/commit-msg.txt
# ✅ Commit message looks good!
#    type=fix, subject="resolve memory leak"
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
4. **Checks branch name** — if the branch follows conventions like `feature/add-login` or `fix/memory-leak`, the type and scope are extracted
5. **Loads config** — reads `.diffscribe.toml` or `.diffscribe.json` from the repo (or any parent directory) for default settings
6. **Aggregates** — picks the dominant commit type and most common scope
7. **Generates** a conventional commit message following the [Conventional Commits v1.0.0](https://www.conventionalcommits.org/) spec

## Config File

Create a `.diffscribe.toml` (or `.diffscribe.json`) in your repo root to set default options:

### TOML format

```toml
# .diffscribe.toml
[diffscribe]
emojis = true
verbose = false
cta = false
scope = "my-project"
type = ""
```

### JSON format

```json
{
  "diffscribe": {
    "emojis": true,
    "verbose": false,
    "cta": false,
    "scope": "my-project",
    "type": ""
  }
}
```

Config file settings are defaults — CLI flags always take precedence. The tool searches for config files starting from the current directory and walks up parent directories.

### Supported config keys

| Key | Type | Description |
|-----|------|-------------|
| `emojis` | bool | Whether to include emoji prefixes (default: `true`) |
| `verbose` | bool | Whether to show detailed branch/stats info (default: `false`) |
| `cta` | bool | Whether to add `Reviewed-by: diffscribe` tail (default: `false`) |
| `scope` | string | Default scope override (empty = auto-detect) |
| `type` | string | Default type override (empty = auto-detect) |

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
