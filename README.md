# Questionary Core

Lightweight, config-aware wrapper around the `questionary` library for all interactive CLI prompts.

## Overview
- Centralizes keyboard-driven prompts (select, autocomplete, path input) across the framework
- Validates choices/defaults before rendering, raising early errors for misconfigured prompts
- Surfaces cancellations as `KeyboardInterrupt` so callers can exit gracefully
- Reads defaults from `ConfigManager` to keep UX consistent across modules

## Features
- **Multiple-choice selector** with search filtering, arrow keys, and safe defaults
- **Path input** helper that enforces directory-only mode when needed
- **Autocomplete input** with middle-of-string matching and deduplicated suggestions
- **Config integration** so prompt settings remain aligned with project-wide preferences

## Quickstart

```python
from cores.questionary_core.questionary_core import QuestionaryCore

prompter = QuestionaryCore()

project_name = prompter.autocomplete_input(
	"Project name",
	choices=["demo_service", "web_console"],
	default="demo_service",
)

dest_path = prompter.path_input(
	"Destination path",
	default=f"./{project_name}",
	only_directories=False,
)

module_type = prompter.multiple_choice(
	"Module type",
	["core", "manager", "plugin", "util", "mcp"],
	default="core",
)
```

## API

```python
class QuestionaryCore:
	def multiple_choice(self, message: str, choices: Sequence[str], default: str | None = None) -> str: ...
	def path_input(self, message: str, *, default: str | None = None, only_directories: bool = False) -> str: ...
	def autocomplete_input(
		self,
		message: str,
		choices: Iterable[str],
		*,
		default: str | None = None,
		match_middle: bool = True,
	) -> str: ...
```

## Notes
- The helpers raise `KeyboardInterrupt` when the user cancels a prompt; catch it at the workflow boundary.
- Duplicate options are deduplicated in `autocomplete_input` to keep menus tidy.
- Defaults must be present in the provided choices or a `ValueError` is raised.

## Requirements & prerequisites
- `questionary` (bundled in the project’s root `requirements.txt`)
- ANSI-capable terminal for best UX

## Troubleshooting
- **`ValueError: choices must contain at least one option`** – ensure you pass a non-empty iterable.
- **Prompt exits with `KeyboardInterrupt`** – the user pressed <kbd>Ctrl+C</kbd> or hit ESC; handle it and exit gracefully.
- **No arrow-key navigation** – run the CLI in a real terminal; some IDE consoles strip arrow key handling.

## Module structure

```
cores/questionary_core/
├─ __init__.py                 # package marker
├─ questionary_core.py         # QuestionaryCore implementation
├─ init.yaml                   # module metadata
└─ README.md                   # this file
```

## See also
- Module Creator Core – uses these prompts when scaffolding modules
- Project Creator Core – interactive wizard for project scaffolding
- Logger Utility – consistent logging for CLI workflows