---
name: write-tests
description: Write failing tests (Red phase of TDD)
argument-hint: <requirement>
context: fork
agent: tester
---

Write failing tests for: $ARGUMENTS

Analyze the requirement, read existing tests for patterns, write test files, and verify all new tests FAIL.
Return test file paths for `/implement`.
