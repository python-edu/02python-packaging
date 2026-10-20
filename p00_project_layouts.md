# Python project layouts

All three layouts support scripts, modules and notebooks.
The examples include optional files; minimal generator output is listed below.

## 1. Simple project

For quick experiments, data analysis and small scripts.

```text
my_project/
├── env/
├── data/
├── notebooks/
│   └── analysis.ipynb
├── program.py
├── module1.py
├── module2.py
├── requirements.txt
└── .gitignore
```

- `env` - virtual environment
- `data` - input and output data
- `notebooks` - Jupyter notebooks
- `program.py` - main Python script
- `module1.py`, `module2.py` - modules used by the script
- `requirements.txt` - list of required Python packages
- `.gitignore` - files excluded from Git

Characteristics:

- scripts can import modules located in the same directory
- no packaging configuration is needed
- not configured as an installable Python package

---

## 2. Flat-layout project

For small installable Python programs and reusable code.

```text
my_project/
├── env/
├── data/
├── notebooks/
│   └── analysis.ipynb
├── my_project/
│   ├── __init__.py
│   ├── cli.py
│   ├── module1.py
│   └── module2.py
├── pyproject.toml
├── README.md
└── .gitignore
```

- `my_project` - Python package containing source code
- `__init__.py` - marks the directory as a regular Python package
- `cli.py` - main program module with a `main()` function
- `module1.py`, `module2.py` - modules used by the program
- `pyproject.toml` - project configuration and installation information
- `README.md` - project description

Characteristics:

- Python package is located directly in the project directory
- can be installed using `pip`
- package can usually be imported directly from the project directory

---

## 3. Src-layout project

For reusable packages and further development.

```text
my_project/
├── env/
├── data/
├── notebooks/
│   └── analysis.ipynb
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── cli.py
│       ├── module1.py
│       └── module2.py
├── pyproject.toml
├── README.md
└── .gitignore
```

- `src` - directory containing the Python package
- other files - as in flat-layout

Characteristics:

- the package normally needs to be installed before importing it from the project directory
- helps avoid accidental imports directly from the source directory
- recommended for developing installable Python packages

---

## Comparison

| Feature | simple | flat | src |
|---|---|---|---|
| Installable Python package | No | Yes | Yes |
| `pyproject.toml` | No | Yes | Yes |
| Separate source directory | No | No | Yes |
| Suitable for distribution | Limited | Yes | Yes |

---

## Minimal generated structures

| Type | Directories and files |
|---|---|
| `simple` | `data/`, `notebooks/` |
| `flat` | package directory with an empty `__init__.py`, `pyproject.toml`, `README.md` |
| `src` | package directory inside `src/` with an empty `__init__.py`, `pyproject.toml`, `README.md` |

All types also include `env/` unless `-n` is used.

## Package directory names

- valid Python identifier
- no hyphens or Python keywords such as `class`
- may differ from the project directory name

Example:

```text
project-20261017164310/
└── src/
    └── project_20261017164310/
        └── __init__.py
```

---

## Environment and Git

- use a separate virtual environment for each project
- exclude virtual environments from Git
