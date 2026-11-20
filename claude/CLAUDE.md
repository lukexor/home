# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) on how I want to
work, across any repository. Project- and language-specific conventions
(naming, lints, dependency choices, API idioms) belong in each project's own
CLAUDE.md so every contributor and every session sees the same thing - this
file is for working style, not code style.

This document establishes best practices for development. It promotes
consistency, maintainability, and adherence to idiomatic principles. This style
guide takes inspiration from the popular [Tiger
Style](https://github.com/tigerbeetle/tigerbeetle/blob/main/docs/TIGER_STYLE.md).

Our style drives our design goals which are safety, correctness, performance,
and developer experience in that order. Readability of and ability to reason
about code are critical to advancing these goals.

## General Principles

- **Safety by Default:** Favor safe features unless there's a proven need for
  `unsafe` code. Refer to [NASA's Power of
  Ten](https://spinroot.com/gerard/pdf/P10.pdf) for general advice.
- **Clarity First:** Code should be written for humans to read and
  maintain. Prioritize readability over cleverness.
- **Minimalism:** Strive for simplicity in APIs and implementations. Avoid
  unnecessary abstractions.
- **Consistency:** Follow idiomatic practices for the given technologies, and
  maintain consistent style within each project.
- **Explicitness:** Prefer explicit over implicit. Avoid hidden magic in APIs or
  implementations.

---

### Safety

- Use simple, explicit control flow. Avoid recursion to ensure execution is bounded.
- Put a limit on everything. All loops and queues must have a fixed upper
  bound. Follow the [fail-fast](https://en.wikipedia.org/wiki/Fail-fast_system)
  principal.
- Add assertions where appropriate. Assertion failures are unexpected and will
  downgrade correctness bugs into liveness bugs.
  - Assert all function arguments and return values that can't be encoded into
    the type system.
  - Split compound assertions into multiple assertions.
  - Assert both positively and negatively (things you expect, and things you do
    _not_ expect).
- Limit block scope and declare variables at the smallest possible scope. Use
  variable shadowing in languages that allow it to reduce the number of in-scope
  variables live at any point.
- Limit function bodies by splitting them. ["push ifs up and fors
  own"](https://matklad.github.io/2023/11/15/push-ifs-up-and-fors-down.html)
- Prefer to state conditionals positively instead of negatively.
- All errors must be handled, including errors hidden inside a pipeline
  (e.g. `cmd | grep | tail` silently drops `cmd`'s exit code); check `$?` or
  use `pipefail` when a pipeline's result matters.

### Correctness

Ensure correctness with unit tests and by adhering to a "zero technical debt"
policy. Do it right the first time because the second time may not come to
pass. This may mean delaying less critical features.

### Performance

- Consider performance from the start. It's much harder to address performance
  issues down the road when there are thousands of lines of code already in
  place.
- Do "napkin math" estimates of min/max for expected resource usage.
- Optimize slowest resources first (network, disk, memory, CPU) in that order,
  accounting for frequency of usage.
- Try to batch expensive operations to amortize the resource costs.

### Developer Experience

#### Formatting

Import ordering should be handled automatically by your editor. For Rust, the
following rust-analyzer settings should be used:

```lua
imports = {
  group = { enable = false },
  granularity = { enforce = true },
  prefix = "crate",
},
```

A project's own formatter config (`rustfmt.toml`, `.prettierrc`, line-length
limits, etc.) is authoritative - don't restate or override it here.

#### Documentation

- Include module-level and item-level documentation.
- Write clear and concise descriptions; include examples for public APIs.
- Use `TODO`/`FIXME` comments sparingly and tag with an issue number if possible.

### Testing

- Write unit tests for all public functions.
- Write integration tests for end-to-end functionality.

### Verification

- Before propagating a plan or spec document's claims across many files,
  verify the premise against the current codebase first - a written plan can
  be stale or simply wrong.
- Verify subagent reports and your own earlier in-session claims with a
  cheap, targeted check before relying on them, even when they claim success.
- Prefer a cheap throwaway probe over a full build/test cycle when validating
  a risky assumption.
- Explicitly flag when you're reversing or correcting your own earlier
  advice/action in-session, rather than quietly changing course.

### Logging and Debugging

- Ensure logs include context and are meaningful for troubleshooting.

### Dependencies

- Use well-maintained and actively supported crates/packages.
- Pin versions to avoid accidental upgrades.
- Audit dependencies regularly with tools like `cargo audit` (for Rust) or `npm
  audit` (for JS).

### API Design

- Avoid breaking changes in public APIs; prefer deprecation.

### Lints

- Address all warnings and document reasoning for any allowed or ignored
  lints. Treat the project's own lint config (`clippy.toml`,
  `[workspace.lints]`, `.eslintrc`, etc.) as the source of truth rather than
  restating a lint list here - a hand-copied list drifts out of sync with the
  real config.

### Git

- Double-check what actually got staged after any broad `git add` -
  untracked scratch/plan files have gotten swept in before.
- When splitting a branch into topic commits, group by actual code
  dependency (what a commit's diff requires to already exist), not by
  superficial file/crate grouping.
- Avoid unscoped `git stash -u` inside loops (e.g. per-commit verification) -
  it can sweep up unrelated untracked files. Scope it with a pathspec or
  use a worktree instead.

### Environment

- A command needing an interactive sudo password can't run through the bash
  tool passthrough - hand the exact command to the user to run themselves
  rather than attempting it and failing.

---

## Code Review Checklist

### General

- [ ] Does the code follow API guidelines and idiomatic practices?
- [ ] Is the code free from warnings, including lints?
- [ ] Are all public items documented?
- [ ] Is item visibility restricted to the smallest scope required?
- [ ] Are test cases comprehensive and meaningful?
- [ ] If the project tracks issues against commits, does this one reference one?
- [ ] Is the branch rebased against `main`?
  - This ensures a clean linear flow of changes and makes it easier to bisect
    regressions.

### Correctness

- [ ] Are edge cases handled?
- [ ] Are error conditions adequately checked and handled?
- [ ] Does the code maintain safety invariants?

### Performance

- [ ] Is the algorithm efficient for its use case?
- [ ] Are there unnecessary allocations or clones?
- [ ] Are benchmarks provided where performance is critical?

### Maintainability

- [ ] Is the code readable and easy to follow?
- [ ] Is the design modular and cohesive?
- [ ] Are abstractions appropriate for the problem?

### Security

- [ ] Are there potential vulnerabilities, e.g., unsanitized input?
- [ ] Are sensitive operations (e.g., cryptographic) implemented using vetted
      libraries?

@RTK.md
