# Roadmap

Phases follow those of the sibling projects (`mlab-decomp`, `spyro-dotd-decomp`,
`ts2-castaway`), adapted to a PSP matching decompilation with splat. Current task
status is in `TASKS.md`; figures over time are in `notes/PROGRESS.md`.

## Step 1 — Setup (current phase)

- Goal: repository, `tools/extract.py` (ISO and PRX SHA-1), splat config for
  `LEVELSTARTUP_DISC.PRX`, `tools/configure.py` + ninja; the all-assembly build
  rebuilds the identical PRX. Private GitHub repositories (main, build image,
  workflow), CI green.
- Exit criteria: `LEVELSTARTUP_DISC.PRX: OK` locally and in CI.
- Risks: binutils without VFPU (handled by `tools/vfpu_words.py`); spimdisasm taking
  data for code or the reverse in a 1 MB `.text` (only matters once units are
  compiled); segment-relative relocations (handled by `tools/prx_reloc.py`).

## Step 2 — Compiler identification

- Goal: which compiler built the game (SNC suspected: `.cplinit`, `.linkonce.d`,
  plain-text `type_info` names, `or`/`ori` idioms), its version and flags, and the
  SDK / runtime libraries. Probes: small leaf functions compiled with every candidate
  (decomp.me PSP compilers, `decompme/compilers`, a local SNC if the owner has one),
  `tools/compiler_sweep.py` rewritten for MIPS.
- Exit criteria: compiler and flags proven (`notes/COMPILER.md`), one game function
  linked from C.
- Risks: **the compiler may not be available** (SNC is a commercial SDK compiler);
  without it, C matching is impossible and the owner decides how to go on. C++ name
  mangling and the EDG front end of SNC differ from GCC.

## Step 3 — Comparison tooling

- Goal: objdiff (`objdiff.json`, report), `tools/report_diff.py` regression gate,
  progress badges and history; `tools/asm_compare.py` on `objdiff-cli diff`;
  `tools/unit_candidates.py` for MIPS; Ghidra project with ghidra-allegrex
  (`tools/ghidra_sync.py`); optional `.ppmap` export for PPSSPP.
- Postponed: decomp.dev and any public showcase.

## Step 4 — Splitting into units

- Goal: region map of `.text` in `notes/SPLITS.md` (SDK libraries, runtime, engine,
  game); unit bounds from the source-path strings, call clusters and data use;
  `.text` split into per-unit files with `INCLUDE_ASM`.
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
