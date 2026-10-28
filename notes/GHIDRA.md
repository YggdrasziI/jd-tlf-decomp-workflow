# Ghidra

Ghidra reads the code; it proves nothing (`CLAUDE.md` section 2). The project lives
outside the repository (`*.gpr` and `*.rep/` are ignored).

## Install (done 2026-10-28)

- Ghidra 12.2: `E:\Projets\Github\Decomp\Tools\Ghidra\ghidra_12.2_PUBLIC` (release
  zip, SHA-256 checked). The Ghidra 11.2.1 beside it stays for ts2 (dsd-ghidra).
- JDK 25 (Ghidra 12.2 needs it; the system JDK is 21): portable Temurin 25.0.5+8 in
  `E:\Projets\Github\Decomp\Tools\Ghidra\jdk-25.0.5+8`. For the GUI, give this folder
  when `ghidraRun.bat` asks for a JDK.
- [kotcrab/ghidra-allegrex](https://github.com/kotcrab/ghidra-allegrex) v21.5
  (`ghidra_12.2_PUBLIC_20261025_ghidra-allegrex.zip`, SHA-256 checked), unpacked in
  `%APPDATA%\ghidra\ghidra_12.2_PUBLIC\Extensions` (same as File > Install
  Extensions). No release is built for Ghidra 11.2.1, hence 12.2.

## Project

- `E:\Projets\Dev\jd-tlf-ghidra`, project `jd_tlf_NPUG80330`, program
  `LEVELSTARTUP_DISC.PRX`.
- Created headless by `python tools/ghidra_sync.py --import`: loader "PSP
  Executable (ELF)", language `Allegrex:LE:32:default`, auto-analysis (3868
  functions). Image base 0: Ghidra addresses are the link addresses of this project
  (program header 1 at 0x1408A8), checked on `func_000960D0` (0x90 bytes) and
  `func_000964A4` (0x84 bytes).

## Sync

- `python tools/ghidra_sync.py` (`--dry-run` to only report): runs
  `tools/ghidra/SyncSymbols.java` through `analyzeHeadless` on the project, naming
  every symbol of `config/NPUG80330/symbol_addrs*.txt` (+ image base) and creating
  functions for `type:func`. Names given by hand in Ghidra are kept and listed. Close
  the project in the GUI first (headless needs the lock).
- `func_<addr>` names of splat are not in the symbol files; Ghidra keeps its
  `FUN_<addr>` for them (same address).

## Known issues

- `analyzeHeadless.bat` goes through cmd.exe, which splits arguments at the `&` of
  the repository folder: `ghidra_sync.py` copies its inputs to a temporary folder.
