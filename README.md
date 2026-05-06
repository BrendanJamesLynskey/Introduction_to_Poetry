# 🐍 Introduction to Poetry

An interactive Reveal.js presentation covering **Poetry** — the opinionated Python dependency manager and build backend. One declarative `pyproject.toml`, one hash-pinned `poetry.lock`, isolated venvs, PyPI publishing — and the Poetry 2.0 / PEP 621 era.

## ▶ [Open the Presentation](https://brendanjameslynskey.github.io/Introduction_to_Poetry/)

## 📄 [Markdown Version](presentation.md)

## 🦀 [Companion deck — Introduction to uv](https://brendanjameslynskey.github.io/Introduction_to_uv/)

---

## Contents

| # | Topic | Description |
|---|-------|-------------|
| 01 | Title | Pitch and the declare → resolve → lock → install → publish flow |
| 02 | Topics | Map of foundations, project workflow, power features, adoption |
| 03 | What Is Poetry? | Origins, status, what Poetry owns end-to-end |
| 04 | Why Poetry? | Pain points solved, design principles, honest trade-offs |
| 05 | Installation | Official installer, pipx, brew/scoop, self-update — and what to avoid |
| 06 | poetry new / init | Project scaffolds — `src/`, flat, in-place |
| 07 | pyproject.toml | Source of truth — `[project]` (PEP 621) + `[tool.poetry]` (2.0) |
| 08 | poetry add / remove | Everyday verbs and what changes on disk |
| 09 | poetry.lock | Cross-platform, hash-pinned, group-tagged lockfile |
| 10 | poetry install / sync | Default vs exact-match install; production patterns |
| 11 | poetry env | Auto-managed venvs, in-project venvs, picking interpreters |
| 12 | poetry run / shell | Execute in env; `env activate` and `poetry-plugin-shell` |
| 13 | Python versions | Pairing Poetry with pyenv / asdf / uv as the installer |
| 14 | Dependency groups | PEP 735-style groups for dev / docs / profile tooling |
| 15 | Optional extras | PEP 621 `[project.optional-dependencies]` for end-users |
| 16 | Source constraints | Private indexes, priorities (primary / supplemental / explicit) |
| 17 | Build & publish | `poetry build`, `poetry publish`, trusted publishing via OIDC |
| 18 | Plugins | export, bundle, dynamic-versioning, poethepoet, mono-repo-deps |
| 19 | Configuration | `poetry config`, env vars, project `poetry.toml`, resolution order |
| 20 | Performance & internals | Mixology resolver, caches, parallel installs, slow spots |
| 21 | Poetry vs pip / pipenv / pdm / Hatch / uv | Capability matrix and verdicts |
| 22 | Migrating from pip | Three-stage path from `requirements.txt` to `poetry.lock` |
| 23 | Migrating to 2.0 | `[tool.poetry]` → `[project]` PEP 621 rewrite |
| 24 | CI patterns | GitHub Actions with `snok/install-poetry`, lock-drift gate |
| 25 | Docker patterns | Multi-stage builds, `VIRTUALENVS_CREATE=false`, layer caching |
| 26 | Cheat sheet | Daily commands, two-column reference |
| 27 | Gotchas | Caret semantics, solver errors, slow locks, private indexes |
| 28 | Summary | Takeaways, next steps, further reading |

---

## Slide Controls

| Action | Key |
|--------|-----|
| Next / Previous | `→` `←` or swipe |
| Overview | `Esc` |
| Fullscreen | `F` |
| Export to PDF | Append `?print-pdf` to URL, then print |

## Technology

[Reveal.js 4.6](https://revealjs.com) · [highlight.js](https://highlightjs.org) · Playfair Display + DM Sans + JetBrains Mono

Single self-contained `index.html` — no build step, no npm, no dependencies to install.

## References

Poetry documentation — python-poetry.org · Poetry source — github.com/python-poetry/poetry · poetry-core — github.com/python-poetry/poetry-core · PEP 517 — peps.python.org/pep-0517 · PEP 518 — peps.python.org/pep-0518 · PEP 621 — peps.python.org/pep-0621 · PEP 735 — peps.python.org/pep-0735 · install-poetry action — github.com/snok/install-poetry

## License

Educational use. Code examples provided as-is.
