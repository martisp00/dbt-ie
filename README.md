# dbt-ie Repository

## Overview

This repository contains the necessary files and configurations for working with dbt (Data Build Tool) projects using Polars, dbt-core, and DuckDB. The following instructions will guide you on how to set up and use this repository locally using `uv` as the package manager.

---

## Prerequisites

Before starting, make sure the following are installed on your machine. Instructions differ slightly between **macOS** and **Windows** — follow the section that matches your OS.

### Common requirements (both OSes)

- **Python 3.10, 3.11, or 3.12** (DuckDB 0.10.2 doesn't ship wheels for 3.13 yet — `uv sync` will enforce this). If you already have a newer Python, don't worry: `uv` can install a compatible one automatically.
- **Git** (for cloning the repository)
- **A code editor** — we recommend [VSCode](https://code.visualstudio.com/)
- **`uv` package manager**

### macOS setup

1. **Open Terminal** (⌘ + Space → type `Terminal`).
2. **Install Homebrew** (if you don't have it) — a package manager for macOS:

    ```bash
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```

3. **Install Python and Git**:

    ```bash
    brew install python git
    ```

4. **Install `uv`**:

    ```bash
    curl -LsSf https://astral.sh/uv/install.sh | sh
    ```

    Restart your terminal, then verify:

    ```bash
    uv --version
    ```

### Windows setup

We strongly recommend using **PowerShell** (not Command Prompt) for the commands below. Search for "PowerShell" in the Start menu and open it.

1. **Install Python**:
    - Download from [python.org/downloads](https://www.python.org/downloads/).
    - During installation, **check the box "Add Python to PATH"**. This is critical.

2. **Install Git**:
    - Download from [git-scm.com/download/win](https://git-scm.com/download/win).
    - Accept the default options during installation.

3. **Install `uv`** (in PowerShell):

    ```powershell
    powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
    ```

    Close and reopen PowerShell, then verify:

    ```powershell
    uv --version
    ```

---

## Setup Instructions

The commands below are identical on Mac and Windows **unless otherwise noted**. Where commands differ, both versions are shown.

### 1. Clone the Repository

Clone the course repository to your local machine:

```bash
git clone https://github.com/dgarhdez/dbt-ie.git
cd dbt-ie
```

### 2. Install the Project with `uv sync`

All project dependencies are declared in `pyproject.toml`. A single command sets everything up:

```bash
uv sync
```

This will:

1. Create a virtual environment at `.venv/` in the project folder.
2. Resolve every dependency (including transitive ones) from `pyproject.toml`.
3. Write a `uv.lock` file pinning the exact versions — so everyone in the class gets the *same* environment.
4. Install everything into `.venv/`.

Among the packages installed:

- **dbt-core** and **dbt-duckdb** — the dbt library and the DuckDB adapter.
- **DuckDB** — an in-process SQL OLAP database for analytics.
- **Polars** — a fast DataFrame library, used for dbt Python models.
- **sqlfluff** — SQL linter.

> If you ever need to add or remove a package later, use `uv add <package>` or `uv remove <package>`. Don't install things with plain `pip` — it won't update `pyproject.toml`.

### 3. Activate the Virtual Environment

The activation command **differs by OS**:

**macOS / Linux (Terminal):**

```bash
source .venv/bin/activate
```

**Windows (PowerShell):**

```powershell
.\.venv\Scripts\Activate.ps1
```

> **Windows note:** If you see an error about execution policies, run this once in PowerShell and try again:
> ```powershell
> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
> ```

**Windows (Command Prompt, if you must):**

```cmd
.venv\Scripts\activate.bat
```

Once activated, you should see `(dbt-ie)` at the beginning of your terminal prompt.

> **Tip:** If you'd rather not activate every time, prefix any command with `uv run` and it will execute inside the project's environment automatically — e.g. `uv run dbt debug`, `uv run python create_db.py`.

### 4. Configure dbt

Ensure you have a `profiles.yml` file in the `.dbt` directory. This file should contain your dbt configurations for DuckDB. Example:

```yaml
default:
  outputs:
    dev:
      type: duckdb
      path: my_database.duckdb
      schema: main
  target: dev
```

### 5. Load Data into DuckDB

The `data` folder contains sample Parquet files and a Python script to create and populate the DuckDB database.

Run the script to create the database:

```bash
python create_db.py
```

This will create `my_database.duckdb` in the project root and load the data from the Parquet files into tables.

### 6. Run dbt Commands

You can now use dbt commands to build and test your project. For example:

```bash
dbt run
dbt test
dbt debug  # To check connection
```

### 7. VSCode dbt Power User Extension

To enhance your dbt development experience, consider installing the [VSCode dbt Power User](https://marketplace.visualstudio.com/items?itemName=calogica.vscode-dbt-power-user) extension. This extension provides features like syntax highlighting, autocompletion, and snippets for dbt projects.

---

## Troubleshooting

### macOS

- **`command not found: uv`** — Restart your terminal after installing `uv`, or add `~/.local/bin` to your `PATH`.
- **`xcrun: error: invalid active developer path`** — Install Xcode command line tools: `xcode-select --install`.
- **Permission denied when activating venv** — Make sure you ran `uv sync` inside the project folder and are using `source .venv/bin/activate` (not just running the script).

### Windows

- **`uv` / `python` / `git` is not recognized** — The installer didn't add it to `PATH`. Reinstall Python making sure to check **"Add Python to PATH"**, then restart PowerShell.
- **`cannot be loaded because running scripts is disabled on this system`** — Run `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` in PowerShell, confirm with `Y`, then retry.
- **Long path errors when cloning** — Enable long paths in Git: `git config --global core.longpaths true`.
- **Commands from the class slides don't work** — The slides use macOS/Linux syntax. Replace `source venv/bin/activate` with `.\venv\Scripts\Activate.ps1`, and `python` with `py` if needed.

---

## Notes

- Always ensure your virtual environment is activated before running any commands — you should see `(dbt-ie)` at the start of your prompt. (Or skip activation entirely and prefix commands with `uv run`.)
- Dependencies live in `pyproject.toml`; the resolved lockfile is `uv.lock`. Use `uv add` / `uv remove` to change them — don't edit either file by hand unless you know what you're doing.
- If you encounter issues with `uv`, refer to its [documentation](https://docs.astral.sh/uv/) or run `uv --help`.
- Windows users: consider installing [Windows Terminal](https://apps.microsoft.com/detail/9n0dx20hk701) from the Microsoft Store for a nicer PowerShell experience.

---

## Contributing

Feel free to submit issues or pull requests to improve this repository.

---

## License

This project is licensed under the MIT License.
