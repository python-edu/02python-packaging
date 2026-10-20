# Add flat project support

## Goal

Extend the current generator so that it can create two project types:

```text
simple
flat
```

`simple` stays unchanged.

`flat` should create:

```text
my-flat-project/
├── env/
├── data/
├── notebooks/
├── my_flat_project/
│   ├── __init__.py
│   └── cli.py
├── pyproject.toml
└── README.md
```

---

## 1. Create the new stage

Open a terminal in `projectgen-toml` and activate its environment.

### Windows PowerShell

```powershell
.\env\Scripts\activate
```

### Linux / macOS

```bash
source env/bin/activate
```

Create a new project for this stage:

```bash
projectgen projectgen-flat -d ..
```

Deactivate the old environment:

```bash
deactivate
```

Go to the new project:

```bash
cd ../projectgen-flat
```

Copy these files from `projectgen-toml` into `projectgen-flat`:

```text
cli.py
environment.py
myparser.py
naming.py
structure.py
pyproject.toml
```

---

## 2. Add a package name helper

Add this function to `naming.py`:

```python
def package_name(project_name):
    return project_name.replace('-', '_')
```

The complete `naming.py` should now be:

```python
from datetime import datetime


def default_name():
    return 'project-' + datetime.now().strftime('%Y%m%d%H%M%S')


def package_name(project_name):
    return project_name.replace('-', '_')
```

---

## 3. Add flat directory creation

Add this function to `structure.py`:

```python
def create_flat(project, package):
    project.mkdir()
    (project / 'data').mkdir()
    (project / 'notebooks').mkdir()

    package_dir = project / package
    package_dir.mkdir()
    (package_dir / '__init__.py').touch()
```

The complete `structure.py` should now be:

```python
def create_simple(project):
    project.mkdir()
    (project / 'data').mkdir()
    (project / 'notebooks').mkdir()


def create_flat(project, package):
    project.mkdir()
    (project / 'data').mkdir()
    (project / 'notebooks').mkdir()

    package_dir = project / package
    package_dir.mkdir()
    (package_dir / '__init__.py').touch()
```

---

## 4. Create files for flat projects

Create a new file:

```text
projectfiles.py
```

Add this code:

```python
def create_package_cli(project, package):
    content = '''def main():
    pass


if __name__ == '__main__':
    main()
'''

    (project / package / 'cli.py').write_text(
        content,
        encoding='utf-8'
    )


def create_readme(project, project_name):
    content = f'# {project_name}\n'

    (project / 'README.md').write_text(
        content,
        encoding='utf-8'
    )


def create_pyproject(project, project_name, package):
    content = f'''[build-system]
requires = ["setuptools>=77.0.3"]
build-backend = "setuptools.build_meta"

[project]
name = "{project_name}"
version = "0.1.0"
description = "Flat Python project"
requires-python = ">=3.10"
dependencies = []

[project.scripts]
{project_name} = "{package}.cli:main"

[tool.setuptools.packages.find]
where = ["."]
include = ["{package}*"]
'''

    (project / 'pyproject.toml').write_text(
        content,
        encoding='utf-8'
    )
```

The package discovery section:

```toml
[tool.setuptools.packages.find]
where = ["."]
include = ["my_project*"]
```

lets `setuptools` find the main package and future subpackages automatically.

---

## 5. Add project type selection

Add this argument to `myparser.py`:

```python
parser.add_argument(
    '-t', '--type',
    choices=['simple', 'flat'],
    default='simple',
    help='Project type: simple or flat (default: simple)'
)
```

The complete `myparser.py` should now be:

```python
import argparse


def parse_arguments():
    parser = argparse.ArgumentParser(
        description='Create a simple Python project'
    )

    parser.add_argument(
        'name', nargs='?',
        help='Project name (default: project-YYYYMMDDHHMMSS)'
    )

    parser.add_argument(
        '-t', '--type',
        choices=['simple', 'flat'],
        default='simple',
        help='Project type: simple or flat (default: simple)'
    )

    parser.add_argument(
        '-d', '--directory',
        default='.',
        help='Parent directory (default: current directory)'
    )

    parser.add_argument(
        '-n', '--no-venv',
        action='store_true',
        help='Do not create a virtual environment'
    )

    return parser.parse_args()
```

---

## 6. Update `cli.py`

Add these imports:

```python
from naming import default_name, package_name
from projectfiles import create_package_cli, create_pyproject, create_readme
from structure import create_flat, create_simple
```

Add project type selection:

```python
if args.type == 'simple':
    create_simple(project)

elif args.type == 'flat':
    package = package_name(name)
    create_flat(project, package)
    create_package_cli(project, package)
    create_readme(project, name)
    create_pyproject(project, name, package)
```

The complete `cli.py` should now be:

```python
from pathlib import Path

from environment import create_environment
from myparser import parse_arguments
from naming import default_name, package_name
from projectfiles import create_package_cli, create_pyproject, create_readme
from structure import create_flat, create_simple


def main():
    args = parse_arguments()
    name = args.name or default_name()
    project = Path(args.directory).expanduser().resolve() / name

    if args.type == 'simple':
        create_simple(project)

    elif args.type == 'flat':
        package = package_name(name)
        create_flat(project, package)
        create_package_cli(project, package)
        create_readme(project, name)
        create_pyproject(project, name, package)

    if not args.no_venv:
        create_environment(project)

    print(f'Created {args.type} project: {project}')


if __name__ == '__main__':
    main()
```

---

## 7. Update the generator `pyproject.toml`

Add:

```toml
"projectfiles"
```

to the `py-modules` list.

The complete `pyproject.toml` should now be:

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
    "projectfiles",
    "structure"
]
```

---

## 8. Install the updated generator

Activate the environment of `projectgen-flat`.

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

## 9. Test the flat project type

Check help:

```bash
projectgen -h
```

Create one flat project:

```bash
projectgen my-flat-project -t flat -n
```

Check that the project contains:

```text
my_flat_project/__init__.py
my_flat_project/cli.py
pyproject.toml
README.md
```

Open the generated `pyproject.toml` and check:

```toml
[project.scripts]
my-flat-project = "my_flat_project.cli:main"

[tool.setuptools.packages.find]
where = ["."]
include = ["my_flat_project*"]
```
