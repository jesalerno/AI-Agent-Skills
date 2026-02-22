<!-- SPDX-License-Identifier: MIT -->
<!-- Copyright (c) 2026 John Salerno -->

# Programming Agent Skills — Python

**Version:** 1.2
**Revised:** 2026-02-22
**Parent document:** Programming Agent Mode (Generalist) v1.2
**License:** [MIT](LICENSE)

> Inspired by [GitHub Beast Mode](https://gist.github.com/burkeholland/88af0249c4b6aff3820bf37898c8bacf)

---

## Keyword Usage (RFC 2119)

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

These keywords appear only in normative sections. Informative sections are written in natural language and labeled **(Informative)**.

---

## 1. Scope (Informative)

This document specifies the operating behavior, coding standards, and definition of done for a software programming agent working in Python. It covers general-purpose Python development including applications, libraries, CLIs, APIs, data pipelines, and automation scripts.

This document extends Programming Agent Mode (Generalist) v1.2. All generalist requirements remain in force unless explicitly overridden here. Where this document conflicts with the generalist document, this document takes precedence.

This document does not cover Jupyter notebooks as the primary deliverable, data science workflows where notebooks are the preferred artifact, or Python extensions written in C/C++.

---

## 2. Definitions (Informative)

**CI-equivalent mode:** Running tests under the same conditions as the project's continuous integration pipeline — including Python version, virtual environment, installed dependencies, and environment variables — without relying on local-only configuration.

**External action:** Any operation with a side effect outside the current conversation context, including but not limited to: file system modifications, commits, network calls, CI triggers, dependency installs, and production-impacting commands.

**Minimal diff:** The smallest change to source code that correctly resolves the stated issue without altering unrelated behavior, structure, or formatting.

**Named coding standard (Python context):** Any of: PEP 8, PEP 257, PEP 484, PEP 526, PEP 20 (The Zen of Python), Google Python Style Guide, or a project-specific configuration in `pyproject.toml` or `setup.cfg`.

**Primary source (Python context):** [docs.python.org](https://docs.python.org/3/), Python Enhancement Proposals at [peps.python.org](https://peps.python.org/), PyPI package pages at [pypi.org](https://pypi.org/), and official tool documentation (mypy, Ruff, pytest, uv).

**Python version:** The current stable release series. As of this document revision: **Python 3.14.3** (released February 3, 2026) is the latest feature release; **Python 3.13.12** (released February 3, 2026) is the current long-term maintenance release. Source: [python.org/downloads](https://www.python.org/downloads/).

**Type-complete:** All public functions, methods, and module-level variables carry type annotations that pass mypy strict mode (`mypy --strict`) without error or suppression.

**Version-sensitive context:** Any situation where the correct behavior, API signature, stdlib module, or built-in differs across Python releases.

---

## 3. Operating Principles (Normative)

All Operating Principles from the generalist document (Section 3) apply. The following extensions and overrides apply for Python.

### 3.1 Primary Source Discipline (Python Extension)

- When referencing any Python stdlib module or built-in, the agent MUST consult [docs.python.org](https://docs.python.org/3/) for the target Python version and cite the specific module, function, and version availability.
- When citing any PEP as a design or style authority, the agent MUST cite the PEP number and fetch the current text from [peps.python.org](https://peps.python.org/).
- When referencing any third-party package, the agent MUST consult [pypi.org](https://pypi.org/) to verify the current stable version and fetch the package's official documentation before implementing or advising on its use.
- If a fetched source conflicts with training knowledge, the agent MUST flag the conflict, present both the fetched content and its training-data knowledge with sources, and defer to the fetched source unless the user instructs otherwise.

### 3.2 Python Version Targeting

- The agent MUST ask for the target Python version before implementing any version-sensitive feature, syntax, or stdlib API.
- The agent MUST NOT use features, syntax, or APIs unavailable in the project's minimum supported Python version without explicitly flagging the incompatibility and offering a compatible alternative.
- The agent MUST flag all deprecated stdlib modules and APIs (e.g., `distutils`, `imp`, `asynchat`), cite the replacement from [docs.python.org](https://docs.python.org/3/), and offer a migration path.
- When the target version is not specified, the agent MUST state its assumed version explicitly (see Section 9 defaults) and ask for correction.

### 3.3 Typing and Static Analysis

- The agent MUST add type annotations to all new public functions, methods, and module-level variables. All annotations MUST pass `mypy --strict` without error or suppression.
- The agent MUST use built-in generic types (`list[str]`, `dict[str, int]`, `tuple[int, ...]`) instead of `typing.List`, `typing.Dict`, `typing.Tuple` for code targeting Python 3.9+.
- The agent MUST use `X | Y` union syntax instead of `typing.Union[X, Y]` for code targeting Python 3.10+.
- The agent MUST NOT use `Any` from `typing` without an inline comment explaining why a more specific type is not possible.

### 3.4 Determinism and State

- The agent MUST NOT introduce global mutable state. Module-level variables MUST be constants (`UPPER_SNAKE_CASE`) or type-annotated as `Final`.
- The agent MUST NOT introduce side effects (time, I/O, randomness, global state reads) in functions that are not documented as non-deterministic. Non-deterministic behaviour MUST be explicit, isolated, and documented at the function level.
- The agent MUST NOT use `random` for security-sensitive operations. Use `secrets` (stdlib) instead.

---

## 4. Definition of Done (Normative)

All DoD criteria from the generalist document (Section 4) apply. The following extensions apply for Python.

### 4.1 Security (Python Extension)

- The agent MUST run `pip-audit` (v2.10.0+) against the project's dependency set and confirm zero known vulnerabilities:
  ```
  pip-audit                  # pip / venv projects
  uv run pip-audit           # uv-managed projects
  ```
- The agent MUST report the `pip-audit` version used, the command run, and full output. If execution is not possible, the agent MUST provide the exact command.
- The agent MUST NOT use `eval()`, `exec()`, or `pickle.loads()` on untrusted input without explicit user acknowledgment and a documented security justification.
- The agent MUST NOT use `subprocess.shell=True` with any user-supplied or externally sourced input.
- The agent MUST NOT store secrets, API keys, or credentials in source code, `.env` files committed to version control, or log output.

### 4.2 Tests and Coverage (Python Extension)

- The agent MUST use **pytest** (v9.0.2+) as the test framework for all new tests.
- The agent MUST structure tests using pytest fixtures, parametrize (`@pytest.mark.parametrize`), and markers. The agent MUST NOT use `unittest.TestCase` for new test code unless integrating with an existing `unittest`-based suite.
- Coverage MUST exceed **70%** for both the overall project and the code modified in the current task (consistent with generalist DoD), measured via `pytest-cov`:
  ```
  pytest --cov=<package> --cov-report=term-missing
  ```
- If the project has zero test coverage, the agent MUST propose a pytest-based test suite before proceeding with implementation.
- If coverage tooling is not configured, the agent MUST add `pytest-cov` to the project's dev dependencies. If the project cannot accept new dev dependencies, the agent MUST document the gap and propose a follow-up task.
- The agent SHOULD add property-based tests using `hypothesis` for functions with non-trivial input domains, citing [hypothesis.readthedocs.io](https://hypothesis.readthedocs.io/) when doing so.

### 4.3 Documentation (Python Extension)

- All public modules, classes, functions, and methods MUST have PEP 257-conformant docstrings using Google style (Args, Returns, Raises sections) unless the project already uses NumPy or Sphinx style, in which case the agent MUST match the existing style.
- The agent MUST update `README.md` or equivalent to reflect any changed public API, CLI flags, or configuration options.
- New CLI commands or entry points MUST be documented with usage examples in the README or a dedicated docs directory.
- The agent SHOULD generate or update documentation when the project already uses a documentation generator (Sphinx or MkDocs).

### 4.4 Lint and Formatting (Python Extension)

- The agent MUST run **Ruff** (v0.15.2+) for linting and confirm zero violations:
  ```
  ruff check .
  ```
- The agent MUST run **Ruff** for formatting and confirm no changes are needed:
  ```
  ruff format --check .
  ```
- If the project uses **Black** (v26.1.0+) instead of Ruff for formatting, the agent MUST respect that choice and run Black instead:
  ```
  black --check .
  ```
- The agent MUST run **mypy** in strict mode and confirm zero errors:
  ```
  mypy --strict <package>
  ```
- The agent MUST NOT suppress Ruff rules or mypy errors inline (`# noqa`, `# type: ignore`) without justification. See §5.6 for the suppression annotation requirement.
- If no linter is configured, the agent MUST add a `pyproject.toml` with Ruff configuration as part of the task.

---

## 5. Coding Standards (Normative)

All Coding Standards from the generalist document (Section 5) apply. The following extensions and overrides apply for Python.

### 5.1 Naming (Python Extension)

- The agent MUST follow PEP 8 naming conventions at all times:
  - Modules and packages: `snake_case`
  - Functions and variables: `snake_case`
  - Classes: `UpperCamelCase`
  - Constants: `UPPER_SNAKE_CASE`
  - Private members: single leading underscore `_name`; name-mangled members: double leading underscore `__name`
- The agent MUST NOT use single-character names outside of loop counters (`i`, `j`) and coordinate variables (`x`, `y`, `z`).
- The agent MUST NOT shadow Python builtins (e.g., `list`, `id`, `type`, `input`, `filter`, `map`).

### 5.2 Functions, Classes, and Structure

- The agent MUST prefer `@dataclass` or `pydantic.BaseModel` for classes whose sole purpose is holding data. Plain classes with only `__init__` MUST NOT be used for new data containers.
- The agent MUST use `pathlib.Path` for all file system path operations. The agent MUST NOT use `os.path` string manipulation for new code.
- The agent MUST use context managers (`with` statements) for all resource acquisition (files, sockets, database connections, locks).
- The agent MUST NOT use mutable default arguments in function signatures. Use `None` as the default and initialize inside the function body.
- The agent SHOULD prefer `dataclasses.field(default_factory=...)` over mutable defaults in dataclass definitions.

### 5.3 Error Handling (Python Extension)

- The agent MUST catch specific exception types. The agent MUST NOT use bare `except:` or `except Exception:` without re-raising or explicitly logging the caught exception.
- Exception messages MUST state what failed, include relevant identifiers or context values, and SHOULD suggest corrective action.
- The agent MUST use custom exception classes (subclasses of an appropriate base exception) for library or application-specific error conditions. Exception class names MUST end in `Error`.
- The agent MUST NOT use exceptions for normal control flow (e.g., using `ValueError` as a sentinel return signal, or `KeyError` to branch on dict membership).

### 5.4 Async Code

- The agent MUST use `asyncio` and `async`/`await` for asynchronous code. The agent MUST NOT mix `asyncio` with `threading` or `multiprocessing` without explicit justification and documentation.
- The agent MUST NOT use `asyncio.get_event_loop()` in new code targeting Python 3.10+. Use `asyncio.run()` as the entry point and `asyncio.get_running_loop()` when a reference to the running loop is needed.
- The agent MUST NOT use `asyncio.sleep(0)` as a yield mechanism without a comment explaining the intent.
- All `async` functions MUST be annotated with return types. Coroutines returning nothing MUST be annotated `-> None`.

### 5.5 Imports and Module Structure

- The agent MUST organize imports in the order mandated by PEP 8 and enforced by Ruff's isort rules: stdlib → third-party → local. Each group separated by a blank line.
- The agent MUST NOT use wildcard imports (`from module import *`) except in `__init__.py` files that explicitly define `__all__`.
- The agent MUST declare `__all__` in any module that is part of a public API, listing only the symbols intended for external use.
- The agent MUST NOT use relative imports (`from . import x`) across package boundaries. Relative imports within a package are acceptable.

### 5.6 Comments and Suppressions (Python Extension)

- All public modules, classes, methods, and functions MUST have PEP 257-conformant docstrings. Format requirements are specified in §4.3.
- Inline comments MUST explain *why*, not *what*. A comment that restates the code in English MUST be removed or rewritten.
- Any use of `# type: ignore`, `# noqa`, or `# pragma: no cover` MUST include an inline explanation of why the suppression is necessary and when it can be removed.
- TODOs MUST include a description of what is deferred and, where known, a tracking reference (issue number, ticket, or owner).

---

## 6. Safety and Correctness Constraints (Normative)

All Safety and Correctness Constraints from the generalist document (Section 6) apply.

Additionally:
- The agent MUST NOT fabricate PyPI package names, versions, or API signatures. All package references MUST be verified against [pypi.org](https://pypi.org/) before use.
- The agent MUST NOT recommend packages that are unmaintained (no release in 24+ months) without explicitly flagging the maintenance status and offering a maintained alternative.

---

## 7. Default Workflow (Informative)

Execute in order, following the generalist workflow with the following Python-specific additions:

1. **Restate the problem** — include target Python version, package manager, and application type (library, CLI, API, script, data pipeline).
2. **Ask clarifying questions** — in addition to generalist questions, ask: Python version (min and max supported), package manager (`uv`, `pip`, `poetry`, `conda`?), virtual environment setup, existing test framework (pytest / unittest?), type checking configured (mypy / Pyright?), linter/formatter (`ruff`, `black`, `flake8`?), deployment target (CPython, PyPy, cloud function, container?).
3. **Plan** — produce a TODO checklist. For library tasks, include a step to verify `__all__` and public API documentation.
4. **Research** — fetch from [docs.python.org](https://docs.python.org/3/), [peps.python.org](https://peps.python.org/), or [pypi.org](https://pypi.org/) before implementing. Cite all sources inline with version numbers.
5. **Implement** — apply the minimal diff. Follow coding standards from Sections 3 and 5.
6. **Verify** — run `pytest --cov`, `ruff check`, `ruff format --check`, `mypy --strict`, and `pip-audit`. If tools cannot be run, provide exact commands and expected output.
7. **Report** — summarize what changed, why, how it was verified, and any remaining risks. Show the updated TODO list.

---

## 8. Authoritative Documentation Sources (Informative)

The agent MUST treat the following as primary sources and MUST fetch from them before implementing or advising on version-sensitive or API-specific topics. Training data alone is insufficient for these claims.

| Source | Purpose |
|--------|---------|
| [docs.python.org/3](https://docs.python.org/3/) | Python stdlib, language reference, built-ins, version availability |
| [peps.python.org](https://peps.python.org/) | Python Enhancement Proposals — language design and style standards |
| [pypi.org](https://pypi.org/) | Package versions, maintainership status, release history |
| [mypy.readthedocs.io](https://mypy.readthedocs.io/) | Type checking rules, configuration, strict mode behavior |
| [docs.astral.sh/ruff](https://docs.astral.sh/ruff/) | Ruff linting rules, formatter behavior, configuration |
| [docs.astral.sh/uv](https://docs.astral.sh/uv/) | Package and project management with uv |
| [docs.pytest.org](https://docs.pytest.org/) | Test framework API, fixtures, plugins, configuration |
| [hypothesis.readthedocs.io](https://hypothesis.readthedocs.io/) | Property-based testing strategies and API |
| [black.readthedocs.io](https://black.readthedocs.io/) | Black formatter configuration and style (when used instead of Ruff) |

---

## 9. Language-Specific Defaults (Informative)

These are the baseline assumptions when project configuration is not specified. The agent MUST state these assumptions explicitly and ask for correction if they are wrong.

- **Python version:** 3.13.12 (current LTS maintenance release as of this document revision). Use 3.14.3 only when the project explicitly targets it, as 3.14 is the newest feature release and ecosystem support is still maturing.
- **Package manager:** `uv` (preferred for new projects); `pip` with virtual environments as fallback
- **Virtual environment:** Always required. The agent MUST NOT install packages into the system Python.
- **Test framework:** pytest 9.0.2
- **Type checker:** mypy 1.19.1, strict mode (`--strict`)
- **Linter/formatter:** Ruff 0.15.2 (replaces Black, isort, and Flake8 for new projects)
- **Formatter (legacy projects):** Black 26.1.0 when already in use
- **Vulnerability scanner:** pip-audit 2.10.0
- **Configuration file:** `pyproject.toml` (PEP 518/621); `setup.cfg` or `setup.py` only for legacy projects
- **Minimum Python version for new projects:** 3.12 (unless constrained by deployment target or dependency requirements)

---

## 10. Cross-Standard Guidance (Informative)

For Python, the relevant external standards and style guides are:

- **PEP 8** ([peps.python.org/pep-0008](https://peps.python.org/pep-0008/)) — authoritative style guide for Python code. The agent MUST follow PEP 8 for all naming, whitespace, and structural decisions. MUST be fetched and cited for non-obvious decisions.
- **PEP 257** ([peps.python.org/pep-0257](https://peps.python.org/pep-0257/)) — authoritative docstring conventions. MUST be fetched when docstring format is in question.
- **PEP 484 / PEP 526 / PEP 604 / PEP 695** ([peps.python.org](https://peps.python.org/)) — type annotation standards. MUST be fetched when advising on type annotation syntax, particularly for version-sensitive features (e.g., `X | Y` syntax requires Python 3.10+; `type` statement requires Python 3.12+).
- **Google Python Style Guide** ([google.github.io/styleguide/pyguide.html](https://google.github.io/styleguide/pyguide.html)) — MUST be consulted when the project explicitly references it as its style standard.

Before applying any rule from these standards, the agent MUST fetch the current published version per §3.1.

---

## 11. Project File Structure (Normative)

This section specifies canonical file and directory layouts for new Python projects, default configuration file content, and the criteria for choosing between layouts. All requirements in this section are normative unless labeled otherwise.

**Sources:** [docs.astral.sh/uv/concepts/projects](https://docs.astral.sh/uv/concepts/projects/layout/), [packaging.python.org — src vs flat layout](https://packaging.python.org/en/latest/discussions/src-layout-vs-flat-layout/), [docs.pytest.org — good integration practices](https://docs.pytest.org/en/stable/explanation/goodpractices.html), [packaging.python.org — writing pyproject.toml](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/).

---

### 11.1 Layout Selection (Normative)

The agent MUST apply the following decision rule when initiating or scaffolding a Python project:

| Project type | Intended for distribution (PyPI / internal registry)? | Layout to use |
|---|---|---|
| Library / reusable package | Yes | **src layout** |
| CLI tool published to PyPI | Yes | **src layout** |
| Web application / API server | No | **src layout** (RECOMMENDED) |
| Internal script or automation | No | Flat layout acceptable |
| Prototype / throwaway | No | Flat layout acceptable |

The agent MUST use the **src layout** for any project that will be packaged, installed, or distributed. The agent SHOULD use the src layout for all non-trivial applications, regardless of distribution intent, because it prevents accidental imports of uninstalled local source and ensures tests exercise the installed package rather than the working directory.

The agent MUST NOT use a flat layout for new libraries. If a flat layout is found in an existing library project, the agent SHOULD flag it and offer a migration plan.

**Why src layout matters for testing (Informative):** pytest's own documentation recommends `--import-mode=importlib` and src layout together. Without src layout, running `pytest` from the project root can cause Python to import the local source tree instead of the installed package, masking packaging errors and import path issues that will surface in CI or after installation.

---

### 11.2 Canonical src Layout — Library (Normative)

Use when: building a reusable package, a CLI tool published to PyPI, or any project that will be `pip install`-able.

Initialize with:

```bash
uv init --lib my-package
# then:
touch src/my_package/py.typed
mkdir -p tests && touch tests/__init__.py
```

**Required directory and file structure:**

```
my-package/
├── .github/
│   └── workflows/
│       └── ci.yml              # CI pipeline (see §11.5)
├── docs/
│   └── index.md                # User-facing documentation
├── src/
│   └── my_package/
│       ├── __init__.py         # Public API surface; defines __all__
│       ├── py.typed            # PEP 561 marker — enables mypy to type-check consumers
│       └── ...                 # Module files
├── tests/
│   ├── __init__.py             # Required for --import-mode=importlib with duplicate file names
│   ├── conftest.py             # Shared fixtures
│   ├── unit/
│   │   └── test_*.py
│   └── integration/
│       └── test_*.py
├── .gitignore                  # MUST exclude .venv/, dist/, __pycache__/, *.egg-info/
├── .python-version             # Managed by uv; pins the project Python version
├── CHANGELOG.md
├── LICENSE
├── README.md
├── pyproject.toml              # Single source of truth for all project and tool config
└── uv.lock                     # MUST be committed to version control
```

**Files that MUST NOT be committed to version control:**

```
.venv/
dist/
__pycache__/
*.egg-info/
*.pyc
.mypy_cache/
.ruff_cache/
.pytest_cache/
```

---

### 11.3 Canonical src Layout — Application (Normative)

Use when: building a web server, API, background worker, or any deployable application that will not be published to PyPI as a reusable library.

Initialize with:

```bash
uv init --package my-app       # creates src layout with entry point
# then:
touch src/my_app/py.typed
mkdir -p tests && touch tests/__init__.py
```

**Required directory and file structure:**

```
my-app/
├── .github/
│   └── workflows/
│       └── ci.yml
├── docs/
│   └── index.md
├── src/
│   └── my_app/
│       ├── __init__.py
│       ├── __main__.py         # Entry point: `python -m my_app`
│       ├── py.typed
│       ├── config.py           # Settings / configuration loading
│       └── ...
├── tests/
│   ├── __init__.py
│   ├── conftest.py
│   ├── unit/
│   └── integration/
├── .gitignore
├── .python-version
├── README.md
├── pyproject.toml
└── uv.lock
```

**Application-specific additions (as applicable):**

```
my-app/
├── Dockerfile                  # If containerized
├── docker-compose.yml          # If local dev uses containers
├── Makefile                    # OPTIONAL: convenience targets (make test, make lint)
└── scripts/
    └── *.py / *.sh             # One-off operational scripts; NOT part of the package
```

---

### 11.4 Annotated pyproject.toml Templates (Normative)

All tool configuration MUST reside in `pyproject.toml`. See §11.7 for the list of legacy config files that MUST NOT be created in new projects.

#### 11.4.1 Library Template

```toml
# pyproject.toml — Library project
# Conforms to PEP 517, PEP 518, PEP 621

[project]
name = "my-package"
version = "0.1.0"
description = "One-sentence description of the package."
readme = "README.md"
license = { file = "LICENSE" }   # Or: { text = "MIT" } for inline declaration
authors = [{ name = "Author Name", email = "author@example.com" }]

# MUST declare the full supported Python version range.
# Do not use a lower bound older than 3.12 for new projects.
requires-python = ">=3.12"

# Runtime dependencies only. Dev/test dependencies go in [dependency-groups].
dependencies = [
    # "requests>=2.32",
]

# PyPI classifiers: https://pypi.org/classifiers/
classifiers = [
    "Development Status :: 3 - Alpha",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.12",
    "Programming Language :: Python :: 3.13",
]

[project.urls]
Homepage = "https://github.com/example/my-package"
Documentation = "https://my-package.readthedocs.io"
Repository = "https://github.com/example/my-package"
"Bug Tracker" = "https://github.com/example/my-package/issues"

# Build system: uv_build for new projects; hatchling or setuptools are acceptable alternatives.
# IMPORTANT: Verify the current uv_build version at pypi.org/project/uv-build before use;
# the version range below was accurate at document revision but tracks uv's release cadence.
[build-system]
requires = ["uv_build>=0.10,<0.11"]
build-backend = "uv_build"

# Development-only dependencies (not installed by consumers of the package).
# uv will install these when running `uv sync` in a dev environment.
[dependency-groups]
dev = [
    "pytest>=9.0",
    "pytest-cov>=6.0",
    "hypothesis>=6.0",
    "mypy>=1.19",
    "ruff>=0.15",
    "pip-audit>=2.10",
]

# ── pytest ────────────────────────────────────────────────────────────────────
[tool.pytest.ini_options]
# importlib mode: required with src layout to avoid importing local source
# instead of the installed package.
addopts = [
    "--import-mode=importlib",
    "--strict-markers",           # Fail on unregistered markers
    "--strict-config",            # Fail on unrecognised config keys
    "-ra",                        # Show short test summary for all except passed
]
testpaths = ["tests"]
# Register custom markers here to avoid PytestUnknownMarkWarning:
# markers = ["slow: marks tests as slow (deselect with '-m \"not slow\"')"]

# ── coverage ──────────────────────────────────────────────────────────────────
[tool.coverage.run]
source = ["src"]
branch = true                     # Measure branch coverage, not just line coverage

[tool.coverage.report]
fail_under = 70                   # Matches the 70% threshold in §4.2
show_missing = true
exclude_lines = [
    "pragma: no cover",
    "if TYPE_CHECKING:",
    "raise NotImplementedError",
    "if __name__ == .__main__.:",
]

# ── mypy ──────────────────────────────────────────────────────────────────────
[tool.mypy]
python_version = "3.13"
strict = true                     # Enables --disallow-untyped-defs, --warn-return-any, etc.
warn_unreachable = true
pretty = true
# For each third-party package without stubs, add an explicit ignore:
# [[tool.mypy.overrides]]
# module = "some_untyped_package.*"
# ignore_missing_imports = true

# ── Ruff ──────────────────────────────────────────────────────────────────────
[tool.ruff]
target-version = "py313"          # Must match requires-python minimum
line-length = 88                  # Matches Black default; adjust if project standard differs
src = ["src"]                     # Tells Ruff which directories contain first-party code

[tool.ruff.lint]
select = [
    "E",    # pycodestyle errors
    "W",    # pycodestyle warnings
    "F",    # pyflakes
    "I",    # isort (import ordering)
    "B",    # flake8-bugbear (likely bugs and design problems)
    "C4",   # flake8-comprehensions (unnecessary constructs)
    "UP",   # pyupgrade (modernise Python syntax)
    "S",    # flake8-bandit (security)
    "T20",  # flake8-print (print statement detection)
    "RUF",  # Ruff-native rules
]
ignore = [
    "S101", # Allow assert statements (common in tests)
]

[tool.ruff.lint.per-file-ignores]
"tests/**" = ["S101", "T20"]      # Allow assert and print in tests

[tool.ruff.lint.isort]
known-first-party = ["my_package"]

[tool.ruff.format]
# Ruff format is a Black-compatible formatter; no further configuration required
# unless project has a specific style override.
quote-style = "double"
indent-style = "space"
```

#### 11.4.2 Application Template

The application template is identical to the library template with the following differences:

```toml
# pyproject.toml — Application project
# Differences from the library template are annotated with # APP-ONLY

[project]
name = "my-app"
version = "0.1.0"
description = "One-sentence description."
readme = "README.md"
requires-python = ">=3.12"

dependencies = [
    # List all runtime dependencies here. For applications, pinning major
    # versions is acceptable. For libraries it is discouraged.
]

# APP-ONLY: Define the command-line entry point.
# This registers `my-app` as a runnable command after `uv sync` or `pip install`.
[project.scripts]
my-app = "my_app.__main__:main"

# APP-ONLY: No [project.urls] block required unless publishing to PyPI.

# APP-ONLY: No [build-system] block required unless the application will be
# packaged and distributed. uv handles app builds differently from library builds.
# Add this only if you need `uv build` / PyPI publishing:
# [build-system]
# requires = ["uv_build>=0.10,<0.11"]
# build-backend = "uv_build"

[dependency-groups]
dev = [
    "pytest>=9.0",
    "pytest-cov>=6.0",
    "mypy>=1.19",
    "ruff>=0.15",
    "pip-audit>=2.10",
]
```

All `[tool.*]` sections from §11.4.1 (pytest, coverage, mypy, ruff) MUST be repeated verbatim in the application `pyproject.toml`.

---

### 11.5 CI Pipeline Template (Informative)

The following GitHub Actions workflow reflects the verification steps required by Section 4. It is provided as a starting template; the agent SHOULD adapt it to the project's CI provider.

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v5
        with:
          enable-cache: true
      - run: uv sync --frozen
      - run: uv run ruff check .
      - run: uv run ruff format --check .
      - run: uv run mypy --strict src/

  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.12", "3.13"]   # Test against all supported versions
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v5
        with:
          enable-cache: true
          python-version: ${{ matrix.python-version }}
      - run: uv sync --frozen
      - run: uv run pytest --cov --cov-report=term-missing --cov-fail-under=70

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v5
      - run: uv sync --frozen
      - run: uv run pip-audit
```

---

### 11.6 Mandatory Root-Level Files (Normative)

The agent MUST ensure the following files exist and are non-empty in all non-trivial projects:

| File | Required content |
|---|---|
| `README.md` | Project name, one-paragraph description, installation instructions, and a minimal usage example |
| `pyproject.toml` | `[project]` metadata, `requires-python`, `[dependency-groups]` dev group, and all `[tool.*]` config from §11.4 |
| `uv.lock` | Generated by uv; committed to version control |
| `.python-version` | Single line: the pinned Python version (e.g., `3.13.12`) |
| `.gitignore` | MUST include at minimum: `.venv/`, `dist/`, `__pycache__/`, `*.egg-info/`, `.mypy_cache/`, `.ruff_cache/`, `.pytest_cache/` |
| `LICENSE` | MUST be present for any project intended for distribution |
| `CHANGELOG.md` | SHOULD be present for libraries; MAY be omitted for internal applications |

The agent MUST NOT leave `pyproject.toml` in a skeleton/placeholder state (e.g., with empty `dependencies = []` and no tool configuration) after completing implementation work.

---

### 11.7 What MUST NOT Exist in New Projects (Normative)

| File | Reason | Replacement |
|---|---|---|
| `setup.py` | Deprecated build entrypoint for new projects | `pyproject.toml` with `[build-system]` |
| `setup.cfg` | Superseded by `pyproject.toml` (PEP 621) | `[project]` table in `pyproject.toml` |
| `requirements.txt` | Not reproducible; no lock semantics | `uv.lock` + `[dependency-groups]` |
| `requirements-dev.txt` | See above | `[dependency-groups]` dev group |
| `MANIFEST.in` | Replaced by `[tool.uv_build]` / hatchling include rules | Build backend config in `pyproject.toml` |
| `.flake8` | Ruff replaces Flake8 | `[tool.ruff.lint]` in `pyproject.toml` |
| `mypy.ini` | Consolidated | `[tool.mypy]` in `pyproject.toml` |
| `.isort.cfg` | Ruff handles import sorting | `[tool.ruff.lint.isort]` in `pyproject.toml` |
| `pytest.ini` | Consolidated | `[tool.pytest.ini_options]` in `pyproject.toml` |
| `.coveragerc` | Consolidated | `[tool.coverage]` in `pyproject.toml` |

**Exception:** The agent MUST NOT remove these files from existing projects unless the user explicitly requests migration. The agent MUST flag their presence in new projects and offer to consolidate them.
