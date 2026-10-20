# TOML and `pyproject.toml`


Official TOML documentation:

- https://toml.io/en/



## TOML syntax

TOML is a text configuration format: Tom's Obvious, Minimal Language.

```toml
# Comment
[person]
name = "Anna"
age = 25
languages = ["Polish", "English"]
```

- `key = value`: assignment
- `"text"`: string
- `[value, value]`: list
- `[section]`: table of settings


## Installation configuration

`pyproject.toml` belongs in the project root.
It describes the build backend, project metadata, dependencies and terminal commands.

| Section / field | Meaning |
|---|---|
| `[build-system]` | Tools needed to build the project |
| `build-backend` | Backend that prepares the installable distribution |
| `[project]` | Name, version and supported Python |
| `dependencies` | Runtime packages installed along with this project |
| `[project.scripts]` | Terminal commands mapped to Python functions |
| `[tool.setuptools]` | Settings specific to setuptools |

The generator has no external runtime dependencies: `dependencies = []`.
The build dependency `setuptools` is separate.

## Complete configuration: simple generator

```toml
[build-system]
requires = ["setuptools>=77.0.3"]
build-backend = "setuptools.build_meta"

[project]
name = "projectgen"
version = "0.1.0"
description = "Python project generator"
requires-python = ">=3.10"
dependencies = []

[project.scripts]
projectgen = "cli:main"

[tool.setuptools]
py-modules = ["cli", "environment", "myparser", "naming", "structure"]
```

`projectgen = "cli:main"` connects the command `projectgen` to `main()` in `cli.py`.
`py-modules` lists the top-level modules to install, without `.py`.

## Package layouts

After moving the modules into a package, the entry point becomes:

```toml
[project.scripts]
projectgen = "projectgen.cli:main"
```

Replace `py-modules` with package discovery:

```toml
[tool.setuptools.packages.find]
where = ["."]
include = ["projectgen", "projectgen.*"]
```

- `where = ["."]`: package in the project root — flat
- `where = ["src"]`: package under `src/`
- `include`: the package and its subpackages

## Installation and building

| Command | Result |
|---|---|
| `python -m pip install -e .` | Editable installation for working on the source |
| `python -m pip install .` | Build and install the current project |
| `python -m build` | Save source and wheel distributions in `dist/` |

`pip install .` already performs a build. A separate build is useful when you want
an installable file to keep or give to someone else. We practise this in `p07`.

References: [TOML](https://toml.io/en/),
[pyproject.toml](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/).
