# State of the project: where the work was left off

Written 2026-11-24. Update this file at the end of any session that moves the state.

## 1. Identity of this checkout

- The local tree equals upstream `IAmNotSiob/leonida-ce` commit `6121caa1` ("xgameruntime: silent speech
  synthesis, after VIctory", 2026-11-23 22:25 -08:00): every one of the 2,340 local files hashes to the blob in
  that commit's tree, none is missing or extra. It is a ZIP, so there is no history and no submodule.
- Upstream `main` at the time of writing: `1403e90e` (2026-11-24 07:39 -08:00), 43 commits ahead, all in
  `runtime/shims`, `runtime/CMakeLists.txt`, `hacks/`, `docs/gpu_test_results.md` and `gpu-test-results/`.
  Nothing under `tools/`, `tests/`, `make_game.py` or the other docs changed (50-upstream-log.md has the list).
- CE repository: issue #2 open (Windows-native boot), PR #3 open (Windows-native fixes), PR #1 merged (RTX 5090 D
  results). No coordination issue exists; the AGENTS.md claim step has nowhere to post but issue #2 or a new issue.
- The snapshot's own test suites are not green (section 5): the original repository merged a large batch of pull
  requests on 2026-11-23 right before going private, and several tests, docs and fakes were left out of step with
  the code. Expect that, and do not mistake those failures for ones a change of yours introduced.

## 2. Where the port stands (README status table, condensed)

| Area | State |
|---|---|
| Playable, a frame on screen | No. Nothing is playable on any platform. |
| Finding and checking a copy (Xbox Series 1.0.2.1) | Works. |
| Shaders: carve, unpack, disassemble, translate | All 26,503 translate and pass `spirv-val` (2,236 VS, 12,101 PS, 2,689 CS, 9,282 MS, 195 GS). 23,841 are wave64: exact on AMD; on 32-wide GPUs compute and mesh are exact with emulation, VS/PS/GS cross-half ops approximate. |
| Translator on real GPUs | Hand-written GPU tests pass on more than 30 GPU and driver combinations; the game's own shaders are checked by `spirv-val` and driver pipeline creation (`vk_pipeline_check`: 26,503 of 26,503 on an RTX 4060 Ti), not by their output. |
| Models, textures, audio | Exported: 382,781 models (0 decode failures); TTPF pools 99.41% strict-clean through `rpf9dump`, the rest through the clean-room `bcpack.py` with a `BCPackLimitation` record where a history table is needed; AWC banks whole (Opus lossless, XMA2 via ffmpeg). |
| PE loader and shims | Runs stand-in executables end to end; GameInput on SDL3; `--trace` and `--trace-d3d12`. |
| Xbox D3D12 to Vulkan | In progress: device, queues, lists, fences, heaps, resources, views, root signatures, PSOs, draws, dispatches, barriers, copies, queries, the `PresentX` queue slots, the upload arena; milestones M0-M4 met on synthetic tests. Open: the console list tail, the descriptor-table record model, mesh-shader draws, indirect draw state, stream output, pipeline-state streams. |
| Ray tracing (XDXR BVH4) | GPU builder for BLAS and TLAS, refit, clone, compaction; serialization not done. |
| Booting the real executable | Partial: through the 13 documented stops, then (with the merged `boot-hacks` stand-ins) about 90 s under Wine with the first `PresentX`; see section 3. |

## 3. The boot, stop by stop

### 3.1 The documented chain (`docs/retail_boot_chain.md`, Windows 11, RTX 4090, 2026-11-23)

Stops 1-13 and what cleared each: `XMemVirtualAlloc` forms and attribute words (1-2), a missing `XErrorReport`
treated as fatal (3), the `/GA` TLS read at module index 0 (4), a null `DStorageGetFactory` (5),
`XMemAllocatePhysicalPages` (6, a CPU-only stand-in with pagefile sections and placeholders), device slot 26
called with the struct-return buffer first (7), device slot 48 = `CreateCommandQueueX` (8), device slot 73 =
console command-list creation (9), a 185 MiB placeholder reserve (10) and the physical sub-ranges carved from it
(11), device slot 133 = `CreatePlacedResourceX` with a 192-slot table (12), and stop 13: the first
`CreateDescriptorHeap`, whose heap start handle the title compares with an address it already holds.

