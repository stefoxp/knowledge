# Advanced Visual Studio Code for Python Developers

- [https://realpython.com/advanced-visual-studio-code-python/](https://realpython.com/advanced-visual-studio-code-python/)

## Team Settings vs Personal Settings

### Global User Settings for Python

#### Install pipx

- [https://pypa.github.io/pipx/installation/](https://pypa.github.io/pipx/installation/)

```bash
# requires pip 19.0 or later
python3 -m pip install --user pipx
python3 -m pipx ensurepath
```

#### Install common tools for the user

```bash
pipx install pylint && \
  pipx install black && \
  pipx install poetry && \
  pipx install pipenv && \
  pipx install bandit && \
  pipx install mypy && \
  pipx install flake8
```

#### Configure Preferences in VS Code or Codium

Preferences: Open Settings (JSON)

Add settings for the preferences you want to apply to all projects:

```json
"python.pipenvPath": "${env:HOME}/.local/bin/pipenv",
"python.poetryPath": "${env:HOME}/.local/bin/poetry",
"python.condaPath": "${env:HOME}/.local/bin/conda",
"python.linting.enabled": true,
"python.linting.banditPath": "${env:HOME}/.local/bin/bandit",
"python.linting.banditEnabled": true,
"python.linting.pylintPath": "${env:HOME}/.local/bin/pylint",
"python.linting.mypyPath": "${env:HOME}/.local/bin/mypy",
"python.linting.flake8Path": "${env:HOME}/.local/bin/flake8",
"python.formatting.blackPath": "${env:HOME}/.local/bin/black",
```

### Workspace Settings

Outside of the user settings, you can use the .vscode/ directory inside the workspace to configure some project-specific settings:

- Which commands to run to execute the project
- How to test and debug the project
- Which linter and formatter to use and any project-specific parameters

