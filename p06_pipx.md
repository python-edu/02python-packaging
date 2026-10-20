# Install the generator with `pipx`

## What is `pipx`?

`pipx` is used to install Python **command-line applications**.

It installs each application in its own isolated virtual environment and makes its terminal commands available on the system `PATH`.

For example, after installing `projectgen` with `pipx`, we can run:

```bash
projectgen
```

from any directory without activating the project environment.

Official documentation:

- https://pipx.pypa.io/
- https://pipx.pypa.io/stable/reference/examples.html

---

## Which project layouts can be installed with `pipx`?

`pipx` does not require a specific project layout.

It can install a project if:

- the project is installable,
- it has packaging configuration such as `pyproject.toml`,
- it provides a terminal command, for example with `[project.scripts]`.

Therefore:

| Project type | `pipx` |
|---|---|
| simple without `pyproject.toml` | No |
| simple with `pyproject.toml` and an entry point | Yes |
| flat | Yes |
| src | Yes |

`pipx` is intended for applications used from the terminal, not for libraries imported by other Python programs.

The final version of `projectgen` uses the `src` layout, so we will install that version.

---

## 1. Check the entry point

In the final `projectgen-src/pyproject.toml` we already have:

```toml
[project.scripts]
projectgen = "cli:main"
```

The entry point connects the terminal command:

```text
projectgen
```

with the Python function:

```text
cli.main
```

---

## 2. Install the final generator

Open a terminal in:

```text
projectgen-src/
```

Install the project with:

```bash
pipx install .
```

`pipx` creates its own virtual environment for `projectgen`.

The `env/` directory inside the source project is not used by the installed application.

---

## 3. Check the installation

List applications installed with `pipx`:

```bash
pipx list
```

Check the generator command:

```bash
projectgen -h
```

The command should be available from any directory.

---

## 4. Test the installed generator

Move to a directory outside `projectgen-src`.

Create a project:

```bash
projectgen test-pipx -n
```

Because `src` is the default project type, the generated project should contain:

```text
src/test_pipx/
pyproject.toml
README.md
```

Check another project type:

```bash
projectgen test-flat -t flat -n
```

The same installed application can create all project types supported by the generator.

---

## 5. Remove the application

To remove the installed generator:

```bash
pipx uninstall projectgen
```

This removes the isolated environment created by `pipx` and the `projectgen` command.

The source project is not removed.
