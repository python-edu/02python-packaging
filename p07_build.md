# Build and install a wheel

## 1. Why build?

| Command | Purpose |
|---|---|
| `python -m pip install -e .` | Work on the source through an editable installation |
| `python -m pip install .` | Build and install a regular copy of the project |
| `python -m build` | Create distributable files in `dist/` |
| `python -m pip install file.whl` | Install an already built distribution |

`pip install .` already builds a wheel for installation; it does not normally leave
a deliverable wheel in your `dist/` directory.
`build` prepares files you can keep, transfer and install later without the source directory.
For this pure-Python project, building packages code and metadata; it does not create a standalone executable.

- wheel (`.whl`): built distribution, ready for installation
- sdist (`.tar.gz`): source distribution, requiring a build before installation

## 2. Build the final generator

Start in `projectgen-src/`, with its environment active.

```bash
python -m pip install build
python -m build
```

By default, `build` creates an sdist and then builds a wheel from that sdist.
It uses an isolated build environment with the backend specified in TOML.

Expected output:

```text
dist/
├── projectgen-0.1.0.tar.gz
└── projectgen-0.1.0-py3-none-any.whl
```

To build only a wheel directly from the project:

```bash
python -m build --wheel
```

The filename uses the version from `pyproject.toml`. Rebuild after source changes;
update the version when distributing a new release.
The wheel contains the package and installation metadata, not your virtual environment.

## 3. Install the wheel in a separate environment

From `projectgen-src/`:

```bash
deactivate
cd ..
mkdir wheel-test
cd wheel-test
python -m venv env
```

Activate `wheel-test/env/`:

Windows PowerShell:

```powershell
.\env\Scripts\Activate.ps1
```

Linux / macOS:

```bash
source env/bin/activate
```

```bash
python -m pip install ../projectgen-src/dist/projectgen-0.1.0-py3-none-any.whl
projectgen -h
projectgen test_wheel -n --data
python -c "import projectgen; print(projectgen.__file__)"
```

- `test_wheel/` has src layout and `data/`
- imported code is inside `wheel-test/env/`, not `projectgen-src/src/`
- editing the source no longer changes this installed copy

```bash
deactivate
cd ../projectgen-src
```

The wheel remains ready for pipx installation.

References: [build](https://build.pypa.io/en/stable/),
[pip install](https://pip.pypa.io/en/stable/cli/pip_install/).
