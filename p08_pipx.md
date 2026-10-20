# Install the generator with pipx

pipx is already installed.
It gives the application its own environment and exposes its terminal command.
You do not activate that environment to run the application.

## 1. Install the wheel

Start in `projectgen-src/`, with no project environment active.
If needed, run `deactivate` first.

```bash
pipx install dist/projectgen-0.1.0-py3-none-any.whl
pipx list
```

The entry point in the generator's TOML is:

```toml
[project.scripts]
projectgen = "projectgen.cli:main"
```

Alternative: `pipx install .` builds and installs directly from the source project.
Choose one installation route. Both install the same application.
If projectgen is already installed with pipx, use `pipx reinstall projectgen`
to reinstall from its recorded source, or uninstall it before choosing a different source.

## 2. Test outside the source directory

```bash
cd ..
projectgen -h
projectgen test_pipx_src -n
projectgen test_pipx_flat -t flat -n --data
projectgen test_pipx_simple -t simple -n --notebooks
projectgen test_pipx_packages -p requests
```

Check the selected layouts, optional directories and the installed package in
`test_pipx_packages/env/`. The generator environment itself is not activated.

pipx's application environment and each generated project's `env/` are separate.
Selected libraries are installed into the generated project, not into pipx's environment.

## 3. Compare installations

| Installation | Environment | Source edits visible immediately? |
|---|---|---|
| `pip install -e .` | Currently selected Python environment | Yes, for Python code |
| `pip install .` or a wheel | Currently selected Python environment | No |
| `pipx install .` or a wheel | Separate pipx environment | No |

pipx can install the earlier simple+TOML version too: it requires an installable application
with a terminal command, not a particular source layout.

## 4. Optional removal

```bash
pipx uninstall projectgen
```

This removes the pipx installation. Source projects and generated projects remain.

Reference: [pipx](https://pipx.pypa.io/).
