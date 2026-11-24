# This PC: what is installed, what runs, what is missing

Measured 2026-11-24. Update when something is installed or a game copy the user owns appears.

## Hardware and OS

| | |
|---|---|
| OS | Windows 11 Pro 10.0.26300 |
| CPU | Intel Core i9-14900HX, 32 logical processors |
| RAM | 32 GiB |
| GPU 1 | NVIDIA GeForce RTX 5070 Laptop GPU, driver 32.0.16.2140 (621.40), Vulkan 1.4.357, discrete; subgroup size 32 only (`tools/vk_subgroup_info.py`: `native64=false`, mesh and compute stages supported, `per_vertex_inputs=true`, barycentric fallback not needed, 256 bytes of push constants) |
| GPU 2 | Intel UHD Graphics (Raptor Lake-S Mobile), driver 32.0.101.7076, Vulkan 1.4.323, integrated. `make_game.py` and the harnesses prefer a native discrete ICD; if a test lands on Intel, set `LEONIDA_VK_DEVICE=5070` (part of the GPU name). Intel's Windows driver crashes on the corpus mesh shaders (BUILD-2202), which `--mesh-compute` auto-handles only when the runtime's GPU is Intel |
| Disks | C: 336 GB free, D: 304 GB free (this checkout), E: 326 GB free. A full build needs about 400 GB (`--stages check,shaders,runtime` about 30 GB); `--work` can point at any drive |

Consequences for this GPU: translate with `--wave64-emulate auto` (it turns emulation on here), run GPU tests with
`LEONIDA_W64_EMULATE=1`, and expect wave64 VS/PS/GS cross-half ops to be approximate. Upstream's own tests often
cite an "RTX 5070 Laptop", so this is a well-trodden configuration.

## Software present

| Tool | Version | Notes |
|---|---|---|
| Python | 3.14.6 (`C:\Users\<user>\AppData\Local\Python\pythoncore-3.14-64`) | CI pins 3.12; everything run so far works on 3.14 (see 10-state section 5) |
| Node.js | 26.4.0 | CI tests 24; one viewer test fails on 26 (10-state section 4.B) |
| git | Git for Windows (MINGW) | the checkout itself is not a repository |
| gh | 2.97.0, logged in | read-only use unless asked to post |
| PowerShell | Windows PowerShell 5.1 and `pwsh` where present | `scripts/*.ps1` helpers |
| vulkaninfo | runtime copy in `C:\Windows\System32` | enough for `make_game.py --wave64-emulate auto`; not the SDK |

## Software missing (the runtime, the shader stage and the asset stage need these)

| Need | Why | How to get it (ids to confirm with `winget search`) |
|---|---|---|
| Visual Studio 2022 with "Desktop development with C++" and Windows SDK 10.0.26100+ | `runtime/` (MSVC, version 1.2 root signatures), `scripts/build_runtime.ps1`, `scripts/build_llvm_mc.ps1` | `winget install Microsoft.VisualStudio.2022.Community --override "--add Microsoft.VisualStudio.Workload.NativeDesktop --includeRecommended"`; SDK: `winget install Microsoft.WindowsSDK.10.0.26100` |
| CMake 3.24+ and Ninja | `cmake -S runtime -B runtime/build` | VS's "C++ CMake tools" component, or `winget install Kitware.CMake Ninja-build.Ninja` |
| Vulkan SDK (LunarG) | `spirv-as`/`spirv-val` for the translator, Vulkan headers for the GPU tools and the D3D12 layer (`VULKAN_SDK` must be set in the environment that runs CMake) | `winget install KhronosGroup.VulkanSDK` |
| `llvm-mc` with the AMDGPU target (LLVM 21+) | disassembling shaders (`gfx1013`) and building the GPU test fixtures. The official Windows LLVM build lacks AMDGPU | `scripts\build_llvm_mc.ps1` (needs VS; 20-40 min), or `conda install -c conda-forge llvm-tools` then `LLVM_MC=<path>\llvm-mc.exe`, or WSL `llvm-21` with `LLVM_MC="wsl -d Ubuntu -e /usr/lib/llvm-21/bin/llvm-mc"` |
| .NET 8 SDK (`global.json` pins 8.0.100, rolls forward) | `tools/rpf9dump` and `tests/Rpf9DumpDiagnostics` | `winget install Microsoft.DotNet.SDK.8` |
| `third_party/rpf9-explorer` submodule | `rpf9dump` builds against it | `git submodule update --init` in a real clone (`make_game.py` fetches it when git is installed) |
| ffmpeg (optional) | XMA2 decode and `--wav` | `winget install Gyan.FFmpeg` |
| Rust/cargo (optional) | `tools/bcpack_calibration`, `runtime/xma_decoder` | only if those are worked on |
| pytest + numpy in a venv (optional) | the pytest-marked tests (`tests/requirements.txt`), `tex_*`/`calibrated_texture` numpy paths | `python -m venv .venv; .venv\Scripts\pip install -r tests\requirements.txt` |

