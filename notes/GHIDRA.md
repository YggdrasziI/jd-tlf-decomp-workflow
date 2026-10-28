# Ghidra

Status: not set up yet (`ROADMAP.md` step 3).

## Install

- Ghidra 11.2.1: `E:\Projets\Github\Decomp\Tools\Ghidra\ghidra_11.2.1_PUBLIC`.
- Extension [kotcrab/ghidra-allegrex](https://github.com/kotcrab/ghidra-allegrex):
  pick the release built for Ghidra 11.2.1 (File > Install Extensions), restart.
- The Ghidra project lives outside the repository (`*.gpr` and `*.rep/` are ignored).

## Load the PRX

- Import `orig/NPUG80330/LEVELSTARTUP_DISC.PRX` with the PSP PRX loader of
  ghidra-allegrex (it applies the relocations and names system imports by NID).
- Check the load base it picks against `notes/PRX.md`; addresses in this project are
  the link addresses (program header 0 at 0x0, program header 1 at 0x1408A8).

## Sync

To do: `tools/ghidra_sync.py` pushing `symbol_addrs*.txt` names into the project
(headless `analyzeHeadless` with a script), as in ts2-castaway.

## Known issues

None recorded yet.
