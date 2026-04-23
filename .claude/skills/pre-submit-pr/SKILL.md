---
name: pre-submit-pr
description: Validate changes before PR submission
---

# /pre-submit-pr

Validate changes before submitting a pull request.

## Usage

```
/pre-submit-pr
```

## Instructions

1. **Run lint check**:
   ```bash
   uv run pre-commit run --all-files
   ```

2. **Run tests**:
   ```bash
   uv run pytest tests/ -v --tb=short
   ```

3. **Check for debug code**:
   ```bash
   bash .claude/scripts/check-debug.sh
   ```

4. **Check docs are up to date**:
   Inspect the branch diff and decide whether the repo's top-level docs (e.g. `README.md`, and any walkthrough/architecture doc such as `REPO_WALKTHROUGH.md`, `ARCHITECTURE.md`, `docs/` entry points) still describe the code accurately. Specifically:
   ```bash
   git diff main...HEAD --stat
   ```
   Read the docs that exist and compare against the branch's changes.
   Flag (or fix) staleness when the branch changes anything a reader of those docs would be told about — for example:
   - Project structure (new/renamed/removed directories, modules, entrypoints)
   - Public interface (CLI commands, API endpoints, config keys, env vars, install/build steps)
   - User-facing capabilities the README advertises (features, supported inputs/outputs, requirements)

   Skip this check when the branch only touches data/fixtures/generated artifacts with no code or interface changes.

5. **Summarize PR readiness**

## Output Format

```
## Pre-Submit PR Report

### Automated Checks
| Check | Status | Details |
|-------|--------|---------|
| Lint | PASS/FAIL | [summary] |
| Tests | PASS/FAIL | [X passed, Y failed] |
| Debug code | CLEAN/FOUND | [details] |

### Verdict: READY FOR PR / ISSUES TO ADDRESS

### Summary for PR Description
[2-3 sentences summarizing changes]
```

## Blocking Issues

These block PR submission:
- Lint failures
- Test failures
- Debugger statements (breakpoint, pdb)

6. **Run code review** (for significant changes):
   Invoke `pr-review-toolkit:review-pr` for deeper analysis.
   Include findings in the report under a "### Code Review" section.

## Non-Blocking (Flag for Reviewers)

Note in PR but don't block:
- TODOs in code
- Print statements (unless excessive)
