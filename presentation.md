# Introduction to Poetry

**The opinionated Python dependency manager & build backend**

*One tool, one config file, one lockfile — declarative dependency management with reproducible installs, isolated venvs, and PyPI publishing built in.*

```
Python -> env -> lock -> install -> run -> publish
```

Declare  |  Resolve  |  Lock  |  Install  |  Publish

---

## Table of Contents

1. [Topics](#slide-01--topics)
2. [What Is Poetry?](#slide-02--what-is-poetry)
3. [Why Poetry? — Opinions That Pay Off](#slide-03--why-poetry--opinions-that-pay-off)
4. [Installation](#slide-04--installation)
5. [poetry new & poetry init — Starting a Project](#slide-05--poetry-new--poetry-init--starting-a-project)
6. [pyproject.toml — Source of Truth (Poetry 2.0)](#slide-06--pyprojecttoml--source-of-truth-poetry-20)
7. [poetry add / poetry remove](#slide-07--poetry-add--poetry-remove)
8. [The Lockfile — poetry.lock](#slide-08--the-lockfile--poetrylock)
9. [poetry install & poetry sync](#slide-09--poetry-install--poetry-sync)
10. [Virtual Environments — poetry env](#slide-10--virtual-environments--poetry-env)
11. [poetry run & poetry shell](#slide-11--poetry-run--poetry-shell)
12. [Managing Python Versions](#slide-12--managing-python-versions)
13. [Dependency Groups (Poetry 1.2+)](#slide-13--dependency-groups-poetry-12)
14. [Optional Extras (PEP 621)](#slide-14--optional-extras-pep-621)
15. [Source Constraints & Private Indexes](#slide-15--source-constraints--private-indexes)
16. [Build & Publish](#slide-16--build--publish)
17. [Plugins — Extending Poetry](#slide-17--plugins--extending-poetry)
18. [Configuration — poetry config & poetry.toml](#slide-18--configuration--poetry-config--poetrytoml)
19. [Performance & Internals](#slide-19--performance--internals)
20. [Poetry vs pip / pipenv / pdm / Hatch / uv](#slide-20--poetry-vs-pip--pipenv--pdm--hatch--uv)
21. [Migrating from pip / requirements.txt](#slide-21--migrating-from-pip--requirementstxt)
22. [Poetry 1.x → 2.0 — Migrating to PEP 621](#slide-22--poetry-1x--20--migrating-to-pep-621)
23. [CI Patterns — GitHub Actions](#slide-23--ci-patterns--github-actions)
24. [Docker Patterns](#slide-24--docker-patterns)
25. [Cheat Sheet — Daily Commands](#slide-25--cheat-sheet--daily-commands)
26. [Gotchas & Troubleshooting](#slide-26--gotchas--troubleshooting)
27. [Summary & Next Steps](#slide-27--summary--next-steps)

---

## Slide 01 — Topics

### Foundations

- What Poetry is — origins, scope, design philosophy
- Installation — official installer, pipx, package managers
- Starting a project with `poetry new` & `poetry init`
- `pyproject.toml` in the Poetry 2.0 / PEP 621 era

### Project workflow

- `poetry add` / `poetry remove`
- `poetry.lock` — deterministic dependency graph
- `poetry install` & `poetry sync`
- Virtual environments — `poetry env`
- `poetry run` & `poetry shell`

### Power features

- Managing Python versions with pyenv / asdf
- Dependency groups (dev, docs, test)
- Optional extras — opt-in features for users
- Private indexes & mirror sources
- Build & publish to PyPI with `poetry-core`
- Plugins — export, bundle, dynamic versioning

### Adoption & performance

- The Mixology resolver & parallel installs
- pip / pipenv / pdm / Hatch / uv comparison
- Migration from `requirements.txt`
- Poetry 1.x → 2.0 migration to PEP 621
- CI / Docker patterns and gotchas

---

## Slide 02 — What Is Poetry?

**Poetry** is a Python tool for declarative dependency management, isolated environments, deterministic builds, and PyPI publishing — all driven by a single `pyproject.toml` and a checked-in `poetry.lock`.

### Origin & status

- Created by **Sébastien Eustace** — first release Feb 2018
- 1.0 in Dec 2019; 1.2 introduced dependency groups (Aug 2022)
- **2.0 (Jan 2025)** — first-class PEP 621 support, `poetry sync`, pluggable shell
- MIT licensed, written in Python, hosted at `python-poetry/poetry`
- Community-maintained; roadmap and discussions on GitHub Discussions

### What Poetry owns

- **Dependency declaration** — runtime, dev groups, extras
- **Resolution & locking** — Mixology resolver → `poetry.lock`
- **Virtual environments** — auto-created & managed per-project
- **Builds** — sdist + wheel via the `poetry-core` backend
- **Publishing** — `poetry publish` straight to PyPI
- **Plugin system** — extend without forking

The pitch: *"npm/yarn for Python"* — one declarative manifest, one lockfile, one CLI verb per task. No `requirements.txt` juggling, no `setup.py` archaeology.

---

## Slide 03 — Why Poetry? — Opinions That Pay Off

### Problems Poetry solves

| Pain | Poetry's answer |
|---|---|
| `requirements.txt` drift | Single declarative `pyproject.toml` |
| Non-reproducible installs | Hash-pinned `poetry.lock` |
| Manual venv juggling | Auto-managed per-project venv |
| Bespoke `setup.py` | Standards-based `poetry-core` backend |
| Publishing ceremony | One `poetry publish` |
| Ad-hoc dev/test deps | First-class dependency groups |

### Design principles

- **Declarative over imperative** — say what, not how
- **Lock by default** — every install is reproducible
- **Standards-track** — PEP 517/518/621/735 are the source of truth
- **Isolation** — every project gets its own venv automatically
- **One tool, one config** — no pip + pip-tools + virtualenv + twine + bumpversion

### Trade-offs (be honest)

- **Slower resolver** than uv / pdm — large graphs cost real seconds
- **No managed Python** — pair with pyenv/asdf for interpreter switching
- Older projects pre-2.0 used `[tool.poetry]` — migrate to `[project]` when you can

---

## Slide 04 — Installation

Poetry installs into its **own isolated venv** — never alongside your project deps. Three good paths, one to avoid.

### Official installer (recommended)

```bash
# macOS / Linux
curl -sSL https://install.python-poetry.org | python3 -

# Windows (PowerShell)
(Invoke-WebRequest -Uri https://install.python-poetry.org `
  -UseBasicParsing).Content | py -

# pin a version
curl -sSL https://install.python-poetry.org | python3 - --version 2.0.1
```

### pipx (also great)

```bash
pipx install poetry
pipx install poetry==2.0.1
pipx upgrade poetry
pipx inject poetry poetry-plugin-export
```

### Package managers

```bash
brew install poetry          # macOS
scoop install poetry         # Windows
pacman -S python-poetry      # Arch

# Linux distro packages exist but tend to lag — prefer pipx
```

### Self-update

```bash
poetry self update
poetry self update 2.0.1
poetry self update --preview
poetry --version
```

Plugins are managed the same way: `poetry self add poetry-plugin-export`.

### First sanity check

```text
$ poetry --version
Poetry (version 2.0.1)

$ poetry config --list
cache-dir = "/home/me/.cache/pypoetry"
virtualenvs.create = true
virtualenvs.in-project = false
virtualenvs.path = "{cache-dir}/virtualenvs"
```

> **Don't `pip install poetry`** — installing Poetry into a project venv mixes its deps with yours and breaks the moment you uninstall something. Use **pipx** or the **official installer**.

---

## Slide 05 — poetry new & poetry init — Starting a Project

### From scratch

```text
$ poetry new my-app
Created package my_app in my-app

$ tree -a my-app
my-app/
├── pyproject.toml
├── README.md
├── src/
│   └── my_app/
│       └── __init__.py
└── tests/
    └── __init__.py
```

```bash
# flat layout (no src/)
poetry new --flat my-app

# library only (skip the tests dir)
poetry new --readme=md my-app
```

### In-place on an existing repo

```text
$ cd existing-repo
$ poetry init
Package name [existing-repo]:
Version [0.1.0]:
Description []:
Author [Brendan <you@x.com>, n to skip]:
Compatible Python versions [^3.12]:
Would you like to define your dependencies interactively? (yes/no) [yes]: no
Would you like to define your development dependencies interactively? (yes/no) [yes]: no
```

### What you get out of the box

- **src/ layout** by default — keeps your tests honest
- **PEP 621 `[project]`** table (Poetry 2.0+)
- **poetry-core** as the build-system
- **README.md** + a tests scaffold

### First commands

```bash
cd my-app
poetry add requests
poetry install
poetry run python -c "import my_app; print(my_app)"
```

> **Tip — check Python in.** Add a `.python-version` file (pyenv / asdf) and pin `requires-python` in `pyproject.toml`. Two pinpoints, one reproducible env.

---

## Slide 06 — pyproject.toml — Source of Truth (Poetry 2.0)

### Anatomy of a Poetry 2.0 project

```toml
[project]
name = "my-app"
version = "0.1.0"
description = "Example service"
readme = "README.md"
requires-python = ">=3.11,<3.14"
authors = [
  { name = "Brendan Lynskey", email = "you@example.com" }
]
dependencies = [
  "fastapi (>=0.115,<1)",
  "httpx (>=0.27,<1)",
  "pydantic (>=2.8,<3)",
]

[project.optional-dependencies]
postgres = ["asyncpg (>=0.29)"]

[tool.poetry]
package-mode = true

[tool.poetry.group.dev.dependencies]
pytest = "^8"
ruff   = "^0.6"
mypy   = "^1.10"

[build-system]
requires      = ["poetry-core>=2.0"]
build-backend = "poetry.core.masonry.api"
```

### `[project]` vs `[tool.poetry]`

- **[project]** — PEP 621 standard metadata. Read by uv, pdm, Hatch, build, etc. Use this for everything portable.
- **[tool.poetry]** — Poetry-specific: package mode, custom URLs, extras-of-extras, source priorities, group *dev* deps in caret syntax.
- Pre-2.0 projects had *everything* under `[tool.poetry]`. Migrate when convenient.

### Version specifiers — quick reference

| Spec | Means |
|---|---|
| `^1.2.3` | ≥ 1.2.3, < 2.0.0 (caret — Poetry default) |
| `~1.2.3` | ≥ 1.2.3, < 1.3.0 (tilde) |
| `==1.2.3` | exactly |
| `>=1.2,<2` | explicit range |
| `*` | any |
| `1.2.*` | wildcard |

> **Caret is not SemVer** — Poetry's `^0.2.3` means `>=0.2.3,<0.3.0`. For pre-1.0 packages the upper bound is the next minor, not the next major. PEP 440 / PyPI doesn't define caret; it's a Poetry convention.

---

## Slide 07 — poetry add / poetry remove

The everyday verbs. Each call edits `pyproject.toml`, refreshes `poetry.lock`, and updates the venv — atomically.

### Adding

```bash
# runtime deps (caret bound by default)
poetry add fastapi httpx

# explicit version
poetry add 'pydantic@^2.8'
poetry add 'pydantic>=2.8,<3'

# dev-group dependency
poetry add --group dev pytest ruff mypy

# extras
poetry add 'fastapi[standard]'

# optional dep that becomes an extra
poetry add asyncpg --optional

# from a local path / git / URL
poetry add ./libs/internal-utils
poetry add 'git+https://github.com/foo/bar.git#main'
poetry add 'https://files.pythonhosted.org/.../wheel.whl'

# editable
poetry add --editable ./libs/internal-utils

# dry-run (don't write files)
poetry add fastapi --dry-run
```

### Removing & updating

```bash
# remove
poetry remove httpx
poetry remove --group dev pytest

# update — re-resolve within constraints
poetry update                # everything
poetry update fastapi        # one package
poetry update --with dev     # include groups

# refresh the lock without re-installing
poetry lock                  # 2.0+: refreshes lock
poetry lock --no-update      # 1.x equivalent of "lock to current"

# show what would change
poetry update --dry-run
```

### What changes on disk

- `pyproject.toml` — adds the dependency line in the right table
- `poetry.lock` — re-resolves; the diff is your audit trail
- `.venv` (or cached venv) — packages installed/upgraded

> **Tip** — always commit `pyproject.toml` and `poetry.lock` together. Reviewers can see both intent and effect in one diff.

---

## Slide 08 — The Lockfile — poetry.lock

`poetry.lock` records the **exact resolved version** of every transitive dependency, with hashes, for cross-platform install. Cross-platform, but resolved per Python version range.

### Excerpt

```toml
[[package]]
name = "fastapi"
version = "0.115.4"
description = "..."
optional = false
python-versions = ">=3.8"
groups = ["main"]
files = [
  { file = "fastapi-0.115.4-py3-none-any.whl",
    hash = "sha256:abc..." },
  { file = "fastapi-0.115.4.tar.gz",
    hash = "sha256:def..." },
]

[package.dependencies]
pydantic   = ">=1.7.4,!=1.8,<3.0.0"
starlette  = ">=0.40.0,<0.42.0"
typing-extensions = ">=4.8.0"

[metadata]
lock-version = "2.1"
python-versions = ">=3.11,<3.14"
content-hash = "..."
```

### What the lock guarantees

- Same resolved versions on every developer machine, every CI run
- SHA256 hashes for supply-chain integrity (verified on install)
- Cross-platform: one lock for Linux, macOS, Windows
- Per-group membership (`main`, `dev`, `docs`) recorded per package
- `content-hash` ties the lock to the `pyproject.toml` that produced it

### Reproducibility flags

```bash
# refuse to update the lock — fail if it's out of date
poetry install --no-update     # 1.x
poetry install                 # 2.0 default if lock matches

# 2.0+: hard fail if pyproject changed
poetry check --lock

# regenerate lock without changing versions
poetry lock --no-update        # 1.x
poetry lock                    # 2.0 — refreshes hashes only
```

### In CI

Always run **`poetry check --lock`** at the top of CI. If the lock is stale or hash-mismatched, the build fails immediately — the way it should.

---

## Slide 09 — poetry install & poetry sync

### What install does

1. Read `pyproject.toml` + `poetry.lock`
2. Verify the `content-hash` matches
3. Ensure a venv exists with a compatible Python
4. Install every package at its locked version + hash
5. Install the project itself in editable mode (if `package-mode = true`)

Default behaviour is **additive** — packages already in the venv that aren't in the lock are *not* removed.

### poetry sync (Poetry 2.0)

```bash
# exact-match install: removes anything in the venv that isn't in the lock
poetry sync

# pre-2.0 equivalent
poetry install --sync
```

Use `sync` for production builds. Use `install` for daily dev where extra cached packages don't hurt.

### Common flag combinations

```bash
poetry install                       # main + default groups
poetry install --with dev,docs       # extra groups
poetry install --without dev         # production install
poetry install --only main           # nothing but runtime
poetry install --extras "postgres"   # one extra
poetry install --all-extras          # every extra
poetry install --no-root             # don't install the project itself
poetry sync                          # exact match (2.0)
poetry install --compile             # pre-compile .pyc
```

### Production install (Docker stage)

```dockerfile
RUN poetry config virtualenvs.create false \
 && poetry install --only main --no-root --no-interaction
# or, in 2.0:
RUN poetry config virtualenvs.create false \
 && poetry sync --only main --no-root
```

- `--only main` drops dev groups
- `--no-root` when copying source separately
- `virtualenvs.create false` installs into the system Python in the container

---

## Slide 10 — Virtual Environments — poetry env

### Auto-managed venvs

```text
# default location (cached, content-hashed)
~/.cache/pypoetry/virtualenvs/
    my-app-aB3-py3.12/
```

```bash
# inspect
poetry env info
poetry env info --path     # just the path
poetry env list            # all venvs Poetry knows about
poetry env list --full-path

# remove
poetry env remove python3.12
poetry env remove --all
```

### In-project venv (recommended)

```bash
# once, project-locally
poetry config virtualenvs.in-project true --local

# now Poetry creates .venv in the repo
poetry install
ls .venv/

# IDEs (VS Code, PyCharm) auto-detect .venv
```

### Pick a Python interpreter

```bash
# point Poetry at a specific Python
poetry env use python3.12
poetry env use /usr/bin/python3.13
poetry env use $(pyenv which python)

# show which is currently active
poetry env info --executable
```

Poetry doesn't *install* Python — it picks an existing one. Pair with pyenv / asdf / rye for switching.

### Useful config keys

```bash
poetry config virtualenvs.in-project true
poetry config virtualenvs.path "$HOME/.venvs"
poetry config virtualenvs.prefer-active-python true
poetry config virtualenvs.options.no-pip true
poetry config virtualenvs.options.system-site-packages false
```

> **Common pitfall** — if `virtualenvs.create=false` and Poetry can't find a venv, it'll install into the host Python. Fine in Docker, dangerous on a dev laptop.

---

## Slide 11 — poetry run & poetry shell

`poetry run` exec's a command in the project venv without activating it. **No `source .venv/bin/activate` required**.

### Everyday usage

```bash
poetry run python main.py
poetry run pytest -q
poetry run ruff check .
poetry run mypy src/
poetry run ipython
poetry run python -c 'import torch; print(torch.__version__)'

# scripts defined in pyproject
poetry run serve
```

### Console scripts

Define entry points in `pyproject.toml` and call them by name.

```toml
[project.scripts]
serve = "myapp.cli:main"

# or the legacy [tool.poetry.scripts]
[tool.poetry.scripts]
serve = "myapp.cli:main"
```

```bash
poetry run serve --port 8080
```

### Activating the shell

```bash
# Poetry 2.0: use the env-activate command
poetry env activate
# prints a shell command to evaluate, e.g.
#   source /path/to/.venv/bin/activate

# one-liner
eval $(poetry env activate)

# or just source the file directly
source $(poetry env info --path)/bin/activate
```

### `poetry shell` — moved to a plugin

The classic `poetry shell` command was removed from core in 2.0. Reinstate it via the official plugin:

```bash
poetry self add poetry-plugin-shell
poetry shell
```

> **Pitfall** — `poetry run python` ≠ a previously-activated Python. If you've manually `source`'d the venv, drop the `poetry run` prefix to avoid double activation.

---

## Slide 12 — Managing Python Versions

Poetry **picks** a Python interpreter; it does not *install* one. Pair Poetry with a Python version manager.

### pyenv (most common)

```bash
# install a Python
pyenv install 3.12.7
pyenv install 3.13.0

# project-local pin (writes .python-version)
pyenv local 3.12.7

# point Poetry at it
poetry env use $(pyenv which python)
poetry env info
```

### asdf

```bash
asdf plugin add python
asdf install python 3.12.7
asdf local python 3.12.7
poetry env use python
```

### uv as the Python installer

```bash
uv python install 3.12.7
poetry env use $(uv python find 3.12.7)
```

Use uv just for interpreter management, Poetry for the project. Belt-and-braces.

### Constraints in pyproject.toml

```toml
# PEP 621 (preferred)
[project]
requires-python = ">=3.11,<3.14"

# Pre-2.0 / [tool.poetry] section
[tool.poetry.dependencies]
python = "^3.11"
```

The constraint affects the resolver: Poetry will only consider package versions that match every Python in the range.

### Switching mid-project

```bash
# nuke the old venv
poetry env remove python3.11

# tell Poetry the new one
poetry env use python3.12

# re-install — no need to re-lock if range is wide enough
poetry install
```

> **Watch out** — if you tighten `requires-python` after the lock was generated, `poetry lock` will need to re-resolve. Loose ranges (e.g. `>=3.11,<3.14`) keep the lock stable across team members on different Pythons.

---

## Slide 13 — Dependency Groups (Poetry 1.2+)

### Declaring groups

```toml
[tool.poetry.group.dev.dependencies]
pytest    = "^8"
ruff      = "^0.6"
mypy      = "^1.10"

[tool.poetry.group.docs]
optional = true

[tool.poetry.group.docs.dependencies]
mkdocs            = "^1.6"
mkdocs-material   = "^9"

[tool.poetry.group.profile.dependencies]
py-spy = "^0.3"
memray = "^1"
```

Groups are **internal-only** — never installed by your downstream users.

### Selecting groups at install time

```bash
# default: main + non-optional groups (dev included)
poetry install

# explicit
poetry install --with docs
poetry install --with docs,profile
poetry install --without dev
poetry install --only main          # production
poetry install --only docs          # docs build
poetry install --all-groups         # everything
```

### Optional vs default groups

- Default group (no `optional = true`) — installed by `poetry install` automatically
- **Optional group** — only installed when explicitly requested with `--with`
- Use optional for heavy/specialised stacks: GPU libs, profiling, perf tooling

### Adding to a group

```bash
poetry add --group dev pytest-asyncio
poetry add --group docs mkdocs-material
poetry add --group profile py-spy
```

---

## Slide 14 — Optional Extras (PEP 621)

### Public flags users opt into

```toml
# Poetry 2.0 / PEP 621
[project]
dependencies = [
  "fastapi (>=0.115)",
]

[project.optional-dependencies]
postgres = ["asyncpg (>=0.29)"]
redis    = ["redis (>=5)"]
all      = ["my-app[postgres,redis]"]

# Pre-2.0 syntax
[tool.poetry.dependencies]
asyncpg = { version = "^0.29", optional = true }
redis   = { version = "^5",    optional = true }

[tool.poetry.extras]
postgres = ["asyncpg"]
redis    = ["redis"]
all      = ["asyncpg", "redis"]
```

### Installing with extras

```bash
# your dev workflow
poetry install --extras "postgres redis"
poetry install --all-extras

# downstream user — pip / uv / pipx all support PEP 621
pip install 'my-app[postgres,redis]'
uv pip install 'my-app[postgres]'
pipx install 'my-app[postgres]'
```

### Groups vs extras — when to use which

| | Groups | Extras |
|---|---|---|
| Visible to users | no | yes |
| Use case | dev tooling | opt-in features |
| Standard | Poetry-only (PEP 735 in 2.0+) | PEP 621 |
| Cmd | `--with dev` | `--extras postgres` |

> **Don't put dev tooling in extras** — if `pytest` is in `[project.optional-dependencies]`, your users can `pip install my-app[test]` and pollute their environment. Use a **group** instead.

---

## Slide 15 — Source Constraints & Private Indexes

### Defining sources

```toml
[[tool.poetry.source]]
name     = "PyPI"
priority = "primary"

[[tool.poetry.source]]
name     = "internal"
url      = "https://pkg.acme.com/simple"
priority = "supplemental"

[[tool.poetry.source]]
name     = "pytorch-cu124"
url      = "https://download.pytorch.org/whl/cu124"
priority = "explicit"
```

Priority levels:

- **primary** — searched first
- **supplemental** — searched only if not found upstream
- **explicit** — used *only* when a package is bound to it

### Pin a package to a source

```bash
poetry add --source internal mylib
poetry add --source pytorch-cu124 torch
```

```toml
# resulting pyproject
[tool.poetry.dependencies]
mylib = { version = "^1.2", source = "internal" }
torch = { version = "^2.5", source = "pytorch-cu124" }
```

### Authentication (HTTP basic / token)

```bash
# interactive
poetry config http-basic.internal ci $TOKEN

# env-vars (CI-friendly)
export POETRY_HTTP_BASIC_INTERNAL_USERNAME=ci
export POETRY_HTTP_BASIC_INTERNAL_PASSWORD=$TOKEN

# certificate-based
poetry config certificates.internal.client-cert /path/cert.pem
```

### Why "explicit" matters

If `pytorch-cu124` is `supplemental`, Poetry might pull a Mac-only wheel by accident. Marking it `explicit` means only packages that opt in are routed there.

---

## Slide 16 — Build & Publish

### Build wheels & sdists

```bash
# build into ./dist/
poetry build

# only one format
poetry build --format wheel
poetry build --format sdist

# inspect
unzip -l dist/my_app-0.1.0-py3-none-any.whl
```

Uses **poetry-core** as the PEP 517 build backend. Pure-Python by default; for native extensions add a `build.py` script.

### Publish to PyPI

```bash
# configure once
poetry config pypi-token.pypi $PYPI_TOKEN

# publish what's in dist/
poetry publish

# build + publish in one shot
poetry publish --build

# test PyPI
poetry config repositories.testpypi https://test.pypi.org/legacy/
poetry config pypi-token.testpypi $TEST_TOKEN
poetry publish -r testpypi
```

### GitHub Actions: trusted publish

```yaml
name: Publish
on:
  release:
    types: [published]
permissions:
  id-token: write   # OIDC for PyPI
jobs:
  pypi:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: snok/install-poetry@v1
        with: { version: '2.0.1' }
      - run: poetry build
      - uses: pypa/gh-action-pypi-publish@release/v1
```

Use the official `pypa/gh-action-pypi-publish` for trusted publishing — no API token to manage.

### Bumping a version

```bash
poetry version 1.2.0
poetry version patch
poetry version minor
poetry version major
poetry version prerelease
poetry version --short          # print only
```

Edits `pyproject.toml` in place. For VCS-driven versions use the **poetry-dynamic-versioning** plugin.

---

## Slide 17 — Plugins — Extending Poetry

### Managing plugins

```bash
# install (into Poetry's own venv)
poetry self add poetry-plugin-export
poetry self add poetry-plugin-bundle
poetry self add poetry-dynamic-versioning

# list
poetry self show plugins

# upgrade / remove
poetry self update
poetry self remove poetry-plugin-export
```

### poetry-plugin-export

```bash
# produce a requirements.txt for tools that can't read poetry.lock
poetry export --format requirements.txt \
  --output requirements.txt \
  --without-hashes \
  --only main

# include hashes (default)
poetry export -f requirements.txt -o requirements.txt
```

Was bundled until 1.x; now an opt-in plugin. Useful for AWS Lambda, vendor scanners, legacy `pip` images.

### Other widely-used plugins

| Plugin | What it does |
|---|---|
| `poetry-plugin-shell` | Restores the classic `poetry shell` in 2.0+ |
| `poetry-plugin-bundle` | Bundles a project + deps into a self-contained venv |
| `poetry-plugin-up` | Bumps caret/tilde upper bounds in `pyproject.toml` |
| `poetry-dynamic-versioning` | Versions sourced from git tags |
| `poetry-plugin-mono-repo-deps` | Local path deps in monorepos |
| `poethepoet` | Task runner — like `npm run` |

### poethepoet (poe)

```toml
[tool.poe.tasks]
test  = "pytest"
lint  = "ruff check ."
serve = "uvicorn app:main --reload"
```

```bash
poetry run poe test
poetry run poe lint
```

---

## Slide 18 — Configuration — poetry config & poetry.toml

### CLI configuration

```bash
# global (default scope)
poetry config virtualenvs.in-project true
poetry config virtualenvs.path "$HOME/.venvs"
poetry config installer.parallel true
poetry config installer.max-workers 10
poetry config cache-dir "/big/disk/poetry-cache"

# project-local — writes ./poetry.toml
poetry config virtualenvs.in-project true --local

# inspect
poetry config --list
poetry config virtualenvs.in-project
```

### Useful keys

| Key | What |
|---|---|
| `virtualenvs.in-project` | Put venv in `./.venv` |
| `virtualenvs.create` | Create one or use system Python |
| `virtualenvs.prefer-active-python` | Honour the activated Python first |
| `installer.parallel` | Parallel wheel installs |
| `installer.max-workers` | Worker threads |
| `warnings.export` | Silence the export plugin warning |

### Environment variables

```bash
POETRY_VIRTUALENVS_IN_PROJECT=true
POETRY_VIRTUALENVS_CREATE=false
POETRY_HTTP_BASIC_INTERNAL_USERNAME=ci
POETRY_HTTP_BASIC_INTERNAL_PASSWORD=$TOKEN
POETRY_PYPI_TOKEN_PYPI=$TOKEN
POETRY_CACHE_DIR=/big/disk/poetry-cache
POETRY_NO_INTERACTION=1     # CI-safe
```

Every `config` key has an env-var equivalent — uppercase, prefix `POETRY_`, dots → underscores.

### poetry.toml (project-local)

```toml
# sits next to pyproject.toml
[virtualenvs]
in-project = true

[installer]
parallel = true
max-workers = 8
```

Commit it to enforce team-wide settings. Don't put secrets here — those go in env-vars or `auth.toml`.

### Resolution order

1. Command-line flags
2. Environment variables (`POETRY_*`)
3. Project `./poetry.toml`
4. User `~/.config/pypoetry/config.toml`
5. Built-in defaults

---

## Slide 19 — Performance & Internals

### The Mixology resolver

- Port of Dart's **PubGrub** algorithm — same family as Cargo, npm, uv
- Conflict-driven clause learning — backtracks intelligently rather than re-trying the whole graph
- Reports human-readable conflicts: "*fastapi requires pydantic ≥ 2, but my-lib pins pydantic < 1*"
- Slower than uv's Rust implementation but functionally equivalent — the same lock would result, just minutes vs seconds

### Caches

- Wheel/sdist cache: `~/.cache/pypoetry/cache/`
- Virtualenvs: `~/.cache/pypoetry/virtualenvs/`
- HTTP cache (PyPI metadata): `~/.cache/pypoetry/cache/repositories/`
- Inspect: `poetry cache list`, `poetry cache clear <name> --all`

### Speed-ups you can flip on

```bash
# parallel wheel installation (default in modern Poetry)
poetry config installer.parallel true
poetry config installer.max-workers 10

# skip pip in created venvs (faster bootstrap)
poetry config virtualenvs.options.no-pip true

# experimental: lazy-wheel partial downloads
poetry config experimental.system-git-client false
```

### Known slow spots

- **Cold lock** on big graphs (transformers, all of LangChain) — minutes
- Source-only packages with no wheels — Poetry has to download & inspect
- `poetry update` with a wide range of pre-release-eligible packages
- If lock perf hurts daily, evaluate **uv** (companion deck) — same lock semantics, ~50× faster resolver

> **Tip — use `--no-update` in CI.** `poetry install --no-update` (1.x) / default 2.0 behaviour skips re-resolution if the lock is fresh. Saves ~80 % of CI time on big projects.

---

## Slide 20 — Poetry vs pip / pipenv / pdm / Hatch / uv

| Capability | pip | pipenv | pdm | Hatch | Poetry | uv |
|---|---|---|---|---|---|---|
| Install / uninstall | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Lockfile | — | ✅ | ✅ | — | ✅ | ✅ |
| Cross-platform lock | — | partial | ✅ | — | ✅ | ✅ |
| Multi-Python lock | — | — | ✅ | — | partial | ✅ |
| Workspaces | — | — | partial | — | via plugin | ✅ |
| Manage Python | — | — | partial | ✅ | — | ✅ |
| Build / publish | — | — | ✅ | ✅ | ✅ | ✅ |
| PEP 621 native | — | — | ✅ | ✅ | 2.0+ | ✅ |
| Plugin ecosystem | — | — | partial | ✅ | ✅ | — |
| Resolver speed | slow | slow | medium | medium | medium | very fast |
| Maturity / mindshare | very high | declining | medium | medium | very high | rising fast |

### Verdicts

- **pip** — the floor; fine for one-off venvs; pair with pip-tools if you want reproducibility
- **pipenv** — historical; lock semantics & UX have not aged well
- **pdm** — closest spiritual ancestor of uv; smaller ecosystem; `__pypackages__` is unusual
- **Hatch** — strong on builds + matrices, weaker on locking
- **Poetry** — mature, opinionated, the de-facto standard for new app projects since ~2020
- **uv** — same lock semantics, much faster, single binary; fastest-rising

### When to pick Poetry today

- You value plugin ecosystem & community familiarity
- Your team already speaks Poetry — switching cost > gain
- You publish libraries and like Poetry's opinionated workflow
- You want pure-Python (no Rust binary in the supply chain)

---

## Slide 21 — Migrating from pip / requirements.txt

### Stage 1 — initialise Poetry

```bash
cd existing-repo
poetry init       # answer prompts (or skip with --no-interaction)

# this writes a minimal pyproject.toml with no deps yet
```

### Stage 2 — import requirements

```bash
# cleanest path: feed the file to poetry add
cat requirements.txt | xargs poetry add

# dev requirements separately
cat requirements-dev.txt | xargs poetry add --group dev

# or, more controlled — eyeball each
poetry add fastapi httpx pydantic
poetry add --group dev pytest ruff mypy
```

### Stage 3 — produce poetry.lock

```bash
poetry lock              # generate the lockfile
poetry install           # verify it installs cleanly
poetry run pytest        # smoke test

git add pyproject.toml poetry.lock
git rm requirements.txt requirements-dev.txt
```

### Keep `requirements.txt` for downstream

```bash
poetry self add poetry-plugin-export
poetry export -f requirements.txt \
  --without-hashes \
  --only main \
  -o requirements.txt
```

Useful for AWS Lambda, IDE remote interpreters, vendor security scanners.

> **Don't** keep both `requirements.txt` and `poetry.lock` as *sources of truth*. Pick one. Export *from* the lock; never edit a generated requirements file by hand.

---

## Slide 22 — Poetry 1.x → 2.0 — Migrating to PEP 621

### Before — Poetry 1.x layout

```toml
[tool.poetry]
name        = "svc"
version     = "1.2.3"
description = "..."
authors     = ["You <you@x.com>"]
readme      = "README.md"

[tool.poetry.dependencies]
python  = "^3.12"
fastapi = "^0.115"
httpx   = "^0.27"

[tool.poetry.group.dev.dependencies]
pytest = "^8"
ruff   = "^0.6"

[build-system]
requires      = ["poetry-core>=1.0"]
build-backend = "poetry.core.masonry.api"
```

### After — Poetry 2.0 / PEP 621

```toml
[project]
name        = "svc"
version     = "1.2.3"
description = "..."
readme      = "README.md"
requires-python = ">=3.12,<3.14"
authors     = [
  { name = "You", email = "you@x.com" }
]
dependencies = [
  "fastapi (>=0.115,<1)",
  "httpx (>=0.27,<1)",
]

[tool.poetry]
package-mode = true

[tool.poetry.group.dev.dependencies]
pytest = "^8"
ruff   = "^0.6"

[build-system]
requires      = ["poetry-core>=2.0"]
build-backend = "poetry.core.masonry.api"
```

### Key changes

- **Metadata** moves from `[tool.poetry]` to `[project]`
- Runtime deps in `dependencies = [...]` with PEP 508 strings
- **Dev/optional groups stay** under `[tool.poetry.group.*]` — Poetry-specific
- `requires-python` replaces the `python = "^3.12"` entry
- `package-mode = true/false` moves to `[tool.poetry]`
- Bump `poetry-core` requirement to `>=2.0`

### Verify after migration

```bash
poetry check               # validate pyproject
poetry lock                # refresh lock format
poetry install
poetry build               # confirm wheel still builds
```

---

## Slide 23 — CI Patterns — GitHub Actions

### Minimal CI workflow

```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python: ["3.11", "3.12", "3.13"]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python }}

      - uses: snok/install-poetry@v1
        with:
          version: '2.0.1'
          virtualenvs-in-project: true
          virtualenvs-create: true

      - name: Cache venv
        uses: actions/cache@v4
        with:
          path: .venv
          key: venv-${{ matrix.python }}-${{ hashFiles('poetry.lock') }}

      - run: poetry check --lock
      - run: poetry install --with dev --no-interaction
      - run: poetry run ruff check .
      - run: poetry run mypy src/
      - run: poetry run pytest -q --cov
```

### Why `snok/install-poetry@v1`

- Officially recommended — pins the Poetry version
- Sets `POETRY_NO_INTERACTION=1` for you
- Configures `virtualenvs.in-project` so cache keys are easy
- Adds `poetry` to PATH

### Speed-ups

- Cache `.venv` keyed on `poetry.lock` — turns reinstalls into seconds
- `poetry install --no-interaction --no-ansi` for cleaner logs
- `poetry config installer.parallel true` (default in modern Poetry)
- Skip `poetry install --sync` in dev jobs; use it in release jobs only

### Lock-drift gate

```yaml
- run: poetry check --lock
  # 2.0+: fails if pyproject changed without re-locking, or hashes are off
```

Run this *before* install. Fail fast on lock drift.

---

## Slide 24 — Docker Patterns

### Multi-stage build

```dockerfile
# ─── builder ────────────────────────────
FROM python:3.12-slim AS builder

ENV POETRY_VERSION=2.0.1 \
    POETRY_NO_INTERACTION=1 \
    POETRY_VIRTUALENVS_CREATE=false \
    PIP_NO_CACHE_DIR=1

RUN pip install "poetry==${POETRY_VERSION}"

WORKDIR /app
COPY pyproject.toml poetry.lock ./

# install deps only — leverages layer cache
RUN poetry install --only main --no-root

# now copy code and install the project itself
COPY src/ src/
RUN poetry install --only main

# ─── runtime ────────────────────────────
FROM python:3.12-slim AS runtime
COPY --from=builder /usr/local/lib/python3.12/site-packages \
                    /usr/local/lib/python3.12/site-packages
COPY --from=builder /app /app
WORKDIR /app
CMD ["python", "-m", "myapp"]
```

### Why `VIRTUALENVS_CREATE=false`

- The container *is* the venv — no need for one inside
- Smaller image, simpler PATH, no double-activation
- Set it via env-var so it survives layer caches

### Layer-cache friendliness

1. Copy `pyproject.toml` + `poetry.lock` first
2. `poetry install --only main --no-root` — caches deps
3. Copy source code
4. `poetry install --only main` — installs the project itself

Step 2 only invalidates when deps change — saves 90 %+ of rebuild time.

### Production sync

```dockerfile
# 2.0+ — guarantees no extras in the container
RUN poetry sync --only main --no-root

# 1.x equivalent
RUN poetry install --sync --only main --no-root
```

> For air-gapped builds, pre-cache wheels on a build host, mount as a volume, and use `poetry install --no-cache --no-update` + a private index.

---

## Slide 25 — Cheat Sheet — Daily Commands

| Task | Command |
|---|---|
| Start a project | `poetry new my-app` |
| Init in-place | `poetry init` |
| Add a dep | `poetry add httpx` |
| Add dev dep | `poetry add --group dev pytest` |
| Add an extra | `poetry add --optional asyncpg` |
| Remove dep | `poetry remove httpx` |
| Install | `poetry install` |
| Sync (exact match) | `poetry sync` |
| Update | `poetry update` |
| Run a script | `poetry run python main.py` |
| Run tests | `poetry run pytest` |
| Pick a Python | `poetry env use python3.12` |
| Activate (2.0) | `eval $(poetry env activate)` |
| Lock the deps | `poetry lock` |
| Validate lock | `poetry check --lock` |
| Production install | `poetry install --only main --no-root` |
| Build wheels | `poetry build` |
| Publish to PyPI | `poetry publish` |
| Bump version | `poetry version patch` |
| Export req.txt | `poetry export -o req.txt` |
| Show dep tree | `poetry show --tree` |
| Why is X here? | `poetry show --tree X` |
| List config | `poetry config --list` |
| Clear cache | `poetry cache clear PyPI --all` |
| Add a plugin | `poetry self add poetry-plugin-export` |

---

## Slide 26 — Gotchas & Troubleshooting

### Caret on pre-1.0 packages

Poetry's `^0.2.3` means `>=0.2.3,<0.3.0`, not `<1.0`. Catches people from npm-land. If you want any 0.x, use `>=0.2,<1`.

### "SolverProblemError"

Two packages have incompatible constraints. Read the message — Mixology tells you exactly which versions fight. Common fixes:

- Loosen one bound (drop `^` for `>=`)
- Pin a known-good intermediate version
- Update one offender to a newer line that supports both

### Slow lock on big graphs

Mixology re-checks the world for `poetry update`. Workarounds:

- `poetry update foo bar` — scope to packages
- `poetry lock --no-update` (1.x) — refresh hashes only
- Tighten `requires-python` — fewer candidate matrices

### Private index 401

```toml
[[tool.poetry.source]]
name = "internal"
url  = "https://pkg.acme.com/simple"
```

```bash
# in CI
POETRY_HTTP_BASIC_INTERNAL_USERNAME=ci
POETRY_HTTP_BASIC_INTERNAL_PASSWORD=$TOKEN
```

### "Cache is huge"

```bash
poetry cache list
poetry cache clear PyPI --all
rm -rf ~/.cache/pypoetry/artifacts
```

### poetry shell missing in 2.0

```bash
poetry self add poetry-plugin-shell
# or:
eval $(poetry env activate)
```

### Editable install of the project

Default with `package-mode = true`. To skip it (e.g. monorepo apps), use:

```bash
poetry install --no-root
```

---

## Slide 27 — Summary & Next Steps

### What we covered

- Poetry as the opinionated, declarative Python toolchain
- `poetry new` / `init` for project scaffolding
- PEP 621 `[project]` + Poetry-specific `[tool.poetry]`
- `poetry add` / `remove` / `install` / `sync`
- `poetry.lock` as a hash-pinned, cross-platform contract
- Virtual environments — `poetry env` & the `activate` dance
- Dependency groups vs optional extras
- Private indexes & source priorities
- Build & publish via `poetry-core`
- Plugins, configuration, performance internals
- CI & Docker patterns; common gotchas

### Recommended next steps

1. Install Poetry via pipx and run `poetry init` on an existing repo
2. Move dev tooling under `[tool.poetry.group.dev.dependencies]`
3. Turn on `virtualenvs.in-project = true` (project-local)
4. Add `poetry check --lock` as the first step in CI
5. Migrate your `[tool.poetry]` metadata to `[project]` (Poetry 2.0)
6. Install `poetry-plugin-export` if any consumer needs `requirements.txt`
7. For libraries, set up trusted PyPI publishing via GitHub Actions

### Companion / contrast deck

For the speed-first alternative, see **"Introduction to uv"** — same lock semantics, Rust binary, ~50× faster resolver. Side-by-side with this deck makes the trade-offs concrete.

### Further reading

- python-poetry.org
- github.com/python-poetry/poetry
- poetry-core — github.com/python-poetry/poetry-core
- PEP 621 — peps.python.org/pep-0621
- PEP 517 — peps.python.org/pep-0517
- install-poetry — github.com/snok/install-poetry

### One-line takeaway

If your repo still juggles `requirements.txt` + `requirements-dev.txt` + `setup.py` + `twine` — replace all four with one `pyproject.toml` and one `poetry.lock`.
