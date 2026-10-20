# Simple Python project generator

## Goal

Create a program that generates this minimal project structure:

```text
my_project/
├── env/
├── data/
└── notebooks/
```

- `env` - virtual environment with Python and `pip`; omitted with `-n`
- `data` - empty directory for data
- `notebooks` - empty directory for notebooks

## 1. Generator structure

Create this structure:

```text
example_project/
├── env/
├── data/
├── notebooks/
├── cli.py
├── myparser.py
├── naming.py
├── structure.py
└── environment.py
```

Create its environment from `example_project`:

```bash
python -m venv env
```

- `cli.py` - `main()` function connecting the other functions
- `myparser.py` - command-line argument parser
- `naming.py` - default project name
- `structure.py` - directory creation
- `environment.py` - virtual environment creation

Copy the code below into the corresponding files.

## 2. Project name

Create `naming.py`:

```python
from datetime import datetime


def default_name():
    return 'project-' + datetime.now().strftime('%Y%m%d%H%M%S')
```

Example: `project-20261017164310`.

## 3. Directory creation

Create `structure.py`:

```python
def create_simple(project):
    project.mkdir()
    (project / 'data').mkdir()
    (project / 'notebooks').mkdir()
```

`mkdir()` stops with an error if the project directory already exists.

## 4. Virtual environment

Create `environment.py`:

```python
import venv


def create_environment(project):
    venv.create(project / 'env', with_pip=True)
```

- `with_pip=True` - include `pip`
- activation is not required to create an environment

## 5. Command-line arguments

Create `myparser.py`:

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

- `parse_arguments()` - return command-line arguments
- `-h`, `--help` - provided automatically by `argparse`

## 6. Main function

Create `cli.py`:

```python
from pathlib import Path

from environment import create_environment
from myparser import parse_arguments
from naming import default_name
from structure import create_simple


def main():
    args = parse_arguments()
    name = args.name or default_name()
    project = Path(args.directory).expanduser().resolve() / name

    create_simple(project)

    if not args.no_venv:
        create_environment(project)

    print(f'Created simple project: {project}')


if __name__ == '__main__':
    main()
```

Use a simple directory name such as `test_simple` and an existing parent directory.

## 7. Running the generator

Open a terminal in `example_project` and display help:

```bash
python cli.py -h
```

Python finds the imported modules next to `cli.py`.

Compare this with:

```bash
python -m cli -h
```

Both commands work from `example_project`:

| Command | How Python finds the code |
|---|---|
| `python cli.py` | File path |
| `python -m cli` | Module name, without `.py` |

For this stage, use `python cli.py`.

## 8. Test the generator

Run these commands from `example_project`.

### Default project

```bash
python cli.py
```

Check that a directory named `project-YYYYMMDDHHMMSS` contains `env`, `data` and `notebooks`.

### Selected name

```bash
python cli.py test_simple
```

Check the same structure inside `test_simple`.

### Without a virtual environment

```bash
python cli.py test_no_env -n
```

Expected structure:

```text
test_no_env/
├── data/
└── notebooks/
```

### Another location

```bash
python cli.py test_location -d ..
```

The project is created in the parent directory of `example_project`.

### Existing project

Repeat the earlier command:

```bash
python cli.py test_simple
```

Expected result: `FileExistsError`. The existing project is not overwritten.

## 9. Running from another directory

From `example_project`, move to its parent directory:

```bash
cd ..
python example_project/cli.py -h
```

Expected: help is displayed. Imports still work.
Use quotes for paths containing spaces.

Now compare:

```bash
python -m cli -h
```

Expected in a clean environment: `No module named cli`.

Create a project using the script path:

```bash
python example_project/cli.py test_from_parent -n
```

Expected: `test_from_parent` next to `example_project`.
Default output location: current terminal directory.

Return to the generator directory:

```bash
cd example_project
```

