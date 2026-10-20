# Simple Python project generator

```text
projectgen-simple/
├── env/
├── naming.py
├── structure.py
├── environment.py
├── myparser.py
└── cli.py
```

## 1. Create the first project manually

Start in a directory for the course exercises:

```bash
mkdir projectgen-simple
cd projectgen-simple
python -m venv env
```

Create the five files below in `projectgen-simple/`.
The generator creates an empty project and, by default, its `env/`.
Optional flags add `data/` and `notebooks/`.

## 2. Copy the code

### `naming.py`

```python
from datetime import datetime
import keyword
import re


def default_name():
    return 'project-' + datetime.now().strftime('%Y%m%d%H%M%S')


def project_name(value):
    if not re.fullmatch(r'[A-Za-z][A-Za-z0-9_-]*', value):
        raise ValueError('Use a letter first, then letters, digits, - or _.')
    package = value.replace('-', '_')
    if keyword.iskeyword(package) or package.lower() in {'env', 'data', 'notebooks'}:
        raise ValueError('This name is reserved; choose another project name.')
    return value


def package_name(value):
    return value.replace('-', '_')
```

### `structure.py`

```python
def create_simple(project):
    project.mkdir()


def create_optional_directories(project, data, notebooks):
    if data:
        (project / 'data').mkdir()
    if notebooks:
        (project / 'notebooks').mkdir()
```

### `environment.py`

```python
import venv


def create_environment(project):
    venv.create(project / 'env', with_pip=True)
```

### `myparser.py`

```python
import argparse

from naming import project_name


def parse_arguments():
    parser = argparse.ArgumentParser(description='Create a Python project')
    parser.add_argument(
        'name', nargs='?', type=project_name,
        help='Project name (default: project-YYYYMMDDHHMMSS)'
    )
    parser.add_argument(
        '-d', '--directory', default='.',
        help='Existing parent directory (default: current directory)'
    )
    parser.add_argument(
        '-n', '--no-venv', action='store_true',
        help='Do not create a virtual environment'
    )
    parser.add_argument('--data', action='store_true', help='Create data directory')
    parser.add_argument(
        '--notebooks', action='store_true', help='Create notebooks directory'
    )

    return parser.parse_args()
```

### `cli.py`

```python
from pathlib import Path

from environment import create_environment
from myparser import parse_arguments
from naming import default_name
from structure import create_optional_directories, create_simple


def main():
    args = parse_arguments()
    name = args.name or default_name()
    project = Path(args.directory).expanduser().resolve() / name

    create_simple(project)
    create_optional_directories(project, args.data, args.notebooks)
    if not args.no_venv:
        create_environment(project)

    print(f'Created simple project: {project}')


if __name__ == '__main__':
    main()
```

`argparse` provides `-h` automatically. The naming helper rejects unsupported names.
`mkdir()` raises `FileExistsError` if the project already exists.
`venv.create(..., with_pip=True)` creates an environment with pip; activation is unnecessary for creation.

## 3. Compare execution from the project directory

```bash
python cli.py -h
python -m cli -h
```

| Command | Finds the program by |
|---|---|
| `python cli.py` | File path |
| `python -m cli` | Module name |

Both work from `projectgen-simple/`. Use `python cli.py` for this stage.

## 4. Test generation

Run from `projectgen-simple/`:

```bash
python cli.py
python cli.py test_simple --data --notebooks
python cli.py test_empty -n
python cli.py test_location -d .. -n --data
```

| Project | Expected contents |
|---|---|
| `project-YYYYMMDDHHMMSS` | `env/` |
| `test_simple` | `env/`, `data/`, `notebooks/` |
| `test_empty` | Empty directory |
| `../test_location` | `data/` |

Repeat `python cli.py test_simple`: expect `FileExistsError`; existing files remain unchanged.
Run `python cli.py "my project" -n`: expect an argument error and no project directory.

## 5. Run from another directory

```bash
cd ..
python projectgen-simple/cli.py -h
python -m cli -h
```

- script path: works; imports are found next to `cli.py`
- module name: in a clean environment, `No module named cli`

```bash
python projectgen-simple/cli.py test_from_parent -n
```

`test_from_parent/` appears in the current terminal directory.
Quote paths containing spaces.

Return to the generator:

```bash
cd projectgen-simple
```
