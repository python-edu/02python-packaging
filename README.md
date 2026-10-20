# Python packaging

## Goal

This repository has two goals:

1. **Learn Python packaging**
   - organize Python projects
   - make Python code installable
   - create Python packages
   - install and reuse code in different environments
   - build wheel files (`.whl`)
   - install command-line applications using `pipx`

2. **Develop a Python project generator (`projectgen`)**
   - create project directories and files automatically
   - support three project types: `simple`, `flat` and `src`
   - create virtual environments (`venv`)
   - allow selecting and installing Python packages
   - provide a command-line interface with different options

**We will learn Python packaging by developing the `projectgen` application step by step.**

We will start with several ordinary Python modules. First we will run them directly and observe the limitations of this approach. Then we will add `pyproject.toml` and install the same code without creating a Python package yet. After that we will reorganize the modules into a package using flat layout and finally src layout.

We will not publish packages on PyPI.

---

## Why install Python code?

A simple program can be run directly:

```bash
python cli.py
```

This is sufficient for small scripts, but the command depends on the location of the file. Running the program from another directory requires giving the path to `cli.py`, and importing several related modules also depends on Python's module search path.

Installation solves part of this problem.

After adding `pyproject.toml`, the same simple modules can be installed in a virtual environment and the application can provide a terminal command:

```bash
projectgen
```

The application can then be started from any directory while that environment is active.

---

## Why create a Python package?

**Installation does not require a package directory.**

A project containing top-level modules such as:

```text
cli.py
naming.py
structure.py
environment.py
```

can already be installed with `pip` when it has a suitable `pyproject.toml`.

However, these modules remain separate top-level names:

```python
import naming
import structure
```

For a larger project it is better to group related modules inside one Python package:

```text
projectgen/
├── __init__.py
├── cli.py
├── naming.py
├── structure.py
└── environment.py
```

The modules then belong to one namespace:

```python
from projectgen import naming
```

A package therefore gives us:

- one namespace for related modules,
- fewer possible name conflicts with other installed modules,
- a clear boundary between our application and other project files,
- a structure that can be developed further as the project grows.

The **flat** and **src** layouts are two ways of organizing the package. They are not required merely to make code installable.

---

## From scripts to an installed application

| Stage | What changes |
|---|---|
| simple | run modules directly with `python cli.py` |
| simple + `pyproject.toml` | install the same modules and use the `projectgen` command |
| flat | group the modules inside the `projectgen` package |
| src | move the package under `src/` |
| wheel | build an installable `.whl` file |
| pipx | install the command-line application independently |

Useful commands used later in the course:

| Method | Purpose |
|---|---|
| `python script.py` | run a Python file directly |
| `python -m module` | run an accessible module |
| `pip install -e .` | install the current project in editable mode |
| `pip install .` | install the current project |
| `pip install package.whl` | install a prepared wheel |
| `pipx install package.whl` | install a command-line application |

---

## Generator requirements

These settings describe the final version. We will add them step by step.

- project types: `simple`, `flat` and `src`
- default type: `src`
- default name: `project-YYYYMMDDHHMMSS`, e.g. `project-20261017164310`
- default location: current working directory
- virtual environment: `env`, created with Python and `pip`
- no additional libraries installed by default
- existing project directories are never overwritten
- create only the minimum directories and files needed for each type

Minimal output:

| Type | Directories and files |
|---|---|
| `simple` | `data/`, `notebooks/` |
| `flat` | package directory with an empty `__init__.py`, `pyproject.toml`, `README.md` |
| `src` | package directory inside `src/` with an empty `__init__.py`, `pyproject.toml`, `README.md` |

All types also include `env/` unless `-n` is used.

The generator does not create sample data, notebooks or example modules.
The structures in [Project layouts](p00_project_layouts.md) show possible project contents, not the generator's exact output.

### Command-line options

- `name` - optional project directory name
- `-t`, `--type` - project type
- `-d`, `--directory` - existing parent directory
- `-n`, `--no-venv` - skip virtual environment creation
- `-h`, `--help` - display help

All option names use lowercase letters.

For installable projects, the package directory name must be a valid Python identifier.
For example, `project-20261017164310` becomes `project_20261017164310`.

---

## Course materials

We will develop the same `projectgen` application step by step:

1. [Project layouts](p00_project_layouts.md) - compare simple, flat and src layouts
2. [Simple generator](p01_simple_generator.md) - create the first version and compare different ways of running it
3. [TOML and `pyproject.toml`](p02_toml.md) - learn the packaging configuration file
4. [Install the simple generator](p03_simple_toml.md) - add `pyproject.toml`, install the existing modules and compare this with direct script execution
5. [Flat layout](p04_flat_generator.md) - group the modules into the `projectgen` package
6. [Src layout](p05_src_generator.md) - move the package under `src/`

Later we will build a wheel and install the application with `pipx`.

---

## Prerequisites

Basic knowledge of:

- Python scripts and modules
- `import` and module search paths
- virtual environments (`venv`)
- using the system terminal

These topics are covered in the [00entry](https://github.com/python-edu/00entry) repository.

The exercises support Windows (PowerShell), Linux (Bash) and macOS (Zsh).
No specific editor or IDE is required.
