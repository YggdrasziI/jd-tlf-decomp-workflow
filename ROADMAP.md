# Roadmap

Phases follow those of the sibling projects (`mlab-decomp`, `spyro-dotd-decomp`,
`ts2-castaway`), adapted to a PSP matching decompilation with splat. Current task
status is in `TASKS.md`; figures over time are in `notes/PROGRESS.md`.

## Step 1 — Setup (done, 2026-10-28)

- Goal: repository, `tools/extract.py` (ISO and PRX SHA-1), splat config for
  `LEVELSTARTUP_DISC.PRX`, `tools/configure.py` + ninja; the all-assembly build
  rebuilds the identical PRX. Private GitHub repositories (main, build image,
  workflow), CI green.
- Exit criteria: `LEVELSTARTUP_DISC.PRX: OK` locally and in CI.
- Done: the all-assembly build matches on Windows and in the Linux CI.
- Risks: binutils without VFPU (handled by `tools/vfpu_words.py`); spimdisasm taking
  data for code or the reverse in a 1 MB `.text` (only matters once units are
  compiled); segment-relative relocations (handled by `tools/prx_reloc.py`).

## Step 2 — Compiler identification (done, 2026-10-28)

- Goal: which compiler built the game (SNC suspected: `.cplinit`, `.linkonce.d`,
  plain-text `type_info` names, `or`/`ori` idioms), its version and flags, and the
  SDK / runtime libraries. Probes: small leaf functions compiled with every candidate
  (decomp.me PSP compilers, `decompme/compilers`, a local SNC if the owner has one),
  `tools/compiler_sweep.py` rewritten for MIPS.
- Exit criteria: compiler and flags proven (`notes/COMPILER.md`), one game function
  linked from C.
- Done: SNC `pspsnc_1.2.7503.0` at `-O2 -g` (`notes/COMPILER.md`), `unit_000960A4`
  linked from C++. Open side checks in `TASKS.md` (probe gap of `func_0002D818`,
  mwccpsp ruled out through wibo).
- Risks: **the compiler may not be available** (SNC is a commercial SDK compiler);
  without it, C matching is impossible and the owner decides how to go on. C++ name
  mangling and the EDG front end of SNC differ from GCC.

## Step 3 — Comparison tooling (done, 2026-10-28)

- Goal: objdiff (`objdiff.json`, report), `tools/report_diff.py` regression gate,
  progress badges and history; `tools/asm_compare.py` on `objdiff-cli diff`;
  `tools/unit_candidates.py` for MIPS; Ghidra project with ghidra-allegrex
  (`tools/ghidra_sync.py`); optional `.ppmap` export for PPSSPP.
- Done: all of the above but the `.ppmap` export (optional, in `TASKS.md`).
- Postponed: decomp.dev and any public showcase.

## Step 4 — Splitting into units (current phase)

- Goal: region map of `.text` in `notes/SPLITS.md` (SDK libraries, runtime, engine,
  game); unit bounds from the source-path strings, call clusters and data use;
  `.text` split into `cpp` units and the `asm` pieces between them.
- Generate the relocation tables from the link instead of copying them, once code is
  compiled (not needed while the layout does not move).
- Exit criteria: every region assigned to a unit with evidence.

## Step 5 — Library code

- Goal: libc, C++ runtime (exception handling, `operator new`, RTTI) and PSP SDK
  libraries (`libgu`, `libgum`, ...) matching, from references whose license is known.

## Step 6 — Game code

- Method: smallest units and call-tree leaves first, one unit per commit
  (`/decomp-unit`, `/unit-readiness`). Engine code first: it is shared with the other
  PRX (step 7).
- Exit criteria: every game unit matching.

## Step 7 — Other modules (later, on the owner's request)

- Other PRX: find the engine code they share with LEVELSTARTUP (fingerprints), reuse
  its C, then their own code.
- `EBOOT.BIN` (`mcp`): decryption (PPSSPP `Core/ELF/PrxDecrypter.cpp` or pspdecrypt),
  then the same pipeline; names of the `mcp_<NID>` imports from its exports.

## Step 8 — Maintenance (continuous)

- Naming from evidence, structure documentation, keeping `TASKS.md`,
  `notes/SPLITS.md` and `notes/PROGRESS.md` in sync with the code.
