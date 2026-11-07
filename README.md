# Karpathy-Inspired Coding Agent Guidelines

> Check out my new project [Multica](https://github.com/multica-ai/multica) — an open-source platform for running and managing coding agents with reusable skills.
>
> Follow me on X: [https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

Reusable coding-agent guidelines for Codex, OpenCode, Claude Code, and Cursor, derived from [Andrej Karpathy's observations](https://x.com/karpathy/status/2015883857489522876) on LLM coding pitfalls.

English | [简体中文](./README.zh.md)

## The Problems

From Andrej's post:

> "The models make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should."

> "They really like to overcomplicate code and APIs, bloat abstractions, don't clean up dead code... implement a bloated construction over 1000 lines when 100 would do."

> "They still sometimes change/remove comments and code they don't sufficiently understand as side effects, even if orthogonal to the task."

## The Solution

Four principles, shared across the skill and editor-specific formats, directly address these issues:

| Principle | Addresses |
|-----------|-----------|
| **Think Before Coding** | Wrong assumptions, hidden confusion, missing tradeoffs |
| **Simplicity First** | Overcomplication, bloated abstractions |
| **Surgical Changes** | Orthogonal edits, touching code you shouldn't |
| **Goal-Driven Execution** | Leverage through tests-first, verifiable success criteria |

## The Four Principles in Detail

### 1. Think Before Coding

**Make assumptions visible. Match clarification to risk. Surface tradeoffs.**

Resolve uncertainty in proportion to its consequences:

- **Inspect context first** — Read the user's instructions and repository conventions; use answers already available.
- **Proceed on low-risk choices** — State a reasonable assumption for reversible decisions within scope and follow existing patterns.
- **Ask about consequential ambiguity** — Clarify unresolved choices that materially affect correctness, scope, privacy, cost, or irreversible actions. Explain why the answer matters.
- **Keep safe work moving** — While waiting, continue independent investigation. Pause the affected action; silence and urgency do not authorize it.
- **Surface meaningful tradeoffs** — Present alternatives with materially different outcomes and suggest simpler approaches when warranted. Reversible code can still have consequential effects.

See [the examples](EXAMPLES.md#1-think-before-coding) for when to proceed, ask, or continue independent work while waiting.

### 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

Combat the tendency toward overengineering:

- No features beyond what was asked
- No abstractions for single-use code
- No "flexibility" or "configurability" that wasn't requested
- No error handling for impossible scenarios
- If 200 lines could be 50, rewrite it

**The test:** Would a senior engineer say this is overcomplicated? If yes, simplify.

### 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't "improve" adjacent code, comments, or formatting
- Don't refactor things that aren't broken
- Match existing style, even if you'd do it differently
- If you notice unrelated dead code, mention it — don't delete it

When your changes create orphans:

- Remove imports/variables/functions that YOUR changes made unused
- Don't remove pre-existing dead code unless asked

**The test:** Every changed line should trace directly to the user's request.

### 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform imperative tasks into verifiable goals:

| Instead of... | Transform to... |
|--------------|-----------------|
| "Add validation" | "Write tests for invalid inputs, then make them pass" |
| "Fix the bug" | "Write a test that reproduces it, then make it pass" |
| "Refactor X" | "Ensure tests pass before and after" |

For multi-step tasks, state a brief plan:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let the LLM loop independently. For vague criteria ("make it work"), inspect context and propose concrete checks; clarify only consequential unresolved choices.

## Install

### Codex and OpenCode

Use the portable [`karpathy-guidelines` skill](skills/karpathy-guidelines/SKILL.md). No Claude Code or Cursor installation is required.

1. Clone or download **this fork**, `michaelchamboko/andrej-karpathy-skills`.
2. Copy the entire `skills/karpathy-guidelines` directory into your target project's `.agents/skills/` directory, producing `.agents/skills/karpathy-guidelines/SKILL.md`. If a copy already exists, compare and merge your customizations before replacing it.
3. Open that project in Codex or OpenCode and ask it to use `karpathy-guidelines` for a coding task. Confirm that the skill appears in the host's available skills; restart the host if it has not refreshed discovery.

Both hosts support this project location; see the official [Codex skill documentation](https://developers.openai.com/codex/skills/) and [OpenCode skill documentation](https://opencode.ai/docs/skills/). The `skills/` directory at this repository's root is the distribution source, not the project discovery location.

Skills load when selected or matched to a task; installing this skill does not make every rule an always-on instruction. For always-on project guidance, merge the desired rules into your existing `AGENTS.md`, preserving the project's instructions and shared-policy references.

### Other supported hosts

**Claude Code plugin (optional)**

From within Claude Code, first add the marketplace:
```
/plugin marketplace add michaelchamboko/andrej-karpathy-skills
```

Then install the plugin:
```
/plugin install andrej-karpathy-skills@karpathy-skills
```

This installs the guidelines as a Claude Code plugin, making the skill available across all your projects.

**CLAUDE.md (optional, per-project)**

New project:
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/michaelchamboko/andrej-karpathy-skills/main/CLAUDE.md
```

Existing project (append):
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/michaelchamboko/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

## Using with Cursor

This repository includes a committed Cursor project rule ([`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc)) so the same guidelines apply when you open the project in Cursor. See **[CURSOR.md](CURSOR.md)** for setup, using the rule in other projects, and how this relates to Claude Code.

## Key Insight

From Andrej:

> "LLMs are exceptionally good at looping until they meet specific goals... Don't tell it what to do, give it success criteria and watch it go."

The "Goal-Driven Execution" principle captures this: transform imperative instructions into declarative goals with verification loops.

## How to Know It's Working

These guidelines are working if you see:

- **Fewer unnecessary changes in diffs** — Only requested changes appear
- **Fewer rewrites due to overcomplication** — Code is simple the first time
- **Consequential ambiguity is clarified before action** — Routine work proceeds with stated assumptions
- **Clean, minimal PRs** — No drive-by refactoring or "improvements"

## Customization

These guidelines are designed to be merged with project-specific instructions. For Codex and OpenCode, merge selected rules into the existing `AGENTS.md`; for Claude Code, use `CLAUDE.md`. Preserve existing instructions and shared-policy references.

For project-specific rules, add sections like:

```markdown
## Project-Specific Guidelines

- Use TypeScript strict mode
- All API endpoints must have tests
- Follow the existing error handling patterns in `src/utils/errors.ts`
```

## Tradeoff Note

These guidelines bias toward **caution over speed**. For trivial tasks (simple typo fixes, obvious one-liners), use judgment — not every change needs the full rigor.

The goal is reducing costly mistakes on non-trivial work, not slowing down simple tasks.

## License

MIT
