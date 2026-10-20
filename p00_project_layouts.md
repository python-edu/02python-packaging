# Python project layouts

## Simple

```text
my_project/
├── env/
├── cli.py
├── module1.py
└── module2.py
```

- scripts and modules in the project root
- run directly: `python cli.py`
- can also be installed after adding a suitable `pyproject.toml`

## Flat

```text
my_project/
├── env/
├── my_project/
│   ├── __init__.py
│   ├── cli.py
│   ├── module1.py
│   └── module2.py
├── pyproject.toml
└── README.md
```

- related modules grouped in one package
- `__init__.py` marks a regular Python package
- package directly under the project root
- package can be imported from the project root even before installation

## Src

```text
my_project/
├── env/
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── cli.py
│       ├── module1.py
│       └── module2.py
├── pyproject.toml
└── README.md
```

- package under `src/`
- normally requires installation before importing from the project root
- helps avoid accidentally using the source directory instead of the installed package

## Comparison

| Feature | simple | flat | src |
|---|---|---|---|
| Source location | Project root | Package in root | Package under `src/` |
| One package namespace | No | Yes | Yes |
| Installable with suitable TOML | Yes | Yes | Yes |
| Typical use | Small scripts | Reusable code / applications | Reusable code / applications |



## Generator output

| Type | Minimum output |
|---|---|
| simple | Empty project directory |
| flat | Package with empty `__init__.py` and skeleton `cli.py`; TOML and README |
| src | The same package under `src/`; TOML and README |

- `env/`: created by default; skip with `-n`
- `data/`: add with `--data`
- `notebooks/`: add with `--notebooks`
- notebook and data files are added by the user
- additional source modules in the diagrams above are examples, not generated files

Use a separate environment for each project. Keep `env/`, `build/`, `dist/`,
`*.egg-info/` and `__pycache__/` out of Git.
