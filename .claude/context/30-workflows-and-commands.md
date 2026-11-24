# Workflows and commands (adapted to the CE fork and this PC)

All commands run from the checkout root, `D:\Work\Personal\GTA VI Leonida`. Python is `python` (3.14 here).

## Quick checks that need nothing but Python and Node

```powershell
python -m compileall -q make_game.py tools tests
python -I make_game.py --help
python -I tests/test_<name>.py                                   # one standard-library unit test
python -m unittest discover -s tests/semantic -p test_<name>.py  # one translator test file
python -I scripts/run_semantic_tests.py tests/semantic           # whole translator suite (466 files, one per core)
python -I scripts/run_semantic_tests.py tests/semantic -j 8 -p 'test_image_*.py'   # a subset
node --test tests/test_viewer_streaming.cjs                     # viewer (Node 26 here: test 16 fails, see 10-state)
node --test tests/test_viewer_studio.cjs
python -I scripts/check_no_game_data.py <files>                  # before any commit
python -I scripts/dev.py discover                                # inventory of the development entry points
```

CI's standard-library loop (`.github/workflows/ci.yml`, job `check`) runs every `tests/test_*.py` outside
`tests/semantic` that has a `__main__` block and no `import pytest` / `HAVE_NUMPY =` marker, one process each,
and reports all failures at the end. Reproduce it in Git Bash:

```bash
find tests -path 'tests/semantic' -prune -o -type f -name 'test_*.py' -print | LC_ALL=C sort | while IFS= read -r t; do
  grep -Eq '^import pytest|^ *HAVE_NUMPY =' "$t" && continue; grep -q '__main__' "$t" || continue
  python -I "$t" > /dev/null 2>&1 || echo "FAIL $t"; done
```

The pytest files (`python -m pytest tests/test_<name>.py`) need the venv from 20-environment. The semantic tests
that assemble and validate with `spirv-as`/`spirv-val` skip without SPIRV-Tools (CI's Linux leg makes that a
failure with `LEONIDA_REQUIRE_SPIRV_TOOLS=1`); on this PC they skip until the Vulkan SDK is installed.

## Translator change workflow

1. Write the rule in `tools/rdna2spirv.py` (anything not modelled raises `Unsupported`, never guesses).
2. Add a hand-written `.asm` fixture and expected words under `tests/semantic/` (see `tests/semantic/README.md`;
   `python -I tools/semantic_mutants.py tests/semantic/test_x.py --op <op>` tells whether the test would catch a
   one-line bug). Shaders for the GPU suite go to `runtime/test/shaders/*.asm` with `check.py` expectations and
   `python tests/semantic/regen_fixtures.py` regenerates the listings.
3. Add the contract row to `docs/design.md` ("Translator status and runtime contract") with how it was confirmed
   (GPU test, Mesa/LLVM reference, corpus count). `tests/test_docs_design_rows.py` and
   `tests/test_design_contract_rows.py` check the table's shape.
4. With a work folder (needs the package): slice runs before the full run, and counts before/after:
   ```powershell
   python -I tools/rdna2spirv.py <work>/shaders/dxbc/disasm --out <scratch> --limit 200
   python -I tools/rdna2spirv.py <work>/shaders/dxbc/disasm --out <scratch> --shard 0/8   # one of 8 slices
   python -I tools/rdna2spirv.py --merge <scratch>                                         # index.part*.json -> index.json
   ```
   Report translated and `spirv-val`-valid counts before and after. Flags: `--wave64-emulate`, `--opt`,
   `--assembler python` (no `spirv-as`), `--fma khr|exact`, the fallbacks listed in 00-project-overview.

## Runtime workflow (once Visual Studio, CMake and the Vulkan SDK are installed)

