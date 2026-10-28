# Tasks

## Step 1 — Setup (done)

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

## Current phase: Steps 3-4 — Tooling, first units (step 2 done)

- [x] SNC 1.2.7503.0 `-O2 -g` (`notes/COMPILER.md`); probes 15/16.
- [x] SNC in the build: `tools/compile.py` (empty sections removed), downloaded by
      `ninja` (`download_tool.py compiler`), wibo on Linux; CI green (2026-10-28).
- [x] No `INCLUDE_ASM` (SNC moves file-scope asm): units are `cpp` subsegments,
      unfinished code stays `asm` subsegments.
- [x] `tools/reloc_overrides.py`: `MIPS_NONE` for symbol guesses the PRX does not
      relocate (26147 entries).
- [x] objdiff: `objdiff.json` from `configure.py`, `ninja report`.
- [x] First unit `src/unit_000960A4.cpp` (3 functions, 100 %).
- [x] `unit_000960A4` extended to 0x96528: 7 of 8 functions (2026-10-28).
- [ ] `func_0009630C` (`text_0009630C`, between `unit_000960A4.cpp` and
      `unit_000960A4_1.cpp`): best attempt in the comment of `unit_000960A4.cpp`;
      gap: the original copies `$a0` to `$s2` before the second owner null test, and
      the loop counter registers are swapped.
- [ ] Next units: other leaf clusters (`python tools/unit_candidates.py`).
- [ ] `func_0002D818` probe gap (load placement).
- [ ] mwccpsp through wibo to rule it out.
- [x] `report_diff.py`, `progress_badges.py` (badges + card in README),
      `unit_candidates.py` for the splat layout; `ninja report` in CI with the report
      as artifact (2026-10-28). `cpp` units are `complete` (linked).
- [ ] `progress_badges.py` regions: empty until the region map is known.
- [ ] Unit data: `.rodata`/`.data` of units (rodata migration, per-unit data
      subsegments) once a unit has some.
- [ ] Header dependencies for `src/` (SNC dependency output) so header edits rebuild.

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
