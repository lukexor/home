# Home

This is the home repo: the place agent sessions start from when the work spans
more than one repository, or isn't in any repository yet (planning, research,
notes). It holds pointers to the repos I work on and shared notes. It holds no
product code.

My working style is the global `~/.claude/CLAUDE.md`, which is a symlink to
`claude/CLAUDE.md` in this repo, so it is versioned here and shared between
machines.

## Layout

- `repos/*.md` - one line per repository: what it is, where it lives locally,
  where to clone it from.
- `claude/` - global Claude Code files that `~/.claude/` symlinks to.
- `work/` - work-only layer, a separate git repo that this one ignores. It
  exists only on the work machine. It never goes to GitHub.

## Working in another repository

- Find it in the pointers below. If its local path is missing, clone it from
  the listed remote to that path, then continue.
- Before changing anything in a repository, read its own `CLAUDE.md` or
  `AGENTS.md`. Claude Code does not load these automatically for directories
  outside the session's start directory.
- Repository-specific rules belong in that repository, not here. Only what is
  true across repositories belongs in this repo.
- Commit in the repository you changed, on a branch, following its own
  conventions.

## Plans and notes

Write plans, research and working notes into this repo (`work/plans/` or
`work/docs/` for work, `plans/` or `docs/` for personal), never into a
scratchpad or `/tmp`. Scratchpads get cleared and the links break.

## Repositories

@repos/personal.md

@work/CLAUDE.md
