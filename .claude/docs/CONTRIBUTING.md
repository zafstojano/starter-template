# Contributing with Claude Code

This project uses Claude Code as the primary development tool.

## TDD Workflow

We use Test-Driven Development (TDD) for new features and bug fixes.

### The Red-Green-Refactor Cycle

1. **Red**: `/write-tests` - Create failing tests that encode requirements
2. **Green**: `/implement` - Write minimal code to make tests pass
3. **Refactor**: `/simplify` - Clean up without changing behavior (provided by `code-simplifier` plugin)
4. **Validate**: `/pre-submit-pr` - Ensure everything passes before PR

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

### Test Scope

We write **unit tests** and **integration tests** only. No end-to-end tests.

**Data mocking requirements:**
- Always mock external data (local disk, S3)
- Use fixtures for test data
- Never load real datasets in tests

## Code Review

### What Claude Catches (Automated)
- Bugs, type errors, uninitialized variables
- Lint failures, test failures
- Debug code left in (print statements, breakpoints)

### What Humans Review
- Does this solve the right problem?
- Is this the right approach?
- Are there edge cases not covered by tests?

## Pull Request Checklist

Before creating a PR:

1. Run `/pre-submit-pr` to validate
2. Ensure all tests pass: `uv run pytest tests/ -v`
3. Ensure lint passes: `uv run pre-commit run --all-files`
4. Write a clear PR description

## Available Tools

See [CLAUDE.md](../../CLAUDE.md#tdd-workflow) for available skills and agents.
