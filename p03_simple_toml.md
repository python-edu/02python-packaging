# Install the simple generator

## Goal

Install the existing simple generator without changing its Python modules into a package.

We will add only:

```text
pyproject.toml
```

and compare the installed application with the earlier direct script execution.

Do not modify or delete the previous generator. Create a new project for this stage.

---

## 1. Create the new stage

Open a terminal in the previous simple generator project and activate its environment.

### Windows PowerShell

```powershell
.\env\Scripts\activate
```

### Linux / macOS

```bash
source env/bin/activate
```

Create a new project:

```bash
python cli.py projectgen-toml -d ..
```

Deactivate the old environment:

```bash
deactivate
```

Go to the new project:

```bash
cd ../projectgen-toml
```

Copy these files from the previous generator into the root of `projectgen-toml`:

```text
cli.py
environment.py
myparser.py
naming.py
structure.py
```

The Python code is unchanged from `p01_simple_generator.md`.

---

## 2. Add `pyproject.toml`

Create:

```text
pyproject.toml
```

in the project root.

Copy the complete configuration from `p02_toml.md`:

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

---

## 3. Install the project

Activate the environment created inside `projectgen-toml`.

### Windows PowerShell

```powershell
.\env\Scripts\activate
```

### Linux / macOS

```bash
source env/bin/activate
```

Install the project in editable mode:

```bash
python -m pip install -e .
```

---

## 4. Test the installed command

Check help:

```bash
projectgen -h
```

Move to the parent directory:

```bash
cd ..
```

Create one test project:

```bash
projectgen test_toml -n
```

Expected result:

```text
test_toml/
├── data/
└── notebooks/
```

The command works outside the generator directory while its environment is active.

---

## What improved?

Before installation we had to run the file directly, for example:

```bash
python path/to/cli.py
```

After adding `pyproject.toml` and installing the project we can use:

```bash
projectgen
```

The Python code itself has not changed.

The current project is still a collection of top-level modules:

```text
cli
environment
myparser
naming
structure
```

In the next stage these related modules will be grouped inside one Python package.
