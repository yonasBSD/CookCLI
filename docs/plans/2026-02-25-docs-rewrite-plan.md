# Documentation Rewrite Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Rewrite all 12 docs pages as lean reference docs that accurately reflect `--help` output.

**Architecture:** Each page follows: description → usage → arguments → options table → subcommands → examples → notes. All options/flags verified against actual CLI help. No fabricated output.

**Tech Stack:** Markdown

---

### Task 1: Rewrite docs/README.md

**Files:**
- Modify: `docs/README.md`

**Step 1: Rewrite the file**

Replace full contents with lean version:
- One-line intro
- Command table with aliases (recipe/r, server/s, shopping-list/sl, seed, search/f, import/i, report/rp, doctor, pantry/p, lsp, update/u)
- Installation (Homebrew + source)
- Global options (`-v`, `-h`, `-V`)
- Quick start (4 commands: seed, recipe, shopping-list, server)
- Link to Cooklang spec
- Remove: Philosophy section, Recipe Files section, Configuration section, Tips and Tricks, Combining with UNIX Tools

**Step 2: Verify all commands listed match `cook --help`**

Run: `cargo run -- --help 2>/dev/null`
Cross-check every command name and alias.

**Step 3: Commit**

```bash
git add docs/README.md
git commit -m "docs: rewrite README.md as lean reference"
```

---

### Task 2: Rewrite docs/recipe.md

**Files:**
- Modify: `docs/recipe.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Parse and display Cooklang recipe files."
- Usage: `cook recipe [OPTIONS] [RECIPE]`
- Arguments table: `[RECIPE]` with description from help (supports name, path, scaling `:N`, stdin)
- Options table from `cook recipe --help`: `-f/--format`, `-s/--scale`, `-o/--output`, `--pretty`
- Subcommands: `read` (note: `cook recipe` defaults to read behavior)
- Examples (4): basic view, scaling, JSON export, stdin pipe
- Notes: `.cook` extension optional, `.menu` files supported, format inferred from `-o` extension

Remove: fabricated output blocks, batch processing scripts, recipe comparison scripts, "Common Issues" section

**Step 2: Verify options match help**

Run: `cargo run -- recipe --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/recipe.md
git commit -m "docs: rewrite recipe.md as lean reference"
```

---

### Task 3: Rewrite docs/shopping-list.md

**Files:**
- Modify: `docs/shopping-list.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Generate a combined shopping list from multiple recipes."
- Usage: `cook shopping-list [OPTIONS] [RECIPES]...`
- Arguments: `[RECIPES]...` with scaling syntax
- Options table from help: `-b/--base-path`, `-o/--output`, `-p/--plain`, `-f/--format`, `--pretty`, `-a/--aisle`, `-i/--ignore-references`, `--ingredients-only`
- Examples (4): basic multi-recipe, scaling, JSON output, plain list
- Notes: ingredients auto-combined, aisle.conf for categorization, glob patterns supported

