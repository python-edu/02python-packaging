# Install the simple generator

## 1. Generate a separate project

Open a terminal in `projectgen-simple/`.

```bash
python cli.py projectgen-simple-toml -d ..
```

The working generator creates `../projectgen-simple-toml/env/`.
If an old environment is active, run `deactivate` before switching projects.

```bash
cd ../projectgen-simple-toml
```

Copy these files from `projectgen-simple/` into the new project root:

```text
cli.py
environment.py
myparser.py
naming.py
structure.py
```

Keep the previous project. Do not copy `env/`.

## 2. Add `pyproject.toml` manually

Copy the complete simple configuration from `p02_toml.md` into
`projectgen-simple-toml/pyproject.toml`.
The Python modules remain unchanged.

## 3. Activate and install

Windows PowerShell:

```powershell
.\env\Scripts\activate
```

Linux / macOS:

```bash
source env/bin/activate
```

```bash
python -m pip install -e .
```

Editable installation uses the source files in this project.
Changes to Python code are available on the next run; changes to entry points or
packaging configuration require installation again.

## 4. Compare execution

From `projectgen-simple-toml/`:

```bash
python cli.py -h
python -m cli -h
projectgen -h
```

Move outside the source directory while keeping its environment active:

```bash
cd ..
projectgen test_installed -n --data
python -m cli -h
```

Both the installed command and installed module work from here.
`test_installed/` contains only `data/`.

```bash
python cli.py -h
```

This fails if the parent directory has no `cli.py`: file execution still needs the correct path.

Return to the generator:

```bash
cd projectgen-simple-toml
```

## 5. Observe editable mode

In `cli.py`, temporarily change `Created simple project:` to `READY:`.

```bash
projectgen test_editable -n
```

The changed message appears without reinstalling. Restore the original message.

The installed program is still five top-level modules. The next stage groups them into a package.
