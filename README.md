# Starter Template

This repo offers a comprehensive starter template for initializing new projects with [Claude Code](https://claude.ai/code).

## Tooling

- **[uv](https://docs.astral.sh/uv/)** — Project and dependency management
- **[pre-commit](https://pre-commit.com/)** — Git hook-based linting and formatting
- **[pytest](https://docs.pytest.org/)** — Testing framework
- **[Claude Code](https://claude.ai/code)** — AI-assisted development with built-in TDD skills

## TDD Workflow

Development follows a Red-Green-Refactor cycle powered by Claude Code skills:

```
/write-tests <requirement>   →  Write failing tests        (Red)
/implement <requirement>     →  Make the tests pass        (Green)
/simplify [file]             →  Refactor, keep tests green (Refactor)
/pre-submit-pr               →  Lint, test, review         (Validate)
```

Each skill delegates to a specialized agent — the tester writes high-signal tests, the implementer focuses only on making them pass, and the code-simplifier cleans up without breaking anything.
