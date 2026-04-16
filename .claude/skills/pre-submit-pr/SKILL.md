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

4. **Summarize PR readiness**

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

5. **Run code review** (for significant changes):
   Invoke `pr-review-toolkit:review-pr` for deeper analysis.
   Include findings in the report under a "### Code Review" section.

## Non-Blocking (Flag for Reviewers)

Note in PR but don't block:
- TODOs in code
- Print statements (unless excessive)
