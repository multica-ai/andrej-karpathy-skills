---
name: karpathy-guidelines
description: Behavioral guidelines to reduce common LLM coding mistakes. Use when writing, reviewing, or refactoring code to avoid overcomplication, make surgical changes, surface assumptions, and define verifiable success criteria.
license: MIT
---

# Karpathy Guidelines

Behavioral guidelines to reduce common LLM coding mistakes, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Before writing new code, take the first option that fully works:
1. Does it need to exist? Skip what nobody asked for, and name it in one line.
2. Already in this codebase (helper, component, pattern)? Use it the way the surrounding code does.
3. Standard library or platform feature? Use it - but the project's own component beats a native one.
4. An installed dependency? Use it. Never add a dependency for a few lines.
5. Otherwise: the minimum code that works.

**Never cut:** validation at trust boundaries, error handling that prevents data loss, security, accessibility, anything the user asked for. Code you move or merge keeps its error handling and validation.

A shortcut with a known limit gets a one-line comment: `shortcut: <the limit>, <when to upgrade>`.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

**Surgical is not partial.** Before editing, list every place the change must reach - callers, tests, fixtures, config, exports - and finish all of them. Bug fix: grep every caller of the function you touch, then fix the root cause once in the shared code. The smallest change in the wrong place is a second bug.

*(Ladder, never-cut list and completeness rule adapted from [Ponytail](https://github.com/DietrichGebert/ponytail), MIT.)*

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For a new feature or bugfix, write the failing test before the implementation, not after - a test added alongside or after the code tends to confirm what you built rather than what was asked. Use the `test-driven-development` skill for the full red-green-refactor process.

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.
