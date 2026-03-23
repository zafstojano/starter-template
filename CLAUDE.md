# CLAUDE.md

Guidance for Claude Code when working with this repository.

## New Here? Start With These

1. **[README.md](README.md)** - Project overview
2. **[TESTING_STRATEGY](./.claude/docs/TESTING_STRATEGY.md)** - Strategy for test coverage
3. **[CONTRIBUTING](./.claude/docs/TESTING_STRATEGY.md)** - Design principles for building new features
4. **[REPO_WALKTHROUGH](./.claude/docs/REPO_WALKTHROUGH.md)** - A walkthrough of the project structure

## TDD Workflow

Red-Green-Refactor cycle (convention-based, not enforced):

```
/write-tests   →  delegates to tester agent   (Red)
    ↓
/implement     →  delegates to implementer agent (Green)
    ↓
/simplify      →  plugin: code-simplifier     (Refactor, optional)
    ↓
/pre-submit-pr →  lint, test, review          (Validate)
```

### When to Use TDD

**Use TDD for:**
- New features with clear acceptance criteria
- Bug fixes where you can write a failing test first
- Refactoring where tests ensure nothing breaks

**Skip TDD for:**
- Quick exploration and prototyping
- Documentation updates
- Simple config changes
- Model training loops (require loading models)

### Available Skills

| Skill | Usage | Purpose |
|-------|-------|---------|
| `/write-tests` | `/write-tests <requirement>` | Write failing tests (delegates to `tester` agent) |
| `/implement` | `/implement <requirement>` | Make failing tests pass (delegates to `implementer` agent) |
| `/simplify` | `/simplify [file]` | Refactor while keeping tests green (plugin: `code-simplifier`) |
| `/pre-submit-pr` | `/pre-submit-pr` | Validate before creating PR |
| `/pr-review-toolkit:review-pr` | `/pr-review-toolkit:review-pr` | Deep code review using specialized agents |

## Build & Development Commands

```bash
# Install dependencies
uv sync

# Run tests
uv run pytest tests/ -v --tb=short

# Run linter
uv run pre-commit run --all-files

# Add a dependency
uv add <package>

# Run a module
uv run python -m <module>
```

## Dev Rules

- Never write docstrings for functions. Use type hints instead.
- Do not include inline comments unless absolutely necessary.
- Always mock external data (disk, S3) in tests.
- No end-to-end tests - only unit and integration tests.
- **No defensive coding**: Do not write validation exceptions, return Optional/None types, or add assert statements. Assume if data was saved, it will be there when loaded.
- **Use f-strings in logger calls**: Write `logger.info(f"Found {count} items")` not `logger.info("Found {} items", count)`.
- **Save functions must not return values**: A save/store function writes data and returns None. Never return the data you just saved.
- If a plan has a TODO checklist, update it when completing a step.
- **Before committing**: Run `uv run pre-commit run --all-files` after staging changes. Fix any failures before committing.

## Plugins

First update marketplace:
```bash
claude plugin marketplace update claude-plugins-official
```

If not installed, prompt user to install:
```bash
/plugin install code-simplifier@claude-plugins-official
/plugin install pr-review-toolkit@claude-plugins-official
/plugin install pyright-lsp@claude-plugins-official       # requires `npm i -g pyright`
```

LSP setup guide: https://karanbansal.in/blog/claude-code-lsp/

**Known false positives**: VS Code's YAML validator flags `context` and `agent` as unsupported attributes in SKILL.md frontmatter. These are valid — see https://code.claude.com/docs/en/skills#run-skills-in-a-subagent. Workaround: add `"files.associations": {"**/.claude/skills/**/SKILL.md": "markdown"}` to VS Code user settings (see https://github.com/microsoft/vscode/issues/294520).

## Code Intelligence

Prefer LSP over Grep/Glob/Read for code navigation:
- `goToDefinition` / `goToImplementation` to jump to source
- `findReferences` to see all usages across the codebase
- `workspaceSymbol` to find where something is defined
- `documentSymbol` to list all symbols in a file
- `hover` for type info without reading the file
- `incomingCalls` / `outgoingCalls` for call hierarchy

Before renaming or changing a function signature, use `findReferences` to find all call sites first.

Use Grep/Glob only for text/pattern searches (comments, strings, config values) where LSP doesn't help.

After writing or editing code, check LSP diagnostics before moving on. Fix any type errors or missing imports immediately.
