# Leonida CE: project overview

Written 2026-11-24 from the checkout at upstream commit `6121caa1`. Measured numbers are the ones the docs state;
each doc names its method and caveats.

## What it is

Leonida is a port template for the Xbox Series build of Grand Theft Auto VI (package version 1.0.2.1), built by a
player on their own PC from their own copy. Nothing of the game is in the repository, ever. The approach is a
loader rather than a recompiler: the console CPU is x86-64, so the game's executable is mapped and run natively
and only the console's system software is reimplemented.

| Layer | Approach | Where |
|---|---|---|
| CPU code | `gta6rt` maps the PE image, binds every import to a shim, installs TLS, registers unwind tables, runs the entry point | `runtime/src` |
| Console imports | one shim per DLL (`xmem`, `xgameruntime`, `d3d12_xs`, `gameinput` on SDL3, `dstorage`, `acphal` and audio, `xg_xs`, WinHTTP offline) | `runtime/shims` |
| Graphics | Xbox D3D12 (`d3d12_xs`) behind a measured-slot COM proxy, recorded and replayed on Vulkan | `runtime/shims/d3d12_*`, `runtime/src/vulkan_*` |
| Shaders | the console's precompiled RDNA2 (gfx1013) programs are carved from `shaders.rpf`, disassembled with LLVM and translated to SPIR-V (all 26,503) | `tools/shader_carve.py`, `fssm_unpack.py`, `shader_disasm.py`, `rdna2spirv.py` |
| Assets | RPF9 archives and `.ttpf` texture pools are read without the encrypted, never-decrypted table of contents; models (OBJ, GLB), textures (DDS) and audio (Ogg Opus, WAV) are exported | `tools/rpf9dump` (C#, on the `rpf9-explorer` submodule), `tools/*.py` |
| Research | PE and RTTI inspection, carving, texture tiling probes, hardware oracles, differential fuzzers | `tools/`, `runtime/test/` |

Leonida CE (community edition, https://github.com/IAmNotSiob/leonida-ce) continues the original
`ItsNotRexed/Leonida` after it went private on 2026-11-23/24; the history, the GPLv3 licence and the credits are
kept. The original's issue and pull-request numbers (`#313`, `#166`, `#6709`, ...) appear throughout the docs as
provenance and are no longer reachable.

## The build (`make_game.py`, or `Build.bat`)

Stages: `check` (names and sizes against `game/xbox-1.0.2.1.json`; `--full-check` adds SHA-256), then `shaders`,
`assets` and `runtime` in parallel, then `pipelines` (every translated shader created on the local driver through
`vk_pipeline_check`, cache saved next to the port). A stopped build resumes. Output goes to `--out`
(`Documents\Leonida` by default) and intermediate files to `--work` (`<out>/work`, hundreds of GB for assets).

Work-folder layout the tools and tests expect:

```
<work>/shaders/dxbc/            carved containers, index.json
<work>/shaders/dxbc/fssm/       unpacked native programs (<name>.fssm)
<work>/shaders/dxbc/disasm/     LLVM listings (<name>.s), disasm.done
<work>/shaders/spirv/           <name>.spvasm, <name>.spv, index.json (per-shader metadata: stage, wave size,
                                inputs, user SGPRs, push_constant_bytes, lds_bytes, control_flow, ...)
<work>/shaders/spirv/fspx/      FidelityFX compute programs;  .../bvh/  XboxBvhBuilder programs
<work>/assets/                  rpf9dump exports (models, textures, pools), audio, manifest.json
<work>/logs/                    per-stage logs
```

Translator flags that `make_game.py` forwards and records next to the output: `--wave64-emulate {auto,on,off}`,
`--no-float-controls`, `--bary-fallback`, `--texel-offset-fallback`, `--f16-interp-fallback`, `--mesh-compute`,
`--native-mesh`, `--pc-tail-ubo`. Changing any of them, the translator sources or the `llvm-mc` version
retranslates.

## The translator's runtime contract (what every shader assumes)

Defined in `docs/design.md` ("Translator status and runtime contract") and relied on by the D3D12 layer: one
SPIR-V invocation per lane, registers as exact-bit uint arrays, EXEC as a per-invocation flag; a bindless set 0
(images, samplers, the descriptor hash table of T# and S# words); a 144-byte push-constant block holding the 32
user-data SGPRs `s0..s31` plus the heap base and code base (152 with `--mesh-compute`; 128 plus a set-1 uniform
block with `--pc-tail-ubo`); one GPU address space where `host = heap_base + console_va` for buffers, descriptor
tables and code (a single buffer-device-address arena of at most 4095 MiB; images are keyed separately through
their T#); vertex input `v0 = VertexIndex - BaseVertex`, `v3 = InstanceIndex - BaseInstance`; wave64 compute and
mesh shaders exact on 32-wide subgroups only with `--wave64-emulate`, wave64 VS/PS/GS cross-half ops approximate
there.

## Repository map

| Path | What | Size |
|---|---|---|
| `make_game.py` | the build: stages, discovery of the game copy, preflight, TUI, resume | 2,900 lines |
| `tools/` | 261 Python tools (40-tools-catalog.md); `rpf9dump/` (C#), `bcpack_calibration/` (Rust), `hw_oracle/` (C, AMD hardware oracle), `conformance/`, `imhex/` patterns; `rdna2spirv.py` alone is 18,877 lines | 8.5 MB |
| `runtime/` | CMake project: `gta6rt`, the shims (34k lines, `d3d12_vulkan_device.cpp` 10k), the Vulkan backend headers, 232 C++ tests, 278 hand-written RDNA2 `.asm` GPU fixtures with `build.py` and `check.py`, `xma_decoder/` (Rust) | 13 MB |
| `tests/` | 449 standard-library unit tests (`python -I tests/test_<name>.py`), a few pytest or numpy ones (`tests/requirements.txt`), `tests/semantic/` with 456 translator regression files, `Rpf9DumpDiagnostics/` (C#), viewer tests (`.cjs`) | 18 MB |
| `docs/` | 89 measured-format notes indexed by `docs/README.md`; `design.md` 566 KB, `d3d12_vulkan.md` 451 KB, `shader_validation.md` 198 KB | 3.3 MB |
| `scripts/` | GPU and Wine test runners, `run_semantic_tests.py`, `check_no_game_data.py`, `dev.py`, PowerShell helpers (`build_runtime.ps1`, `build_llvm_mc.ps1`, `first_look.ps1`) | |
| `hacks/` | exploratory boot stand-in notes (`BOOT_NOTES.md`) and one machine's Wine loops; not pull-request quality | |
| `viewer/` | map viewer (`index.html`, `studio.html`, the local-only `serve.py`) | |
| `mods/` | manifest-only example mods (`mod.json`); `Mods.bat` runs `tools/leonida_mods.py gui` | |
| `game/xbox-1.0.2.1.json` | per-file size and SHA-256 of the 108-file package (hashes only) | |
| `gpu-test-results/` | per-GPU result records | |
| `third_party/` | `rpf9-explorer` (submodule, empty in this ZIP), `xtaskqueue` (vendored), `bcpack-rs` (vendored), `xma` notices | |
| `.github/workflows/` | `ci.yml` (compile, standard-library tests, rpf9dump, translator pytest, semantic suite on Windows and Linux, macOS, viewer), `runtime.yml` (MSVC build, Wine, llvmpipe, MoltenVK), `no-game-data.yml`, `xma2-decoder.yml` | |

## Glossary

- **RPF9**: the archive format; its table of contents is encrypted and never decrypted here, so tools carve
  streams by signature. **0DRP** page directories, **PSIN** metadata containers, **RBF0** UI trees, **RSC8** lip-sync
  clips, **AWC** audio banks (Opus, PCM, XMA2), **TTPF** texture pools (**BCPack**-compressed BCn chunks).
- **DXBC / XBCH / FSSM**: the shader container, its console record and the native RDNA2 program inside;
  **FSPX / ACCC**: the FidelityFX DLL's native compute programs; **PAL stage descriptors** head an FSSM blob.
- **gfx1013**: the LLVM target for the console GPU (RDNA2 with ray-tracing instructions); **wave32 / wave64**: the
  wave width a program was compiled for (23,841 of 26,503 shaders are wave64).
- **V# / T# / S#**: buffer, image and sampler descriptors (GFX10 layout: `tools/gfx10_formats.json`,
  `runtime/src/gfx10_descriptors.h`); **user SGPRs**: the registers root arguments arrive in.
- **d3d12_xs**: the console D3D12 flavour; **PresentX**: its presentation call (queue slot 32 measured);
  **XDXR / BVH4**: the console ray-tracing acceleration-structure format built by `XboxBvhBuilder.dll`.
- **Device, queue and list slots**: 0-based vtable indexes; the console tables deviate from the PC SDK order and
  are measured per slot in `docs/d3d12_command_objects.md`, `docs/retail_boot_chain.md` and `hacks/BOOT_NOTES.md`.
- **Arena / heap base**: the one BDA buffer console VAs are offsets into (see the contract above).
- **VIctory**: a sibling project whose behaviours were ported ("after VIctory" in commit messages); **XCL**: a
  synthetic test title compared against in `docs/xcl_compare.md`; **americas-recomp**: GPL-2.0-only prior art
  whose measurements are cited by link only, never copied (`docs/related_projects.md`).
- **RT-5, D3D-NN, SHADER-NN, TEST-NN, ASSET-NN, FUZZ-NN, BUILD-NN**: item ids of the old coordination issue.
- **The work folder** `<work>`: the user's local build output; everything in it is game-derived and stays private.

## Where to look

- Status and formats: `README.md` (status table), `docs/README.md` (index of every note with its headline count).
- The boot: `docs/boot.md` (runbook, trace triage, questions Q1-Q22), `docs/retail_boot_chain.md` (stops 1-13),
  `hacks/BOOT_NOTES.md` (stand-ins past stop 13), `docs/retail_boot_intel_host.md`.
- The D3D12 layer: `docs/d3d12_vulkan.md` (section 3.3 address space, 3.4 descriptors, 6 milestones M0-M7, 8.2
  open questions), `docs/d3d12_command_objects.md`, `docs/d3d12_xs_surface.md`, `docs/xbox_d3d12_xs.md`.
- The runtime: `runtime/README.md` (modes, `LEONIDA_UNIMPLEMENTED`, trace formats, every test),
  `docs/runtime_imports.md`.
- Testing on a GPU: `docs/testing.md` (setup per OS, wave64 emulation, known problems), `docs/pipeline_check.md`.
- Translator validation: `docs/shader_validation.md`, `tests/semantic/README.md`, `docs/isa_coverage.md`.