## The checkout is a ZIP: make it a clone

Option 1, a fresh clone next to it (keeps this folder as is):

```powershell
cd D:\Work\Personal
git clone --recursive https://github.com/IAmNotSiob/leonida-ce leonida-ce
```

Option 2, adopt this folder in place (the files equal `6121caa1`, so the index matches without a checkout):

```powershell
cd "D:\Work\Personal\GTA VI Leonida"
git init
git remote add origin https://github.com/IAmNotSiob/leonida-ce
git fetch origin
git reset 6121caa1                      # HEAD and index at the snapshot; the working tree is untouched
git status                              # expect only CLAUDE.md and .claude/ as untracked
Add-Content .git\info\exclude "CLAUDE.md`n.claude/"
git submodule update --init
git merge --ff-only origin/main         # moves to upstream's current main (43 commits ahead at 1403e90e)
```

Either way, `CLAUDE.md` and `.claude/` stay local (`.git/info/exclude`), never in a pull request.

## Game files

- `python -I make_game.py --find` finds no copy on this PC.
- `E:\Modding\GTA VI` contains only `game_scarlett_master.exe` (120,181,760 bytes) and a release note from a leak
  group. `--check` refuses it (no `MicrosoftGame.Config`, `Catalog000.bin`, `appdata0.rpf`). It is not the user's
  own Xbox copy and is out of scope for this project: the exe tools (`pe_inspect.py`, `first_look.ps1`,
  `gta6rt --dry-run`) were deliberately not run on it, and obtaining game files is not something to help with.
- The build and the boot work need the user's own Xbox Series 1.0.2.1 package with its files readable on disk
  (the folder that holds `MicrosoftGame.Config` and the `.rpf` archives). Until then, work from list B in
  10-state-of-the-project.md.

## Environment variables worth knowing (full tables: `runtime/README.md`, `docs/boot.md`, `docs/testing.md`)

`LEONIDA_D3D12=vulkan` (the Vulkan-backed device; the only one that writes `[d3d12-trace]`), `LEONIDA_SHADERS`
(translated shader folder), `LEONIDA_VK_DEVICE` (pick the GPU by name), `LEONIDA_W64_EMULATE=1` (wave64 tests on a
32-wide GPU), `LEONIDA_VK_VALIDATION=1` (Khronos validation layer), `LEONIDA_UNIMPLEMENTED=log|fail|stop`,
`LEONIDA_SKIP_INT3=1`, `LEONIDA_ARENA_MB`, `LEONIDA_GAME_DIR` and `LEONIDA_MOVIE` (package-gated ctest tests),
`LEONIDA_FIRST_LIGHT_DIR`, `LLVM_MC`, `VULKAN_SDK`; upstream-only (after this snapshot): `LEONIDA_CE_*`.
Overlay layers (RivaTuner, OBS, FPS Monitor) break Vulkan tests: `docs/testing.md` "Known problems".
