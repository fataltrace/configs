# Шаги

## 1. Создание правил
- Найти или написать `.cursorrules`, соответствующие используемой технологии и задаче.

## 2. Описание проекта
- В свободной форме описать проект.
- Попросить нейросеть формализовать описание в виде `project.md`.

## 3. Верификация задачи
- Задать нейросети вопрос:  
  *«Посмотри `project.md`. Всё ли в нём понятно? Какие есть вопросы?»*
- При необходимости дополнить описание и повторить проверку.

> Посмотри `@project.md`, всё ли в нём понятно?  
> Задай уточняющие вопросы.  
> Затем распиши необходимые проектные роли и создай для каждой роли отдельный `.md` файл с её задачами.

> Отлично! Сейчас я отвечу на вопросы, тебе нужно будет внести изменения в соответствии с моими ответами  
> в файлы проектных ролей и в сам @project.md 

## 4. Определение ролей и задач
- Спросить нейросеть:  
  *«Какие проектные роли нужны для реализации?»*
- Записать каждую роль и связанные задачи в отдельные `.md` файлы.

## 5. Планирование очередности
- Попросить нейросеть определить порядок выполнения задач для каждой роли.
- Последовательно выполнять задачи, передавая нейросети:
  - файл роли,
  - `.cursorrules`,
  - `project.md`.

---

# .cursorrules

# Python CLI Application Development Rules (2025)

Here are some best practices and rules you MUST FOLLOW:

0. **Use ONLY ENGLISH**: in the project.
1. **Use Meaningful Names**: Choose descriptive variable, function, and class names.
2. **Follow PEP 8**: Adhere to the Python Enhancement Proposal 8 style guide for formatting.
3. **Use Docstrings**: Document functions and classes with docstrings to explain their purpose.
4. **Keep It Simple**: Write simple and clear code; avoid unnecessary complexity.
5. **Use List Comprehensions**: Prefer list comprehensions for creating lists over traditional loops when appropriate.
6. **Handle Exceptions**: Use try-except blocks to handle exceptions gracefully.
7. **Use Virtual Environments**: Isolate project dependencies using virtual environments (e.g., `venv`).
8. **Write Tests**: Implement unit tests to ensure code reliability.
9. **Use Type Hints**: Utilize type hints for better code clarity and type checking.
10. **Avoid Global Variables**: Limit the use of global variables to reduce side effects.

## Project Structure Standards

### Use Modern Python Project Layout
- **ALWAYS** use `src/` layout instead of flat structure
- Place source code in `src/package_name/` directory
- Use `pyproject.toml` as the single configuration file
- Follow PEP 518/621 standards for project configuration

### CLI Application Structure
```
mycli/
├── pyproject.toml              # Single config file (replaces setup.py, setup.cfg)
├── README.md
├── .gitignore
├── .env.example
├── uv.lock                     # If using uv package manager
├── src/
│   └── mycli/
│       ├── __init__.py         # Package version
│       ├── main.py             # Main entry point
│       ├── cli/
│       │   ├── __init__.py
│       │   ├── app.py          # Main CLI app
│       │   ├── commands/       # Individual commands
│       │   │   ├── __init__.py
│       │   │   ├── init.py
│       │   │   ├── build.py
│       │   │   └── deploy.py
│       │   ├── options.py      # Common options
│       │   └── utils.py        # CLI utilities
│       ├── core/
│       │   ├── __init__.py
│       │   ├── config.py       # Configuration management
│       │   ├── exceptions.py   # Custom exceptions
│       │   └── models.py       # Data models
│       ├── services/           # Business logic
│       │   ├── __init__.py
│       │   ├── file_service.py
│       │   └── api_service.py
│       └── utils/
│           ├── __init__.py
│           ├── validators.py
│           ├── formatters.py
│           └── helpers.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py            # Pytest configuration
│   ├── test_main.py
│   ├── cli/
│   │   └── commands/
│   │       ├── test_init.py
│   │       └── test_build.py
│   └── fixtures/              # Test data
└── docs/
```

## Technology Stack (2025 Standards)

### Required Dependencies
- **Typer**: Modern CLI framework (preferred over Click)
- **Rich**: Beautiful terminal output with colors, progress bars, tables
- **Pydantic**: Data validation and settings management
- **Pydantic-Settings**: Environment-based configuration
- **HTTPX**: Modern HTTP client (preferred over requests)

### Development Tools
- **uv**: Fastest package manager (preferred over pip/poetry)
- **ruff**: All-in-one linter and formatter (replaces black + flake8)
- **mypy**: Type checking
- **pytest**: Testing framework
- **pre-commit**: Git hooks for code quality

### Package Manager Priority
1. **uv** (fastest, recommended for new projects 2025)
2. Poetry (mature, stable)
3. PDM (good alternative)

## Code Style and Standards

