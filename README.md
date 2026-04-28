# python-devcontainer-template

A minimal, reproducible Python dev container template using **[uv](https://github.com/astral-sh/uv)** for dependency management. Clone once, use forever.

---

## What's Included

| File | Purpose |
|---|---|
| `.devcontainer/devcontainer.json` | VS Code container config, extensions, settings |
| `.devcontainer/Dockerfile` | Python 3.12 slim + uv |
| `.devcontainer/docker-compose.yml` | Workspace bind mount |
| `.devcontainer/postCreate.sh` | Runs `uv sync` and verifies the environment on first open |
| `pyproject.toml` | Project metadata and dependency config |
| `.python-version` | Pins Python 3.12 for uv |
| `.gitignore` | Comprehensive Python gitignore including `.venv/` and `.env` |
| `.env.example` | Template for environment variables — copy to `.env` and populate |

---

## Using This Template

### 1. Create a new repo from this template

On GitHub, click **"Use this template"** → **"Create a new repository"**.

Do not fork — the template feature gives you a clean commit history with no connection back to this repo.

### 2. Open in dev container

Open the repo in VS Code and select **"Reopen in Container"** when prompted, or via the Command Palette: `Dev Containers: Reopen in Container`.

The container will build and `postCreate.sh` will run `uv sync` automatically. A successful build looks like this:

```
▶ Installing dependencies...
▶ Verifying environment...
Python 3.12.x
✅ Environment ready.
```

> **Note:** After the build, the integrated terminal may show `/bin/sh: 1: source: not found`. This is not an error — the terminal defaults to `sh`, which doesn't support `source`. You never need to activate the venv manually; use `uv run` instead.

### 3. Rename the project

Update the `name` field in `pyproject.toml`:

```toml
[project]
name = "your-project-name"
```

### 4. Commit the lockfile

`uv sync` generates `uv.lock` on first run. Commit it so all future installs are deterministic:

```bash
git add uv.lock
git commit -m "chore: add uv lockfile"
```

---

## Adding Dependencies

```bash
# Add a runtime dependency
uv add polars

# Add a dev-only dependency
uv add --dev pytest ruff

# Sync after manually editing pyproject.toml
uv sync
```

---

## Environment Variables

Copy `.env.example` to `.env` and populate as needed:

```bash
cp .env.example .env
```

`.env` is gitignored and will never be committed. `.env.example` is the source of truth for what variables the project expects — keep it up to date as the project grows.

---

## How the venv Works

The virtual environment is written directly into the workspace bind mount at `.venv/`. It is gitignored and rebuilt automatically when the container is created.

> **Do not add a named Docker volume for `.venv/`.** A named volume mounts over the workspace bind mount and persists stale environments across rebuilds — `Rebuild Container` will not clear it. The bind mount approach means the venv is always in sync with your `uv.lock`.

To verify the environment is wired correctly after a build:

```bash
uv run python -c "import sys; print(sys.executable)"
# expected: /workspace/.venv/bin/python3
```

---

## Requirements

- **[Docker Desktop](https://www.docker.com/products/docker-desktop/)**
- **[VS Code](https://code.visualstudio.com/)** with the **[Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)**
