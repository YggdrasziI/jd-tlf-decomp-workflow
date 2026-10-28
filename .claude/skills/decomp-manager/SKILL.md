---
name: decomp-manager
description: Plan and steer jd-tlf decompilation work - read TASKS/ROADMAP, check the repository state, pick the next unit or task, present a short plan with checkpoints, and drive one reviewable unit at a time. Use at the start of a session or when asked to continue the decompilation.
---

# decomp-manager

Same workflow as the sibling projects (mlab-decomp, spyro-dotd-decomp, ts2-castaway).
Project rules are in `CLAUDE.md`; do not repeat them here.

## Workflow

1. Inspect the repository state:
   ```
   git status --short
   git log --oneline -5
   ```
   Separate pre-existing untracked files from the work; never commit or delete them
   without asking.
2. Read `TASKS.md`. Read `ROADMAP.md` only when the phase changes or a risk matters.
   Read other documents through the routing table of `CLAUDE.md`.
3. If a previous session was interrupted, read its last request and answer before
   starting new work (the transcript path is given by the session reminder).
4. Choose the work:
   - an unfinished unit from `TASKS.md` when a technique in `notes/MATCHING.md`
     applies to its remaining gap;
   - otherwise a new unit: small, few calls, a call-tree leaf, no VFPU, skip the
     "Set aside" list (`python tools/unit_candidates.py` once it exists).
5. Present a short plan (unit, bounds, expected sections, risks). Stop there if the
   user asked for a plan only or reserved approval; ask only when a real decision is
   open.
6. Before editing, save the report baseline: `python tools/report_diff.py --save`
   (once the objdiff report exists).
7. Execute one unit at a time with the `decomp-unit` skill, then close it with the
   `unit-readiness` skill (gate, docs, commit, push).
8. Time-box each function: after a few unsuccessful families of variants, record the
   gap in `TASKS.md`, leave it in assembly and move on.
9. At the end of a session (or when asked), update `TASKS.md`, add the session's new
   techniques to `notes/MATCHING.md`, and after each new unit add a line to
   `notes/PROGRESS.md` and run `python tools/progress_badges.py --record`.

## Rules

- Never mark a task done without the verification evidence of `unit-readiness`.
- Never treat a compiler, Ghidra, m2c or agent output as evidence; only `ninja` output
  and the objdiff report count.
- Keep project knowledge in project documents, not in this skill.
- Report conflicts between `TASKS.md`, `notes/SPLITS.md`, the splat yaml and the code
  instead of ignoring them.

## Final report

Units touched (with match counts), files changed, checks run
(`LEVELSTARTUP_DISC.PRX: OK`, `report_diff.py`), skipped checks, remaining gaps,
commits pushed.