Remove: fabricated JSON output, `--pantry` flag (doesn't exist on this command), party planning scripts, weekly meal planning scripts, pantry configuration section (belongs in pantry.md), recipe references section

**Step 2: Verify options match help**

Run: `cargo run -- shopping-list --help 2>/dev/null`
Confirm `--pantry` does NOT exist on this command.

**Step 3: Commit**

```bash
git add docs/shopping-list.md
git commit -m "docs: rewrite shopping-list.md as lean reference"
```

---

### Task 4: Rewrite docs/server.md

**Files:**
- Modify: `docs/server.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Start a local web server to browse your recipe collection."
- Keep the 4 screenshot images at top
- Usage: `cook server [OPTIONS] [BASE_PATH]`
- Arguments: `[BASE_PATH]` — root directory for recipes
- Options table: `--host [<ADDRESS>]`, `-p/--port` (default 9080), `--open`
- Examples (4): basic, custom port, external access, auto-open browser
- Notes: localhost-only by default, `--host` for network access

Remove: systemd service config, launchd plist config, Docker deployment, nginx reverse proxy, Web Interface Guide, Family Sharing, Kitchen Setup, Tablet Mode, Development Workflow, fswatch, Troubleshooting, Security Considerations, shell aliases

**Step 2: Verify options match help**

Run: `cargo run -- server --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/server.md
git commit -m "docs: rewrite server.md as lean reference"
```

---

### Task 5: Rewrite docs/search.md

**Files:**
- Modify: `docs/search.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Search through your recipe collection for matching text."
- Usage: `cook search [OPTIONS] <TERMS>...`
- Arguments: `<TERMS>...` — one or more search terms (AND logic)
- Options table: `-b/--base-dir`
- Examples (3): single term, multiple terms, specific directory
- Notes: searches titles, ingredients, instructions, metadata; case-insensitive; results ranked by relevance

Remove: phrase search (verify if actually supported), dietary restrictions examples, cooking method examples, cuisine examples, fzf/dmenu/rofi integration, inventory-based cooking, random meal selector, recipe discovery, export search results, menu planning scripts, complex queries with comm/grep, Troubleshooting, Optimizing Search

**Step 2: Verify options match help**

Run: `cargo run -- search --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/search.md
git commit -m "docs: rewrite search.md as lean reference"
```

---

### Task 6: Rewrite docs/doctor.md

**Files:**
- Modify: `docs/doctor.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Analyze your recipe collection for issues and improvements."
- Usage: `cook doctor [OPTIONS] [COMMAND]`
- Subcommands section with three subsections:
  - `validate` with options: `-b/--base-path`, `--strict`
  - `aisle` with options: `-b/--base-path`
  - `pantry` with options: `-b/--base-path` (THIS IS MISSING FROM CURRENT DOCS)
- Examples (4): validate all, strict mode, check aisles, check pantry
- Notes: `--strict` exits with code 1 for CI/CD use

Remove: Common Issues and Fixes section (syntax examples), CI/CD GitHub Actions template, batch fix scripts with sed/find, Aisle Configuration Management, Multi-Store Configuration

**Step 2: Verify subcommands match help**

Run: `cargo run -- doctor --help 2>/dev/null`
Run: `cargo run -- doctor validate --help 2>/dev/null`
Run: `cargo run -- doctor aisle --help 2>/dev/null`
Run: `cargo run -- doctor pantry --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/doctor.md
git commit -m "docs: rewrite doctor.md as lean reference, add pantry subcommand"
```

---

### Task 7: Rewrite docs/import.md

**Files:**
- Modify: `docs/import.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Import recipes from websites and convert to Cooklang format."
- Note about `OPENAI_API_KEY` requirement upfront
- Usage: `cook import [OPTIONS] <URL>`
- Arguments: `<URL>`
- Options table: `-s/--skip-conversion`, `--metadata` (frontmatter/json/yaml/none), `--metadata-only`
- Examples (4): basic import, save to file, skip conversion, metadata only
- Notes: requires OPENAI_API_KEY for conversion, works with Schema.org markup sites, link to cook.md converter alternative

Remove: Supported Websites list (can't verify), TODO sections (paywalled sites, anti-scraping), Working with Different Sites, Best Practices, fabricated output blocks

Fix: "downlad" typo

**Step 2: Verify options match help**

Run: `cargo run -- import --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/import.md
git commit -m "docs: rewrite import.md as lean reference"
```

---

### Task 8: Rewrite docs/seed.md

**Files:**
- Modify: `docs/seed.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Initialize a directory with example Cooklang recipes."
- Usage: `cook seed [OPTIONS] [DIR]`
- Arguments: `[DIR]` — target directory (default: current, created if needed)
- Examples (2): current directory, specific directory
- Notes: creates example recipes, folder structure, and aisle.conf

Remove: directory tree listing (may not match current seed content), example recipe cooklang block, advanced features list, learning from examples, practical uses, common patterns, "Try Before You Buy"

**Step 2: Verify options match help**

Run: `cargo run -- seed --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/seed.md
git commit -m "docs: rewrite seed.md as lean reference"
```

---

### Task 9: Rewrite docs/report.md

**Files:**
- Modify: `docs/report.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Generate custom reports from recipes using templates."
- Prototype note
- Usage: `cook report [OPTIONS] --template <TEMPLATE> <RECIPE>`
- Arguments: `<RECIPE>` with scaling syntax
- Options table: `-t/--template`, `-d/--datastore`, `-a/--aisle`, `-p/--pantry`, `-b/--base-path`
- Template variables section (keep but tighten — metadata, ingredients, steps)
- One example template (simple recipe card)
- Link to cooklang-reports repo for more templates
- Examples (3): basic report, with scaling, with all configs

Remove: conditional content templates, formatted output templates, calculations, meal planning template, recipe book LaTeX template, index generation, reusable components/macros, PDF generation, email newsletter, social media template, all the speculative Jinja2 examples

**Step 2: Verify options match help**

Run: `cargo run -- report --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/report.md
git commit -m "docs: rewrite report.md as lean reference"
```

---

### Task 10: Rewrite docs/update.md

**Files:**
- Modify: `docs/update.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Update CookCLI to the latest version."
- Usage: `cook update [OPTIONS]`
- Options table: `--check-only`, `--force`
- Alias: `cook u`
- Examples (3): update, check only, force
- Notes: downloads from GitHub releases over HTTPS, may need sudo for system installs

Remove: platform support table, fabricated example output blocks, permissions section details, version checking in doctor section, manual installation steps, security section, "Regular Update Workflow" verbose example

**Step 2: Verify options match help**

Run: `cargo run -- update --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/update.md
git commit -m "docs: rewrite update.md as lean reference"
```

---

### Task 11: Rewrite docs/lsp.md

**Files:**
- Modify: `docs/lsp.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Start the Language Server Protocol server for Cooklang editor integration."
- Usage: `cook lsp [OPTIONS]`
- Options: (only `-v` and `-h` — very simple)
- Features list (keep but as bullet list): diagnostics, completions, hover, document symbols, semantic tokens, go to definition
- Editor Integration section — keep ALL editors (VS Code, Neovim, Vim/CoC, Emacs lsp-mode, Emacs eglot, Helix, Sublime Text, Zed) with their config blocks
- Troubleshooting (keep, it's useful): verify PATH, check logs, debug logging

Remove: Protocol Details table, Transport section, completion trigger details, hover information details, document symbols details, semantic tokens details

**Step 2: Verify options match help**

Run: `cargo run -- lsp --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/lsp.md
git commit -m "docs: rewrite lsp.md as lean reference"
```

---

### Task 12: Rewrite docs/pantry.md

**Files:**
- Modify: `docs/pantry.md`

**Step 1: Rewrite the file**

Structure:
- Description: "Manage and analyze your pantry inventory."
- Usage: `cook pantry [OPTIONS] <COMMAND>`
- Options table: `-b/--base-path`, `-f/--format` (human/json/yaml)
- Subcommands with their own options:
  - `depleted` (alias `d`): `--all`
  - `expiring` (alias `e`): `-d/--days` (default 7), `--include-unknown`
  - `recipes` (alias `r`): `-p/--partial`, `--threshold` (default 75)
  - `plan`: `-n/--max-ingredients`, `-s/--skip` (default 0), `-m/--allow-missing` (default 0) — THIS IS MISSING FROM CURRENT DOCS
- Configuration format section (keep, essential): pantry.conf TOML format with item attributes (quantity, low, bought, expire)
- Examples (4): one per subcommand
- Notes: units must match for comparison, config search path

Remove: fabricated JSON/YAML output examples, Integration with Other Commands, Tips section, verbose output format examples

**Step 2: Verify subcommands match help**

Run: `cargo run -- pantry --help 2>/dev/null`
Run: `cargo run -- pantry plan --help 2>/dev/null`
Run: `cargo run -- pantry depleted --help 2>/dev/null`
Run: `cargo run -- pantry expiring --help 2>/dev/null`
Run: `cargo run -- pantry recipes --help 2>/dev/null`

**Step 3: Commit**

```bash
git add docs/pantry.md
git commit -m "docs: rewrite pantry.md as lean reference, add plan subcommand"
```

---

### Task 13: Final review

**Step 1: Check all docs for consistency**

Verify every page follows the same structure: description, usage, arguments, options, examples, notes.

**Step 2: Check cross-references**

Verify all links between docs pages work (e.g., README.md links to individual pages).

**Step 3: Final commit if any fixes needed**

```bash
git add docs/
git commit -m "docs: fix cross-references and consistency"
```
