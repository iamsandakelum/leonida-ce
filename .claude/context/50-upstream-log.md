# Upstream since this snapshot

Local snapshot: `6121caa1` (2026-11-23 22:25 -08:00). Upstream `main` on 2026-11-24 (15:39 UTC): `1403e90e`,
43 commits ahead. Repository: https://github.com/IAmNotSiob/leonida-ce (20 stars, 5 forks at the time).

## Files upstream changed (6121caa1..1403e90e)

```
M  docs/gpu_test_results.md            A  gpu-test-results/RTX5090Dv2.txt
M  hacks/BOOT_NOTES.md                 A  hacks/dfree.gdb  engine.gdb  owner.gdb  popfree.gdb  pops.gdb  trap.gdb  uaf.gdb
M  runtime/CMakeLists.txt              A  runtime/shims/page_alias.h      A  runtime/shims/sysinfo_ce.cpp
M  runtime/shims/acphal.cpp            M  runtime/shims/d3d12_vulkan_device.cpp
M  runtime/shims/d3d12_xs_device.cpp   M  runtime/shims/d3d12_xs_device.h
M  runtime/shims/dstorage_x.cpp        M  runtime/shims/xg_xs.cpp         M  runtime/shims/xmem.cpp
```

Nothing under `tools/`, `tests/`, `make_game.py`, `viewer/` or the other docs changed.

## Commits, oldest first (author: the CE owner unless noted)

```
b23492b2 2026-11-24  docs: record RTX 5090 D v2 GPU results on Windows (contributor, PR #1)
ffb2eb4c 2026-11-23  Boot: compute pipelines compile in the background; thread stack dumps
f29c6967 2026-11-23  Boot: console texture addresses, depth+UAV textures, no memory probes
2e85f842 2026-11-23  Boot: console-only depth formats 118-120 map to the PC 24/8 formats
b2e69dd9 2026-11-24  hacks: boot notes, where loading stops after the texture fixes
9bd2a8d3 2026-11-24  Boot: resource sizes from the console layout, after VIctory
dfc600f1 2026-11-24  hacks: boot notes, the next stop after console footprints
da389446 2026-11-24  hacks: boot notes, the bad free at 124 s
fd26f6fc 2026-11-24  hacks: boot notes, the command stream that fills at 100 s
97ca1793 2026-11-24  acphal: coprocessor messages and acknowledgments, after VIctory
64ccf99c 2026-11-24  hacks: boot notes, the command stream is never switched
7f530ccd 2026-11-24  hacks: boot notes, the command stream consumer runs once
f9cd31f0 2026-11-24  hacks: boot notes, the audio thread waits on a lock
63377c6e 2026-11-24  hacks: boot notes, the audio lock is held by an exited thread; owner.gdb
4aca9e5e 2026-11-24  hacks: boot notes, the audio object lock is overwritten, not held
91ad4a63 2026-11-24  hacks: boot notes, the audio object is outside the runtime audio allocations
2448afaa 2026-11-24  hacks: boot notes, the list writes do not reach the audio object
ef4312d5 2026-11-24  hacks: boot notes, forcing the audio lock free does not unblock audio
8c690380 2026-11-24  Boot: stack dumps name each thread's title index; notes on the audio lock
422f53cf 2026-11-24  acphal: XMA contexts advance silently, after VIctory
62ad4fdf 2026-11-24  Boot: patch the title's audio lock leak; the audio thread runs
6d233cc7 2026-11-24  xmem: XMemAlloc and XMemFree call the title's allocation hooks
b314acbf 2026-11-24  hacks: boot notes, the two forms of the 120 s stop; dfree.gdb
a06cf4da 2026-11-24  hacks: boot notes, the bad-freed block is at a fixed offset; uaf.gdb
d2c209e3 2026-11-24  Boot: queue CopyPageMappingsBatchX maps pool pages, after VIctory
9f7d2572 2026-11-24  xmem: host-placed title reservations stay below 2^40, after VIctory
3c026085 2026-11-24  hacks: boot notes, the 120 s stop is float data written over live objects
1409d6d8 2026-11-24  hacks: boot notes, the float corruption is a race; a run reached world loading; trap.gdb
9a67483c 2026-11-24  Boot: an in-process page write trap (LEONIDA_CE_TRAP_PAGE)
61475133 2026-11-24  Boot: the page trap follows the arena allocating there; notes on the race
28f92b6b 2026-11-24  Boot: LEONIDA_CE_CPUS cuts the processors GetLogicalProcessorInformation reports
45b8a6f6 2026-11-24  dstorage: requests run on workers after Submit; statuses complete in order
b1ab580b 2026-11-24  Boot: the page trap tags writes with thread and title index
f1cb3814 2026-11-24  Boot: the trap watches the arena it finds and dumps its owner on a reset
8b324c1b 2026-11-24  hacks: boot notes, work-queue items come back; pops.gdb
8183169c 2026-11-24  hacks: boot notes, all three forms of the stop come from the teardown walk; popfree.gdb
ffb04ff4 2026-11-24  Boot: presents are paced (LEONIDA_CE_FPS, 30 by default)
3b4e1feb 2026-11-24  Boot: the arena watcher dumps every thread when the arena object changes; the stop is an overflow
80dfa403 2026-11-24  Boot: in-process code probes (LEONIDA_CE_PROBE)
a1e6ef1c 2026-11-24  Boot: footprint padding switch, small-texture layout log; notes
0a816fd7 2026-11-24  Boot: probe range filter and caller chains; summary of the 120-140 s stop
9e6b0be0 2026-11-24  Merge pull request #1 (RTX 5090 D v2 results)
1403e90e 2026-11-24  d3d12_xs: log why CreateDescriptorHeap failed; opt-in table-copy skip
```

The commits right before the snapshot, for orientation: `cdb03090` "Leonida CE: continue the project as a
community edition" and `28c9da37` / `8772be66` (the `boot-hacks` branch and its merge, "about 90 s under Wine"),
`3ae4efe0` (console queue Signal/Wait, fence values, first PresentX), `fee9fe22` (the title runs on, frames
captured), `fda2e0d5`, `bf9fb390`, `6fe2c98b` (the console texture computer), `bb463061`. Before `cdb03090` the
history is the original repository's (`ItsNotRexed`), ending in a batch of pull-request merges on 2026-11-23.

## Issues and pull requests on CE (2026-11-24)

- Issue #2 (open): "Windows-native --run reaches CreateDescriptorHeap, then E_INVALIDARG + segfault". Two
  contributors and the owner; the findings are summarised in 10-state-of-the-project.md section 3.4.
- PR #3 (open, mergeable): "Windows-native: allocator slot 9, local dump paths, copy-rejection diagnostics".
- PR #1 (merged): RTX 5090 D v2 GPU results on Windows (Node 32-wide subgroups; 251 shaders translated and valid;
  `ms_wave_info_s3` fails as on other GPUs; `d3d12_depth_resolve_test` exited 0xC0000409 there).

## How to refresh this file

From a clone: `git fetch origin`, then `git log --reverse --format='%h %ad %s' --date=short 1403e90e..origin/main`
and `git diff --name-status 1403e90e origin/main`; `gh issue list` / `gh pr list -R IAmNotSiob/leonida-ce`.
Without a clone, a blobless shallow clone in a scratch folder works:
`git clone --filter=blob:none --no-checkout --depth 200 https://github.com/IAmNotSiob/leonida-ce <scratch>`.