```powershell
$env:VULKAN_SDK = "C:\VulkanSDK\<version>"          # in the same shell that runs CMake
cmake -S runtime -B runtime/build -DLEONIDA_REQUIRE_VULKAN=ON
cmake --build runtime/build --config RelWithDebInfo
ctest --test-dir runtime/build -C RelWithDebInfo --output-on-failure
powershell -File scripts/build_runtime.ps1            # the wrapper: vcvars64, CMake+Ninja or cl.exe
python -I runtime/test/shaders/build.py <out>         # GPU fixtures (needs LLVM_MC, spirv-as, spirv-val)
python -I runtime/test/shaders/check.py <out> runtime/build
$env:LEONIDA_W64_EMULATE = "1"; python -I scripts/run_gpu_tests.py        # this GPU is 32-wide
python -I scripts/run_gpu_tests.py --filter ms_v5_workgroup
```

Expect about 125 ctest entries with Vulkan headers found and about 45 without (`docs/testing.md`). Tests that need
the package read `LEONIDA_GAME_DIR` (and `LEONIDA_MOVIE`) and print `SKIP` when unset. `loader_test` writes PE
images to `%TEMP%\leonida_loader_test`, which antivirus can block. Disable overlay Vulkan layers
(`DISABLE_RTSS_LAYER=1`, `DISABLE_VULKAN_OBS_CAPTURE=1`, or `VK_LOADER_LAYERS_DISABLE=~implicit~`) and Smart App
Control if freshly built test programs are refused.

Record GPU results as `gpu-test-results/<GPU>.txt` in the format of the existing files (GPU, driver, subgroup
range, OS, commit, tool versions, counts; never shader output from the game).

## The boot runbook (only with a package the user owns)

`docs/boot.md`: `LEONIDA_D3D12=vulkan`, `gta6rt --run <exe> --game-dir <package> --overlay <scratch> --trace
<new file> --trace-d3d12`, then `python -I tools/boot_trace.py <trace.jsonl> --d3d12 <stderr log>`; run with the
network off (the executable binds `ws2_32.dll` to the host's). Triage order: `map`, `unimplemented_call`,
`tls_callback`/`dllmain`, `exception`, `exit`, then the D3D12 trace. Share counts and error classes only. Day one
on a new executable: `scripts\first_look.ps1 -Exe <exe>` (PE inspection, GDK runtime classes, archetype-lookup
candidates, the ASSUMPTION/UNVERIFIED checklist, a dry run). On Windows, start `--run` from the package folder
(`dstorage_x.cpp` resolves `G:\x` against the working directory) and expect the `Z:\root` dump paths until PR #3
or its successor lands.

## Pull requests to the CE repository

1. There is no coordination issue on CE. Check the open issues and PRs first:
   ```powershell
   gh issue list -R IAmNotSiob/leonida-ce --state open
   gh pr list -R IAmNotSiob/leonida-ce --state open
   gh pr view 3 -R IAmNotSiob/leonida-ce --json files,title,body
   ```
   For a piece of the Windows-native work, say so in issue #2 (the de facto thread) before duplicating it.
2. Branch from the commit the checkout is at (`6121caa1`) or, better, from a real clone's `origin/main`.
3. Fill `.github/pull_request_template.md`: what and why, what was tested and on which GPU and driver,
   `AI-assisted: yes`, `Depends on: #N` for CE PRs it builds on, the two checklist boxes.
4. Before pushing: `python -I scripts/check_no_game_data.py <changed files>`, the quick checks above, the whole
   semantic suite for a translator change, `ctest` for a runtime change.
5. Never attach game files, extracted assets, generated shaders, traces or logs that quote the game. Counts,
   export names, slot numbers and call shapes only.

## Keeping this setup current

- Upstream moves fast (43 commits in one day at the time of writing). Refresh 50-upstream-log.md from a clone:
  `git fetch origin; git log --reverse --format='%h %ad %s' --date=short <known>..origin/main`,
  `git diff --name-status <known> origin/main`.
- Keep 10-state-of-the-project.md's sections 1, 3, 4 and 5 true; move finished items out of section 4.
- Memory lives in the user's Claude memory folder (index `MEMORY.md`); update the project-state memory when the
  state here changes materially, with absolute dates.
