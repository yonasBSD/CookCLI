# Documentation Rewrite Design

## Goal

Rewrite all command documentation in `docs/` as lean reference pages derived from actual `--help` output. Target audience: end users on the website.

## Problems with current docs

1. **Inaccurate options/flags** — `--pantry` on shopping-list (doesn't exist), missing subcommands (doctor pantry, pantry plan)
2. **Fabricated output** — JSON structures, command outputs that may not match reality
3. **Bloat** — shell scripting tutorials, deployment configs, social media templates
4. **Inconsistent structure** — each page follows a different pattern

## Approach: Lean reference

Each page follows this structure:

1. One-line description (from CLI help)
2. Usage (from `--help`)
3. Arguments (if any)
4. Options table (complete, from `--help`)
5. Subcommands (if any, with their own options)
6. Examples (2-4 real examples)
7. Notes (only genuinely useful context)

Target: 50-80 lines per page (except lsp.md which keeps all editor configs).

## Pages to rewrite

| Page | Key changes |
|------|-------------|
| README.md | Fix command list, tighten intro, fix base-path references |
| recipe.md | Remove fabricated output, batch scripts; add menu files |
| shopping-list.md | Remove fake JSON, remove `--pantry`, remove party planning |
| server.md | Remove Docker/systemd/launchd/nginx deployment configs |
| search.md | Remove inventory scripts, random selectors, fzf tutorials |
| doctor.md | Add `pantry` subcommand, remove CI template, batch fix scripts |
| import.md | Fix typo, remove TODO sections |
| seed.md | Simplify — very simple command |
| report.md | Remove social/email/LaTeX templates, keep core example |
| update.md | Simplify, remove platform table |
| lsp.md | Keep all editor configs, tighten rest |
| pantry.md | Add `plan` subcommand |

## Principles

- Every option must match `--help` exactly
- No fabricated command output
- No shell scripting tutorials
- Keep all editor integration configs in lsp.md
