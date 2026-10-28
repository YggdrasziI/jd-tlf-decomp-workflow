# Tasks

## Current phase: Step 1 — Setup

- [x] Repository skeleton, `.gitignore`, documents (`CLAUDE.md`, `SPEC.md`,
      `ROADMAP.md`, `notes/`), skills.
- [x] `tools/extract.py`: ISO and PRX SHA-1 checked (2026-10-27).
- [x] PRX analysis: `tools/prx.py`, `tools/prx_info.py`; segment-relative relocations
      handled by `tools/prx_reloc.py` (round trip identical, 2026-10-27).
- [x] Symbols from the PRX tables: `tools/prx_symbols.py` (124 system imports named,
      92 `mcp_<NID>`, 6 exports).
- [x] splat config `config/NPUG80330/splat.levelstartup.yaml`: split runs (2026-10-27).
- [x] `tools/configure.py` + `tools/assemble.py` (VFPU as `.word`) write `build.ninja`.
- [x] **First `ninja` run**: all-asm build gives `LEVELSTARTUP_DISC.PRX: OK`, also
      after `ninja -t clean` (2026-10-28). The decompals `as` has no
      `-march=allegrex`: assembled as `mips32r2`/o32, VFPU and Allegrex-only
      instructions (`min`, `max`, `clz`...) as `.word`, splat `add_set_gp_64: false`.
- [x] `git init`, `.git/info/exclude`; remote `YggdrasziI/jd-tlf-decomp`.
- [x] First commits pushed to `jd-tlf-decomp` (2026-10-28); `.workflow.git` local.
- [x] `jd-tlf-decomp-build` pushed (Dockerfile: Ubuntu 24.04, python3-pip,
      `PIP_BREAK_SYSTEM_PACKAGES=1`, `COPY orig /orig`), GHCR image built, package
      access given to `jd-tlf-decomp`, CI green on Linux (2026-10-28).
- [x] `jd-tlf-decomp-workflow` created by the owner, `.workflow.git` pushed (2026-10-28).
- [x] `psp-decomp-tools.md` deleted with the owner's go (content in
      `notes/REFERENCES.md`).

## Next: Step 2 — Compiler identification

- [ ] SNC runtime routines and EH tables (`notes/COMPILER.md`, "Next probes").
- [x] PSP compilers from decomp.me in `tools/compilers/` (2026-10-28):
      `pspsnc_1.2.7503.0`, `mwccpsp_3.0.1_219`, `psp-gcc-1.3.1` (Linux only, not run).
- [x] Probes `tools/probes/snc_{leaf,branch}.cpp`: SNC 1.2.7503.0 **`-O2 -g`** gives
      15/16 identical functions (`notes/COMPILER.md`, 2026-10-28). `-g` is what
      gives the single epilogue and unthreaded `b` chains.
- [ ] `func_0002D818`: `self->key` load placement not reproduced.
- [ ] m2c: the PyPI `m2c` was an unrelated package (uninstalled, requirements now
      point to matt-kempster/m2c on git); installing it from git needs the owner's
      approval.
- [ ] mwccpsp hangs when run natively: retry through wibo to rule it out.
- [ ] `tools/compiler_sweep.py` for MIPS.

## Later

- Generate the relocation tables from the link (`--emit-relocs`) instead of copying
  `elf_tail.bin` (needed before code can move; not needed for matching).
- objdiff config and report, `report_diff.py`, `progress_badges.py`,
  `asm_compare.py`, `unit_candidates.py` (step 3).
- Ghidra with ghidra-allegrex, `tools/ghidra_sync.py`; `.ppmap` export for PPSSPP.
- Evaluate TeamGDB/PortableKit.

## Naming leads

- Source paths in `.rodata` (`notes/SPLITS.md`): `ENGINE/video.cpp`,
  `ENGINE/PSP/PSPTextureManager.cpp`, `ENGINE/PSP/PSPDraw.cpp`, `SHARED/WAD/loader.cpp`,
  `SHARED/WAD/loadCharacter.cpp`.

## Set aside

None.

## Completion rule

A unit is matching only at 100 % for all functions and data with
`LEVELSTARTUP_DISC.PRX: OK`; check a box only with that evidence.
