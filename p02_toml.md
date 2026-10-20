# `pyproject.toml`

## What is TOML?

**TOML** stands for **Tom's Obvious, Minimal Language**.

It is a simple text format used for configuration files.

Example:

```toml
[person]
name = "Anna"
age = 25
```

Official TOML documentation:

- https://toml.io/en/

---

## What is `pyproject.toml`?

`pyproject.toml` is the standard configuration file used by modern Python packaging tools.

It is placed in the root directory of a Python project:

```text
my_project/
├── pyproject.toml
└── ...
```

It can describe:

- how the project is built,
- project metadata,
- Python dependencies,
- terminal commands provided by the installed project,
- configuration of packaging tools.

Python Packaging User Guide:

- https://packaging.python.org/en/latest/guides/writing-pyproject-toml/
- https://packaging.python.org/en/latest/specifications/pyproject-toml/

Project metadata in `[project]` is standardized by PEP 621:

- https://peps.python.org/pep-0621/

---

## Basic TOML syntax

Values are assigned with `=`:

```toml
name = "my-project"
version = "0.1.0"
```

Lists use square brackets:

```toml
dependencies = ["numpy", "pandas"]
```

Sections also use square brackets:

```toml
[project]
name = "my-project"
```

Comments start with `#`:

```toml
# This is a comment
name = "my-project"
```

---

## `pyproject.toml` in Python packaging

Packaging tools read `pyproject.toml` when a project is installed or built.

For example:

```bash
python -m pip install .
```

or:

```bash
python -m build
```

---

## `[build-system]`

This section tells packaging tools which build backend should be used.

In this course we use `setuptools`:

```toml
[build-system]
requires = ["setuptools>=77.0.3"]
build-backend = "setuptools.build_meta"
```

---

## `[project]`

This section contains basic information about the project:

```toml
[project]
name = "projectgen"
version = "0.1.0"
description = "Simple Python project generator"
requires-python = ">=3.10"
dependencies = []
```

---

## Entry point: `[project.scripts]`

An installed project can provide a terminal command.

For the current simple generator:

```toml
[project.scripts]
projectgen = "cli:main"
```

The general form is:

```toml
command = "module:function"
```

When the module is inside a package, the form is:

```toml
command = "package.module:function"
```

For example:

```toml
projectgen = "projectgen.cli:main"
```

After installation, the command can be started directly:

```bash
projectgen
```

---

## Tell `setuptools` what to install

### Simple project with top-level modules

The current generator consists of separate top-level modules:

```text
cli.py
environment.py
myparser.py
naming.py
structure.py
```

They can be listed explicitly:

```toml
[tool.setuptools]
py-modules = [
    "cli",
    "environment",
    "myparser",
    "naming",
    "structure"
]
```

### Flat project with a Python package

Later the project will contain a package:

```text
my_project/
├── __init__.py
├── cli.py
└── ...
```

Files such as:

```text
cli.py
module1.py
module2.py
```

inside the package do not need to be listed separately.

If the package later contains subpackages:

```text
my_project/
├── __init__.py
├── cli.py
└── utils/
    ├── __init__.py
    └── files.py
```

we do not want to add every new subpackage manually to `pyproject.toml`.

`setuptools` can find packages automatically:

```toml
[tool.setuptools.packages.find]
where = ["."]
include = ["my_project*"]
```

This finds:

```text
my_project
my_project.utils
...
```

The same idea will later be used with `src` layout, but `where` will point to `src`.

---

## Complete `pyproject.toml` for the current simple generator

```toml
[build-system]
requires = ["setuptools>=77.0.3"]
build-backend = "setuptools.build_meta"

[project]
name = "projectgen"
version = "0.1.0"
description = "Simple Python project generator"
requires-python = ">=3.10"
dependencies = []

[project.scripts]
projectgen = "cli:main"

[tool.setuptools]
py-modules = [
    "cli",
    "environment",
    "myparser",
    "naming",
    "structure"
]
```

This file can be copied directly into the current simple generator project.