### Use Modern Python Features
- **Minimum Python version**: 3.13+
- **Always use type hints** for function parameters and return types
- Use `pathlib.Path` instead of `os.path`
- Prefer f-strings over `.format()` or `%` formatting
- Use dataclasses or Pydantic models for data structures

### CLI Framework Rules
- Use **Typer** for new CLI applications
- Structure commands in separate modules under `cli/commands/`
- Use Rich for all terminal output (colors, progress bars, tables)
- Implement proper error handling with custom exceptions
- Add `--verbose` and `--help` options to all commands

### Configuration Management
- Use **Pydantic Settings** for configuration
- Support both environment variables and config files (YAML/TOML)
- Use `APP_` prefix for environment variables
- Provide sensible defaults for all configuration options

## Code Patterns

### Main Entry Point Pattern
```python
# src/your_cli/main.py
import sys
import typer
from rich.console import Console
from rich.traceback import install

from your_cli.cli.app import app
from your_cli.core.exceptions import YourCLIError

install(show_locals=True)  # Rich tracebacks
console = Console()

def main() -> None:
    try:
        app()
    except YourCLIError as e:
        console.print(f"[red]Error:[/red] {e}")
        sys.exit(1)
    except KeyboardInterrupt:
        console.print("\n[yellow]Operation cancelled[/yellow]")
        sys.exit(130)

if __name__ == "__main__":
    main()
```

### Command Structure Pattern
```python
# src/your_cli/cli/commands/build.py
import typer
from rich.console import Console
from rich.progress import track

console = Console()
app = typer.Typer()

@app.command()
def all(
    ctx: typer.Context,
    clean: bool = typer.Option(False, "--clean", help="Clean before build"),
    verbose: bool = typer.Option(False, "--verbose", help="Verbose output"),
) -> None:
    """Build the entire project."""
    console.print("🔨 [bold]Building project[/bold]")
    
    tasks = ["Task 1", "Task 2", "Task 3"]
    for task in track(tasks, description="Building..."):
        # Your build logic here
        pass
    
    console.print("✅ [green]Build completed![/green]")
```

### Configuration Pattern
```python
# src/your_cli/core/config.py
from pathlib import Path
from typing import Optional
from pydantic import BaseModel, Field
from pydantic_settings import BaseSettings

class BuildConfig(BaseModel):
    target: str = Field(default="dist/", description="Build target")
    clean: bool = Field(default=True, description="Clean before build")

class Settings(BaseSettings):
    model_config = SettingsConfigDict(
        env_prefix="YOURCLI_",
        env_file=".env",
        case_sensitive=False,
    )
    
    name: str = Field(default="my-project")
    version: str = Field(default="1.0.0")
    build: BuildConfig = Field(default_factory=BuildConfig)
```

## Testing Standards

### Test Structure
- Place tests in `tests/` directory
- Mirror source structure in test directory
- Use `conftest.py` for shared fixtures
- Test CLI commands using `typer.testing.CliRunner`

### Test Patterns
```python
# tests/cli/commands/test_build.py
import pytest
from typer.testing import CliRunner
from your_cli.cli.commands.build import app

def test_build_command(runner: CliRunner):
    result = runner.invoke(app, ["all"])
    assert result.exit_code == 0
    assert "Build completed!" in result.stdout
```

## Error Handling

### Custom Exceptions
```python
# src/your_cli/core/exceptions.py
class YourCLIError(Exception):
    """Base exception for your CLI."""
    pass

class ConfigError(YourCLIError):
    """Configuration related errors."""
    pass

class BuildError(YourCLIError):
    """Build related errors."""
    pass
```

## Documentation Standards

### README
- Use best practice
- Add mermaid diagrams for workflow

### Docstring Standards
- Use Google/NumPy style docstrings
- Document all public functions and classes
- Include type information in docstrings
- Provide usage examples for complex functions

## Best Practices

### Performance
- Use async/await for I/O operations
- Implement progress bars for long-running operations
- Use streaming for large file operations
- Cache expensive computations

### User Experience
- Provide clear, actionable error messages
- Use colors and formatting for better readability
- Add confirmation prompts for dangerous operations

### Security
- Validate all user inputs
- Use secure defaults for configuration
- Don't log sensitive information
- Implement proper permission checks for file operations

### Maintenance
- Write comprehensive tests (aim for >90% coverage)
- Use semantic versioning
- Maintain a CHANGELOG.md
- Document breaking changes clearly

## File Patterns to Follow

### .gitignore
```
__pycache__/
*.py[cod]
*$py.class
.Python
build/
dist/
*.egg-info/
.env
.venv/
.coverage
htmlcov/
.pytest_cache/
.mypy_cache/
.ruff_cache/
```

Always prioritize code clarity, type safety, and user experience when developing CLI applications. Your goal is to create a powerful, user-friendly CLI application that sets new standards in the field.
