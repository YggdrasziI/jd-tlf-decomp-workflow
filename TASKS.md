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
- [ ] `jd-tlf-decomp-build`: repository prepared (Dockerfile, `orig/NPUG80330/`,
      image workflow from ts2) but not pushed: pushing the PRX needs the owner's go.
      Then GHCR image and CI green.
- [ ] `jd-tlf-decomp-workflow` to create, then push `.workflow.git`.
- [ ] Delete `psp-decomp-tools.md` (content now in `notes/REFERENCES.md`), with the
      owner's go.

## Next: Step 2 — Compiler identification

- [ ] SNC runtime routines and EH tables (`notes/COMPILER.md`, "Next probes").
- [ ] PSP compilers available on decomp.me / `decompme/compilers`; SNC availability
      (ask the owner).
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
