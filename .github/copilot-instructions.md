## 1. Copilot Instructions

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

### 1.1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

### 1.2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

### 1.3. Surgical Changes

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

#### 1.4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.

## 2. Nextflow nf-core Best Practices

**Follow nf-core standards. Respect Nextflow idioms.**

When working with Nextflow pipelines:
- Use `publishDir` in modules, not workflows. Let the workflow control publishing through `ext.args`.
- Emit named outputs from processes. Use `tuple val(meta), path(file)` consistently.
- Never hardcode tool versions. Define them in `modules.config` using `ext.args` or container directives.
- Use channel operators idiomatically: `map`, `groupTuple`, `join`, `combine` - not imperative loops.
- Prefix local modules/subworkflows with namespace. Use nf-core modules unmodified when possible.

Module structure:
- One process per tool/step. Respect single responsibility.
- Include `meta.yml` with description, inputs, outputs, and authors.
- Use `environment.yml` for Conda, not manual package lists.
- Test with nf-test, not placeholder tests.

Configuration:
- Base resource requirements in `conf/base.config` using process labels.
- Use `params` for user-facing options, `ext` for internal module config.
- Never expose internal paths or implementation details as params.

File organization:
- Local modules in `modules/local/`, nf-core modules in `modules/nf-core/`.
- Subworkflows group related processes. Keep them focused and reusable.
- Main workflow logic in `workflows/`, not `main.nf`.

The test: Can another nf-core developer understand this without explanation?