### 3.2 Past stop 13: the `boot-hacks` stand-ins (`hacks/BOOT_NOTES.md` in this snapshot)

Measured on Ubuntu 24.04, Wine 9.0, RTX 6000 Ada. Exploratory, hard-coded `/root/...` paths, not PR quality.
What they establish: GameInput is imported by ordinal; the DirectStorage factory, queue and status slots and the
request layout; the 32-byte console descriptor-heap description `{PC 16 bytes, address, size}` whose storage is
the title's own memory (the console-addressed heap); device slots 25 and 108 (allocation info, swapped struct
return), 127 (`CreateCommittedResource3` shape), 41 (indirect argument type 105), 57 (a 0x160-byte console
pipeline record whose code blobs match exactly one corpus FSSM program by their first 24 bytes:
`hacks/psomatch.py`), 61 and 70 (sampler and buffer-view writes, ignored), 74 (a probe after the 6 GB physical
pool is mapped); the title's resource memory comes from its exported `__XMemAllocationHooks__`; runtime compute
shaders are FSPX programs the offline stage never carved (dumped at run time, 38 of 42 translate into
`spirv/fspx_game/`); a 30 s hang watchdog is switched off; the console queue runs two slots ahead of the PC one
from `Signal` on (16/17 Signal/Wait, 18/19 timestamp frequency and clock calibration, 32
`PresentX(planeCount, planes, params)`, 35 `ExecuteCommandLists` with two extra words); fence slot 12 returns a
pointer to a completed value the title polls. At this snapshot the title presents its first frame (no window) and
stops at a descriptor write whose descriptor-memory base came back 0, probably from an unforwarded console list
slot (70-75, 126, 139 or 142).

### 3.3 Upstream after this snapshot (43 commits on 2026-11-24, same machine under Wine)

