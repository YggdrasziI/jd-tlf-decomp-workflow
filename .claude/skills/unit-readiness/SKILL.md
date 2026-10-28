---
name: unit-readiness
description: Gate a finished jd-tlf change before commit and push - full build with the PRX SHA-1 check, no-regression report diff, matching/unfinished consistency, documentation sync, clean diff, commit, push and CI check. Use before every commit.
---

# unit-readiness

This project has no pull requests: verified commits go straight to `main`
(`CLAUDE.md` rules 3 and 4). The objdiff report plays the role of the code review.

## Gate (all must pass)

1. Full build: `python tools/configure.py` when `configure.py`, a splat yaml or a
   symbol file changed, then `ninja`; the output must end with
   `build/NPUG80330/LEVELSTARTUP_DISC.PRX: OK`.
2. No regression: `python tools/report_diff.py` against the baseline saved before the
   work (`--save`), once the objdiff report exists. Any regression is fixed before
   going on; renamed symbols at 100 % are fine.
3. Consistency:
   - every unit called matching is at 100 % for all functions and data;
   - every unfinished function keeps its `INCLUDE_ASM` with its gap written above it
     and in `TASKS.md`;
   - each change to the splat yaml and `symbol_addrs.txt` is explained;
     `symbol_addrs.prx.txt` and `config/nids.txt` only change through
     `tools/prx_symbols.py`.
4. Documentation in sync: the unit's row in `notes/SPLITS.md`; `TASKS.md` (new open
   entries, checked items only when verified); new techniques in
   `notes/MATCHING.md`; reused code in `notes/PROVENANCE.md`; facts that changed in
   `SPEC.md`, `notes/PRX.md` or `notes/COMPILER.md`.
5. Clean diff: `git status --short` and `git diff --stat` show only the intended
   subject; no `build/`, `orig/`, `asm/`, ISO, PRX, game files or scratch scripts.
6. Changes to scripts or tooling (not decompiled code): run `/code-review` on the diff
   and fix what it confirms.

## Commit and push

- One subject per commit, English message, no `Co-Authored-By` trailer.
- `git push origin main` (never force). Then check CI:
  `gh run list -R YggdrasziI/jd-tlf-decomp --limit 1` and, if needed,
  `gh run watch -R YggdrasziI/jd-tlf-decomp <id>`. A failed CI run is a blocker to
  report, not to work around.
- Workflow files (`TASKS.md`, `ROADMAP.md`, `CLAUDE.md`, `.claude/`,
  `notes/GHIDRA.md`) are not in this repository (`CLAUDE.md` rule 9): commit them
  separately with `git --git-dir=.workflow.git commit` and push that repository
  (`git --git-dir=.workflow.git push origin main`). Main-repository files and
  messages never refer to them.
- After each new unit: a dated line in `notes/PROGRESS.md`, then
  `python tools/progress_badges.py --record` and commit `progress/` with it.

## Rules

- Never report a unit as matching from a partial score, and never invent hashes,
  percentages or command output.
- Skipped checks are listed as such in the report, not presented as passed.

## Final report

`HEAD` and worktree state, units and functions now matching, gate results
(`LEVELSTARTUP_DISC.PRX: OK`, report diff summary), CI state, skipped checks,
remaining gaps.