In order of discovery (details and the gdb recipes are in upstream's `hacks/BOOT_NOTES.md` and `hacks/*.gdb`):

1. About 87 s: creating small render textures failed because the title's own GPU pool allocator returned null;
   answering allocation sizes with console footprints (ported from VIctory) let the title run to about 124 s and
   bind its UI pipelines. Frames stayed all zero.
2. About 100 s: a shared command stream overflowed because its consumer ran once; the consumer is driven from the
   audio thread's loop, and that thread waited on an engine lock a title function leaks when a count is 0. Patched
   at device setup (62ad4fdf): the audio thread runs and the overflow is gone. Along the way: a silent XMA context
   pump and coprocessor acknowledgments (`acphal`), `XMemAlloc` and `XMemFree` through the title's hooks, queue
   slot 31 = `CopyPageMappingsBatchX`, title reservations kept below 2^40, DirectStorage requests completing
   asynchronously in order, `LEONIDA_CE_CPUS` to cut the reported processor count.
3. The 120-145 s stop (three alternating forms: a bad free, a virtual call through float data, a null in a block
   lookup) all come from one worker thread's recursive teardown walk. Measured with in-process page traps and
   code probes: a 4 KiB 256x1 RGBA32F lookup-table fill is copied right after a 0xa0-byte bump-arena node and
   overruns its neighbours, and the arena object itself sits on that thread's stack below the crashing stack
   pointer. Working hypothesis at `1403e90e`: use after return of the arena. Slowing the title (gdb, page traps)
   avoids it and the run goes on to load world data (ttdb terrain and map tiles), the furthest so far. Ruled out,
   each tested: the audio lock (fixed), XMem hooks, physical-page aliasing, page-mapping batches, the address
   range, the CPU count, DirectStorage ordering, frame pacing (now 30 fps, `LEONIDA_CE_FPS`), runtime writes into
   title memory, footprint padding (`LEONIDA_CE_PAD`), skipping the table copy (`LEONIDA_CE_SKIP_LUT`).
4. Upstream's own next step: find the function that builds that arena on its stack and whether it returns before
   the cleanup walk uses it. Still open from the earlier list: the ignored sampler and view slots 61 and 70, the
   probe at 74, device slot 86 (another pipeline-creation variant called thousands of times), reading `PresentX`
   plane 0 back to disk, and a fixed pool of compile workers (about 1,190 threads at 110 s).
5. `1403e90e` adds one diagnostic line when `CreateDescriptorHeap` fails (description, console address and size,
   expected size, increment, which path ran) and an opt-in table-copy skip.

Upstream run settings that matter (Wine): `LEONIDA_ARENA_MB=4095`, `LEONIDA_SKIP_INT3=1`, `LEONIDA_CE_STACKS=1`,
and the `LEONIDA_CE_TRAP_PAGE`, `_DELAY`, `_ARENA`, `LEONIDA_CE_PROBE`, `_RANGE` diagnostics.

### 3.4 Windows-native (CE issue #2, two contributors and the owner, 2026-11-24)

- Contributor A (RTX 5060 8 GB): `--run` reaches `CreateDescriptorHeap`, which fails with `E_INVALIDARG`
  (0x80070057), and the title segfaults at about 6 s. Independent of thread count and of SDL3. The four heap
  starts differ between runs and once came back swapped between the CPU and GPU slots. The repository's
  `d3d12_console_descriptor_heap_test` passes on the same machine. The owner's hypothesis: the expected size uses
  the PC device's descriptor increment, which on the Windows NVIDIA driver may differ from the console's, so the
  console description falls through to the PC `CreateDescriptorHeap`; the `1403e90e` line will confirm or refute.
- Contributor B (RTX 3070 Laptop 8 GB, Windows 11): with three local fixes (`G:\x` resolved against the working
  directory, local dump paths instead of `Z:\root`, command-allocator slot 9 answering `S_OK` instead of being
  retried 2.5 million times) a run reaches full startup: 124 MiB streamed by DirectStorage, 911 of 911 pipeline
  records matched to corpus shaders, 42 runtime compute shaders dumped and 38 translated, audio connected, and
  `PresentX` at 3840x2160 continuously. But over 1.7 million traced calls there are zero `DrawInstanced`,
  `DrawIndexedInstanced` or `Dispatch` calls from the title (the only draws are the runtime's own detile pass),
  every frame is black, 32 of 34 threads sit in the pool wait, no fence is ever waited on, and the present thread
  polls its own memory for a ready flag nothing sets. No 120-145 s crash on Windows: it presents forever.
- The structural finding (contributor B, confirmed by the owner's numbers): the runtime models a title VA as an
  offset into one arena (`addr = heap_base + va`, arena at most 4095 MiB, 256 MiB by default), but this title's
  placed-resource and descriptor-table VAs span about 4.3 GB to 55 GB after `reserve_in_title_range` (and were
  host pointers around 1.85 TB before it). So `heap_base + va` cannot land in the arena, which is why descriptor
  heaps show as "not backed by title memory" and shaders read nothing meaningful. The proposed fix is a rebasing,
  not a widening: a registry of title host ranges to arena ranges (filled from the `xmem` reservations and
  `xs_record_placed`), a `normalise(va)` wherever a title VA becomes a shader address (descriptor tables, root
  CBV/SRV/UAV, indirect arguments, the bindless set), and a refresh of CPU-written pages before the draw that
  reads them (the shape `xs_refresh_placed_textures` already has). Necessary but not sufficient: the title also
  never reaches the state where it draws.
- PR #3 (open, 69 additions and 50 deletions in `d3d12_vulkan_device.cpp`, `d3d12_xs_device.cpp`,
  `vulkan_replay.h`): the slot-9 answer with its diagnostic capped at 20 lines, local dump paths (absolute paths
  for one machine; the author expects to change what they key off), and a `COPY_REJECTED()` diagnostic naming the
  replay line that refuses a copy (on this title every refusal is the multisampled-footprint check). The owner
  welcomed these as pull requests.

## 4. Open work, by what it needs

### A. Needs a package the user owns, the toolchain and a GPU

1. The host-to-arena VA rebasing (section 3.4; design in `docs/d3d12_vulkan.md` section 3.3): the first
   structural fix for Windows-native runs.
2. The 120-145 s overrun or use-after-return under Wine (section 3.3, item 4).
3. Why the title never reaches "ready" (no draws): what sets the flag the present thread polls.
4. The console list slots still ignored or probed (70-75, 126, 131, 132, 139, 142), device slot 86, sampler and
   buffer-view writes with console descriptions (61, 70), the probe at 74, `PresentX` plane readback to disk.
5. `CreateDescriptorHeap` `E_INVALIDARG` on Windows (read the `1403e90e` diagnostic first).
6. A fixed pool of compile workers; the hard-coded `/root` dump paths (PR #3 is the start).
7. README "Want to help": the vertex-shader input layout (`v0 = VertexIndex` unverified if the console runs VS as
   NGG); mesh-shader draws, indirect draw state and stream output in the layer; the 71 unrecovered fragment
   candidates in `levels.rpf`.

### B. Possible without game data

- Review and rebase PR #3; decide what the dump paths should key off (the runtime knows only the package
  directory, not a work folder).
- Bring the snapshot's tests, docs and fakes back in step with the code (section 5 lists them): stale test fakes
  (`FakeProc.poll`, `harness(cwd=)`, `Gen.decode_any_format`, the `expected_exe` manifest key), the `bink_index`
  error wording, `build_runtime.ps1`'s file list, the `runtime.yml` path filter (`tools/replace_retry.py`), docs
  naming missing paths or flags, the duplicated rows and lines, an undocumented `LEONIDA_CE_MAGENTA`.
- `tests/test_viewer_streaming.cjs` test 16 fails under Node 26 (`ReferenceError: AbortController is not
  defined` from `loadWorld` inside the `vm` sandbox of that one test, whose context object does not pass
  `AbortController`; the other sandboxes do). CI runs Node 24 and passes.
- Translator fidelity: wave64 cross-half ops in VS/PS/GS on 32-wide subgroups; FidelityFX FSPX programs (13 of
  29 translate); the BVH builder's one missing rule (`buffer_store_short`, 51 of 52); the two semantic files that
  fail at the snapshot (`test_image_atomics_guard`: `OpImageTexelPointer in unguarded block`;
  `test_image_store_d16`), which PR #1 also saw at `fee9fe22`.
- The D3D12 layer's open questions 1-22 in `docs/d3d12_vulkan.md` section 8.2 that are answerable from the
  package DLLs or the corpus (several are already marked answered there).
- Docs housekeeping: `runtime/README.md` and `docs/boot.md` still say the executable is not available while
  `docs/retail_boot_chain.md` and `docs/runtime_imports.md` measure it; `docs/testing.md` has a duplicated
  "128 bytes of push constants" heading.

## 5. Test status on this PC (2026-11-24; Python 3.14.6, Node 26.4.0; no LLVM, SPIRV-Tools, numpy or .NET)

| Check | Result |
|---|---|
| `python -m compileall -q make_game.py tools tests` | OK (16 s) |
| `python -I make_game.py --help` | OK |
| `python -I make_game.py --find` | 0 copies found |
| `python -I make_game.py --game "E:\Modding\GTA VI" --check` | refused: `MicrosoftGame.Config`, `Catalog000.bin`, `appdata0.rpf` missing (not a package) |
| `node --test tests/test_viewer_streaming.cjs` | 56 of 57 pass; test 16 fails (section 4.B) |
| `node --test tests/test_viewer_studio.cjs` | 11 of 11 pass |
| `python -I scripts/run_semantic_tests.py tests/semantic` | 456 files, 6,015 tests, 4 files failed, 272 s on 32 jobs: `test_sopc_validation` and `test_unary_f16_validation` need `spirv-as`/`spirv-val` (environment); `test_image_atomics_guard` and `test_image_store_d16` fail at the snapshot itself |
| CI's standard-library loop (449 files, 12 in parallel) | 418 pass, 31 fail; classified below |

Standard-library failures, by cause (logs were kept in the session scratchpad only):

- **The snapshot itself** (expected to fail on CI too at `6121caa1`): `test_bink_index` and
  `test_report_source_collision` (the tool says "not a Bink 2 stream (KB2) or an RPF archive", the test expects the
  wording without "(KB2)"), `test_build_runtime_fallback` (`build_runtime.ps1` lists fewer sources than CMake),
  `test_bvh_slot_check` (a 6-tuple unpacked into 5 names), `test_docs_cli_flags` (`docs/testing.md:318`
  `bench_wave64.py --prefix`, `docs/embedded_shaders.md:372`), `test_docs_design_rows` (README names
  `strand_grooms.md` twice; `design.md` row 328 has 4 cells), `test_docs_env_vars` (`LEONIDA_CE_MAGENTA`
  undocumented), `test_docs_repeated_lines` (`tests/semantic/README.md` lines 5 and 7), `test_docs_repo_paths`
  (`docs/retail_boot_intel_host.md` names a missing `docs/retail_boot_frame_loop.md`), `test_f64_conversion` and
  `test_f64_output_modifiers` (expected SPIR-V instructions missing from the translator's output),
  `test_make_game_paths` (fixture manifests lack `expected_exe`), `test_make_game_resume` (`FakeProc` has no
  `poll`), `test_make_game_empty_translation`, `test_rdna2spirv_decode` (`Gen.decode_any_format` gone),
  `test_runtime_paths` (`runtime.yml` filter lacks `tools/replace_retry.py`), `test_shader_checks` (the fake
  harness rejects `cwd=`), `test_tool_stdout_encoding` (tools whose `__main__` does not reconfigure stdout),
  `test_bvh_shaders_stage`, `test_psin_names`, `test_joaat_agreement` (non-ASCII names through `leonida_mods`;
  may also be an encoding difference), `test_awc_manifest` (one read-only replace case), `test_fuzzer_work_isolation`.
- **This PC** (environment, not the code): `test_bink_names`, `test_opus_tags`, `test_world_names` (Python 3.14
  formats `PermissionError` with the path in repr form, so the plain path is not a substring),
  `test_first_look_paths`, `test_guest_scene_reference`, `test_leonida_mods` (`%TEMP%` is the 8.3 short name
  `C:\Users\SANDAK~1\...`, which the tools resolve to the long name; set `TEMP`/`TMP` to the long path to clear
  these), `test_missing_input_exit_codes` (`tile_probe` needs numpy), `test_run_wine_tests` (no Wine, checkout on
  drive D:).

A serial rerun of the 31 (one at a time) failed all 31 again, so none of them is interference between parallel
tests: the 22 in the first group are the snapshot's own, the 9 in the second are this PC's.

## 6. Suggested order for the next sessions

1. Turn the ZIP into a real clone, so history, the submodule and upstream's 43 newer commits are available
   (commands in 20-environment-this-pc.md). Until then, treat `6121caa1` as the base of any patch.
2. Install the toolchain (Visual Studio 2022 C++ with Windows SDK 10.0.26100, CMake and Ninja, the Vulkan SDK,
   an `llvm-mc` with the AMDGPU target, the .NET 8 SDK, ffmpeg) and build `runtime/`; run `ctest` and
   `scripts/run_gpu_tests.py` with `LEONIDA_W64_EMULATE=1` (32-wide NVIDIA subgroups); record the result in the
   `docs/testing.md` format.
3. Work from list B until a package the user owns is available on this PC; then list A, starting with the
   `--check`, the `shaders` stage, `vk_pipeline_check`, and the `docs/boot.md` runbook.
