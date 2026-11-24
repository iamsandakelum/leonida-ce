# Tools catalog

Every `tools/*.py` and `scripts/*.py` with its line count and the first line of its docstring, generated on
2026-11-24 from the snapshot (`python -I` each one with `--help` for the options). Grouped index first, then the
full table. Non-Python: `tools/rpf9dump` (C#, needs the `rpf9-explorer` submodule and .NET 8),
`tools/bcpack_calibration` (Rust), `tools/hw_oracle` (C, AMD-only hardware oracle used by the probes),
`tools/conformance` (compare harness with native and spec references), `tools/imhex` (ImHex patterns for RPF9,
PSIN, RBF0), `tools/gfx10_formats.json`, `tools/gdk_runtime_classes.json`.

## By area

- **Build and infrastructure**: `make_game.py` (root), `leonida_tui.py`, `mc_command.py`, `pool_workers.py`,
  `replace_retry.py`, `report_paths.py`, `file_mode.py`, `shader_stage_manifest.py`, `progress_dashboard.py`,
  `leonida_mods.py`, `manifest.py`, `manifest_verifier.py`, `hashdict.py`, `zero_span.py`, `turing_atlas.py`;
  `scripts/dev.py`, `scripts/check_no_game_data.py`, `scripts/check_apt_key.py`, `scripts/ci_expected_failures.py`,
  `scripts/merge_check.py`, `scripts/run_semantic_tests.py`, `scripts/run_gpu_tests.py`, `scripts/run_wine_tests.py`,
  `scripts/run_guest_scene.py`, `scripts/bench_wave64.py`, `scripts/shader_stats.py`.
- **Shader pipeline** (carve, unpack, disassemble, translate): `shader_carve.py`, `fssm_unpack.py`, `fssm_header.py`,
  `pal_stages.py`, `shader_disasm.py`, `shader_ir.py`, `rdna2spirv.py`, `cfg_structure.py`, `lane_masks.py`,
  `wave64_halves.py`, `wave64_emulate_opt.py`, `spirv_opt.py`, `spirv_asm.py`, `spirv_asm_tables.py`,
  `spirv_stubs.py`, `spirv_interface.py`, `spirv_gs_to_compute.py`, `spirv_metal_set0.py`, `mesh_compute_vs.py`,
  `bary_fallback_gs.py`, `shader_meta.py`, `shader_link_check.py`, `fspx_unpack.py`, `pe_shader_containers.py`,
  `shader_library.py`, `rdna2_decode.py`, `rdna2_isa_tables.py`.
- **Corpus censuses and scans** (counts over the translated or disassembled corpus): `shader_profile.py`,
  `isa_coverage.py`, `op_reach.py`, `feature_scan.py`, `gbuffer_census.py`, `user_sgpr_census.py`,
  `rootsig_census.py`, `root_sig_contract.py`, `root_spill_census.py`, `sampler_source_census.py`,
  `heap_index_census.py`, `ptr_truncation_census.py`, `image_bvh_census.py`, `wave32_pixel_scan.py`,
  `wave64_graphics_scan.py`, `wave64_pipeline_audit.py`, `vk_subgroup_info.py`, `ps_interp_modes.py`,
  `sgd5_effects.py`, `psocache_extract.py`, `psocache_inspect.py`, `psocache_usersig.py`, `classify_carve.py`;
  `scripts/audit_shader4_regs.py`, `scripts/vs_input_survey.py`, `scripts/vs_layout_census.py`.
- **Translator validation** (CPU references, differential fuzzers, GPU oracles): `cpu_ref.py`, `cf_ref.py`,
  `cf_diff.py`, `f32_fuzz.py`, `fmisc_diff.py`, `f64_diff.py`, `valu_fuzz.py`, `valu_int_diff.py`, `vint_diff.py`,
  `wint_diff.py`, `pk16_diff.py`, `sdwa_diff.py`, `salu_diff.py`, `sad_diff.py`, `div_fixup_diff.py`, `mem_diff.py`,
  `lds_fuzz.py`, `image_fuzz.py`, `crosslane_fuzz.py`, `wave_diff.py`, `vk_fuzz_run.py`, `ab_control_flow.py`,
  `ps_oracle.py`, `ps_sweep.py`, `ps_helper_probe.py`, `shader_diff.py`, `semantic_mutants.py`.
- **Hardware probes** (RDNA measurements through `tools/hw_oracle`, AMD GPU): `buffer_bounds_probe.py`,
  `buffer_format_fuzz.py`, `buffer_store_probe.py`, `buffer_swizzle_probe.py`, `bvh_tri_probe.py`,
  `cvt_f16_omod_probe.py`, `cvt_norm_mods_probe.py`, `cvt_pknorm_f16_probe.py`, `d16_format_probe.py`,
  `d16_image_probe.py`, `ds_permute_probe.py`, `f16_trans_sweep.py`, `f16_widen_probe.py`, `f64_omod_probe.py`,
  `fma_mix_literal_probe.py`, `fma_mix_probe.py`, `frexp_ldexp_f16_mods_probe.py`, `fspx_prologue_probe.py`,
  `image_d16_probe.py`, `image_query_probe.py`, `int_hw_probe.py`, `interp_hw_probe.py`, `vinterp_hw_probe.py`,
  `mad_denorm_probe.py`, `mode_round_probe.py`, `pk_int16_clamp_probe.py`, `pknorm_probe.py`,
  `probe_gfx1036_vcmp16.py`, `sampler_lod_probe.py`, `smem_neg_probe.py`, `texoff_probe.py`, `vcmp16_opsel_probe.py`.
- **Executable, boot and D3D12** (most need the executable or package): `boot_trace.py`, `pe_inspect.py`,
  `exe_checklist.py`, `gdk_runtime_calls.py`, `gen_gdk_runtime_classes.py`, `import_call_sites.py`,
  `inline_scan.py`, `rtti_scan.py`, `find_hash_lookup.py`, `recomp_census.py`, `x64_recomp.py`,
  `d3d12_guid_sites.py`, `d3d12_iid_scan.py`, `d3d12_xs_surface.py`, `gen_d3d12_stubs.py`, `xcl_compare.py`,
  `first_light.py`, `sandbox.py`, `sandbox_scene.py`, `xdxr_bvh.py`, `bvh_gpu_smoke.py`.
- **Archives and game data**: `rpf_probe.py`, `rpf9_carve.py`, `rpf9_names.py`, `page_index.py`, `psin_index.py`,
  `psin_data.py`, `psin_names.py`, `psin_psch.py`, `rbf0_index.py`, `rbf0_tree.py`, `rbf0_encode.py`, `odrp_7a.py`,
  `gamedata_index.py`, `gamedata_reader.py`, `gamedata_xml.py`, `gamedata_graph.py`, `gamedata_blobs.py`,
  `gamedata_maps.py`, `gamedata_sps1.py`, `content_definitions.py`, `restofworld_index.py`,
  `restofworld_model_join.py`, `story_index.py`, `mission_tables.py`, `script_qtable.py`, `text_tables.py`,
  `timecycle_pairs.py`, `veh_spawn_intensities.py`, `fog_volumes.py`, `instance_lists.py`, `draw_ownership.py`,
  `saves.py`, `save_keys.py`.
- **Models, characters, animation**: `meshlet_page.py`, `analyze_4240_full.py`, `vertex_types.py`,
  `skin_streams.py`, `fragment_children.py`, `character_census.py`, `character_textures.py`, `ped_catalogues.py`,
  `ped_assemble.py`, `skinned_glb.py`, `smh_groom.py`, `anim_index.py`, `anim_clips.py`, `anim_rigs.py`,
  `anim_move.py`, `anim_glb.py`, `anim_skel_match.py`, `anim_skeleton_match.py`, `glb_writer.py`, `glb_inspect.py`,
  `glb_skinning.py`, `batch_glb.py`, `gallery.py` (local 3D studio and texture gallery server).
- **World, navmesh, BVH**: `world_index.py`, `world_build.py`, `world_census.py`, `world_names.py`,
  `world_archetypes.py`, `world_interior_names.py`, `world_lights_paths.py`, `world_grid_atlas.py`,
  `world_drawable_lookup.py`, `world_drawable_verify.py`, `navmesh_index.py`, `navmesh_decode.py`,
  `navmesh_links.py`, `navmesh_nodes.py`, `navmesh_cell_fit.py`, `navmesh_tile_obj.py`, `bvh_index.py`,
  `bvh_groups.py`, `bvh_match.py`, `bvh_rehydrate.py`, `bvh_geometry_check.py`, `bvh_packing_check.py`,
  `bvh_slot_check.py`.
- **Textures**: `tex_headers.py`, `tex_export.py`, `tex_mode_probe.py`, `tile_probe.py`, `swizzle_search.py`,
  `amd_swizzle.py`, `bc_feature.py`, `calibrated_texture.py`, `bcpack.py`, `bcpack_tables.py`,
  `bcpack_tables_bptc.py`, `bcpack_tool.py`, `bcpack_history_calib.py`, `ttpf_assets.py`, `ttpf_mips.py`,
  `dds_png.py`, `mask_channels.py`.
- **Audio and movies**: `awc_manifest.py`, `awc_opus.py`, `awc_shorts.py`, `awc_names.py`, `awc_markers.py`,
  `awc_cue_labels.py`, `audio_config.py`, `audio_wav.py`, `opus_tags.py`, `rsc8_audio.py`, `bink_index.py`,
  `bink_names.py`, `bink_play.py`.

## Full table

| File | Lines | First docstring line |
|---|---:|---|
| `tools/ab_control_flow.py` | 400 | GPU A/B of control flow: render branchy pixel shaders translated both ways, as one loop around a switch |
| `tools/amd_swizzle.py` | 300 | AMD GFX10/RDNA2 texture detiling from addrlib's published swizzle patterns. |
| `tools/analyze_4240_full.py` | 344 | Analyze '42 40 00 00' vertex blocks in RPF9 archives (ASSET-57). |
| `tools/anim_clips.py` | 1593 | Decode the clip dictionaries in anim.rpf: clips, animations and their keyframe channels. |
| `tools/anim_glb.py` | 819 | Export a skinned character part with its skeleton and decoded clips as an animated binary glTF (.glb). |
| `tools/anim_index.py` | 633 | Index the animation containers in an RPF9 data area or a folder of carved streams. |
| `tools/anim_move.py` | 659 | Decode the MoVE (version 19) state-machine and motion-tree containers in anim.rpf (ASSET-86). |
| `tools/anim_rigs.py` | 194 | Read the rig dictionaries in anim.rpf and join the RBF0 rig-set manager's entries to them (ASSET-20). |
| `tools/anim_skel_match.py` | 174 | Match the bone names in anim.rpf's bone-table streams against the skeletons of packs.rpf (ASSET-231). |
| `tools/anim_skeleton_match.py` | 188 | Match 'anim.rpf' 'SKEL_*' bone lists against decoded skeleton name tables in 'packs.rpf' (ASSET-231). |
| `tools/audio_config.py` | 303 | Read the music/radio record table from the game's inflated audio-config blob. |
| `tools/audio_wav.py` | 573 | Decode Ogg Opus audio to 16-bit PCM WAV with ffmpeg. |
| `tools/awc_cue_labels.py` | 55 | Convert one exported cue_regions.json to per-channel Audacity label tracks. |
| `tools/awc_manifest.py` | 289 | Write a readable manifest of the streams in AWC audio banks, without decoding or exporting audio. |
| `tools/awc_markers.py` | 241 | Count the marker events (tag 0xBD) of AWC banks: layout checks, type hashes and counts, never audio or names. |
| `tools/awc_names.py` | 460 | Name AWC streams by matching dictionary words against their 29-bit joaat stream ids. |
| `tools/awc_opus.py` | 552 | Repackage the Opus streams of AWC audio banks into standard Ogg Opus files (lossless: no re-encoding). |
| `tools/awc_shorts.py` | 221 | Export short streamed-music channels (station idents, stings, DJ links) from AWC banks. |
| `tools/bary_fallback_gs.py` | 213 | Pass-through geometry shaders that feed rdna2spirv.py --bary-fallback pixel shaders. |
| `tools/batch_glb.py` | 285 | Batch converter from Wavefront OBJ files to binary glTF 2.0 (.glb). |
| `tools/bc_feature.py` | 54 | Vectorised per-block average colour for BC1 and the colour half of BC3. |
| `tools/bcpack.py` | 1488 | Clean-room BCPack bitstream decoder (Python standard library only). |
| `tools/bcpack_history_calib.py` | 2791 | Local pure-Python BCPack initial-history calibration and validation tool. |
| `tools/bcpack_tables.py` | 493 | Fixed codec constant tables for the clean-room BCPack decoder. |
| `tools/bcpack_tables_bptc.py` | 418 | Public BPTC (BC6H and BC7) specification tables and runtime history loader. |
| `tools/bcpack_tool.py` | 271 | DirectStorage BCPack bitstream inspection, decoding and profiling tool. |
| `tools/bink_index.py` | 449 | Index Bink 2 streams in an RPF archive or a directory without extracting them. |
| `tools/bink_names.py` | 481 | Name the Bink 2 streams of an RPF archive from the game's own mount lists. |
| `tools/bink_play.py` | 174 | Play a game movie by name, straight from its archive, with runtime/test/bink_play. |
| `tools/boot_trace.py` | 416 | Summarize 'gta6rt''s two first-boot traces: the startup JSONL and the '[d3d12-trace]' lines. |
| `tools/buffer_bounds_probe.py` | 258 | SHADER-341: native GFX10 MUBUF 'OOB_SELECT' bounds probe. |
| `tools/buffer_format_fuzz.py` | 666 | Hardware oracle for the GFX10 buffer data formats a V# or MTBUF can name (standard library only). |
| `tools/buffer_store_probe.py` | 405 | Measure RDNA2 (GFX10) typed buffer store rounding and 2-component DST_SEL swizzling natively (SHADER-364). |
| `tools/buffer_swizzle_probe.py` | 668 | SHADER-343: Native RDNA2 hardware probe for swizzled buffer addressing (V# SWIZZLE_ENABLE, ADD_TID_ENABLE). |
| `tools/bvh_geometry_check.py` | 211 | Check whether geometry given to a BLAS rehydration is the geometry the BLAS was built from. |
| `tools/bvh_gpu_smoke.py` | 320 | Run XboxBvhBuilder.dll's translated compute programs on the GPU with synthetic inputs (TEST-594). |
| `tools/bvh_groups.py` | 313 | Decode the '0x5000xxxx' descriptor streams of a '*_bvh4.rpf' archive and tie them to their BVH buffers. |
| `tools/bvh_index.py` | 582 | Index the payloads of an RPF9 archive (or a directory of payloads) for BVH4 data. |
| `tools/bvh_match.py` | 418 | Find the drawable geometry a dehydrated BLAS was built from, over 'rpf9dump''s model export. |
| `tools/bvh_packing_check.py` | 549 | Check the dehydrated BVH4 packing of docs/bvh.md §4a on every stream of a pack, without geometry. |
| `tools/bvh_rehydrate.py` | 543 | Rehydrate a dehydrated XDXR BVH4 bottom-level acceleration structure on the CPU. |
| `tools/bvh_slot_check.py` | 341 | Compare multi-slot BVH transforms against drawable groups selected by topology. |
| `tools/bvh_tri_probe.py` | 1064 | Measure the triangle step of image_bvh_intersect_ray / image_bvh64_intersect_ray natively on RDNA2 (node types 0-3). |
| `tools/calibrated_texture.py` | 443 | Numerical reconstruction with an explicitly supplied local calibration profile. |
| `tools/cf_diff.py` | 686 | FUZZ-8: differential fuzzer for control flow and EXEC (standard library only). |
| `tools/cf_ref.py` | 377 | Wave-level CPU reference for RDNA2 control flow (standard library only). |
| `tools/cfg_structure.py` | 349 | Arrange a control-flow graph as nested loops and conditionals driven by a block-number variable. |
| `tools/character_census.py` | 937 | Census decoded character models: counts, materials, textures, bones and skin layout. |
| `tools/character_textures.py` | 406 | Classify the texture bindings of decoded character ''model.json'' materials. |
| `tools/classify_carve.py` | 140 | Group every carved file by signature, using only the per-archive index.json (no file reads). |
| `tools/content_definitions.py` | 242 | Read local content-definition XML (ASSET-149), without resolving references. |
| `tools/cpu_ref.py` | 872 | CPU reference for a small, straight-line RDNA2 subset. |
| `tools/crosslane_fuzz.py` | 1759 | Differential fuzzer for the translator's wave and cross-lane operations (FUZZ-7 in #156 / #374). |
| `tools/cvt_f16_omod_probe.py` | 495 | Measure RDNA2 v_cvt_f16_f32 VOP3 output modifiers (mul:2, mul:4, div:2, clamp) across MODE settings. Standard library only. |
| `tools/cvt_norm_mods_probe.py` | 667 | Measure RDNA2 VOP3/SDWA output modifiers on v_cvt_norm_{i16,u16}_f16 and v_cvt_pknorm_{i16,u16}_f32. |
| `tools/cvt_pknorm_f16_probe.py` | 685 | Measure RDNA2 v_cvt_pknorm_i16_f16 and v_cvt_pknorm_u16_f16 on native hardware. Standard library only. |
| `tools/d16_format_probe.py` | 360 | How an RDNA2 GPU rounds UNORM/SNORM buffer loads to f16 with d16 (BUILD-161). Standard library only. |
| `tools/d16_image_probe.py` | 422 | How an RDNA2 GPU rounds d16 image results to f16 (point loads, point samples and bilinear samples). Standard library. |
| `tools/d3d12_guid_sites.py` | 423 | Which GUID constants does a PE image's code pass at its call sites? (D3D-441, stdlib only) |
| `tools/d3d12_iid_scan.py` | 200 | Which D3D12 interfaces can a PE image ask for? (D3D-350, stdlib only) |
| `tools/d3d12_xs_surface.py` | 629 | The measured Xbox Series ''d3d12_xs.dll'' export surface, with provenance. |
| `tools/dds_png.py` | 1553 | Pure standard-library DDS to PNG decoder and 2x box-filter downscaler. |
| `tools/div_fixup_diff.py` | 1035 | Differential fuzzer for v_div_fixup_f16, v_div_fixup_f32 and v_div_fixup_f64 (standard library only). |
| `tools/draw_ownership.py` | 261 | ASSET-70 draw-ownership tool: system-segment pointer layers, synthetically tested. |
| `tools/ds_permute_probe.py` | 258 | Measure RDNA2 (GFX10) 'ds_permute_b32' natively via 'tools/hw_oracle' (SHADER-133). |
| `tools/exe_checklist.py` | 220 | The things to confirm against the game executable: every ASSUMPTION, UNVERIFIED and VERIFY note, in one list. |
| `tools/f16_trans_sweep.py` | 517 | Every f16 input through the f16 transcendentals on an AMD GPU (FUZZ-29). Standard library only. |
| `tools/f16_widen_probe.py` | 355 | Measure how RDNA2 (GFX10) widens a half-precision NaN in a typed buffer load (FUZZ-5 follow-up). |
| `tools/f32_fuzz.py` | 1770 | Differential fuzzer for the translator's 32-bit float VALU (FUZZ-3; standard library only). |
| `tools/f64_diff.py` | 811 | Differential checks for the 47 binary64 VALU instruction forms translated by main. |
| `tools/f64_omod_probe.py` | 340 | Measure the VOP3 output modifiers of the binary64 conversion, frexp and div_fixup VALU ops across MODE settings. Standard library only. |
| `tools/feature_scan.py` | 666 | Scan the shader corpus for features the D3D12 -> Vulkan layer may need (docs/d3d12_vulkan.md §8.2 #10, #14, #16). |
| `tools/file_mode.py` | 41 | Permission bits for a file published through a private staging file. |
| `tools/find_hash_lookup.py` | 166 | Rank candidate hash-lookup functions in a PE image (docs/modding.md, "Day one: finding the hook"). |
| `tools/first_light.py` | 1167 | First light: draw an exported game model with a selected pair of translated game shaders. |
| `tools/fma_mix_literal_probe.py` | 280 | Does a v_fma_mix source with op_sel_hi and op_sel set read the high half of a 32-bit literal? A probe, standard |
| `tools/fma_mix_probe.py` | 372 | Does v_fma_mixlo_f16 / v_fma_mixhi_f16 round once or twice to nearest even? A probe, standard library only. |
| `tools/fmisc_diff.py` | 874 | Differential fuzzing of the 32-bit float VALU ops no other fuzzer generates, and of float compares. Standard |
| `tools/fog_volumes.py` | 456 | Scan RPF9 archives or carved 0x7a files for CScatteringTransmittance__FogVolumeListData resources. |
| `tools/fragment_children.py` | 377 | Census the child drawables of the fragments in an RPF9 archive (ASSET-261). |
| `tools/frexp_ldexp_f16_mods_probe.py` | 606 | Measure RDNA2 VOP3 output modifiers on v_frexp_mant_f16, v_frexp_exp_i16_f16, and v_ldexp_f16. Standard library only. |
| `tools/fspx_prologue_probe.py` | 319 | TEST-849: RDNA2 hardware probe for the 36-byte FSPX shader prologue. |
| `tools/fspx_unpack.py` | 291 | Unpack the native compute programs in FSPX payloads: the ACCC part of the shader containers that the package's |
| `tools/fssm_header.py` | 406 | Read the pixel-shader input table from an FSSM blob. |
| `tools/fssm_unpack.py` | 238 | Unpack the native GPU program from each carved shader container. |
| `tools/gallery.py` | 547 | Standalone local 3D Web Studio & Texture Gallery server for Leonida. |
| `tools/gamedata_blobs.py` | 427 | Decode the non-XML text and binary payloads of common.rpf and join to shaders.rpf. |
| `tools/gamedata_graph.py` | 646 | Build a content-set and reference graph for the ''common.rpf'' XML catalog. |
| `tools/gamedata_index.py` | 508 | Index a carved RPF9 archive by payload type and XML root. |
| `tools/gamedata_maps.py` | 567 | Decode the MHIU and PAMH (HMAP) heightmaps in a carved RPF9 archive. |
| `tools/gamedata_reader.py` | 339 | Stream the game-data XML of a carved RPF9 archive into Python values, dispatching on root tag and ''type''. |
| `tools/gamedata_sps1.py` | 405 | Decode the SPS1 shader-preset table in common.rpf and cross-check with shaders.rpf. |
| `tools/gamedata_xml.py` | 748 | Catalog the XML documents recovered from a carved RPF9 archive. |
| `tools/gbuffer_census.py` | 146 | Count declared G-buffer signatures and static exports in an indexed shader carve. |
| `tools/gdk_runtime_calls.py` | 1076 | List the GDK runtime classes, interfaces and vtable slots a PE image calls through xgameruntime.lib's thunks. |
| `tools/gen_d3d12_stubs.py` | 227 | Generate logging stubs for D3D12 COM interfaces from the Windows SDK's d3d12.h. |
| `tools/gen_gdk_runtime_classes.py` | 148 | Build tools/gdk_runtime_classes.json from a clone of xodus-gaming/xgameruntime-docs. |
| `tools/glb_inspect.py` | 732 | Inspect a glTF 2.0 binary (.glb): meshes, materials, skins, animations and POSITION bounds. |
| `tools/glb_skinning.py` | 1160 | Validate the skinning data in a binary glTF 2.0 (.glb) file. |
| `tools/glb_writer.py` | 1409 | Pure standard-library Wavefront OBJ parser and binary glTF 2.0 (.glb) writer. |
| `tools/hashdict.py` | 234 | Build a joaat (Jenkins one-at-a-time, lowercased) hash -> string dictionary from every string we can find. |
| `tools/heap_index_census.py` | 466 | Where does a directly heap-indexing shader get the descriptor-heap base? (D3D-329, stdlib only) |
| `tools/image_bvh_census.py` | 951 | Census of the image_bvh_intersect_ray / image_bvh64_intersect_ray call sites (counts only, standard library). |
| `tools/image_d16_probe.py` | 359 | How an RDNA2 GPU narrows 32-bit integer texels in d16 image loads, per image dimension (SHADER-387). Standard |
| `tools/image_fuzz.py` | 703 | Differential fuzzer for the translator's image path, starting with 'image_load' (standard library only). |
| `tools/image_query_probe.py` | 370 | What 'image_get_resinfo' and 'image_get_lod' return on an RDNA2 GPU (SHADER-388). Standard library only. |
| `tools/import_call_sites.py` | 173 | Count and locate the direct calls to chosen imports of an x86-64 PE image, from the file alone. |
| `tools/inline_scan.py` | 239 | Find the inline command-list writes in an x64 image and say whether each one checks the cursor limit first. |
| `tools/instance_lists.py` | 396 | Count and check the instanced props and grass in map resources (CMapData instancedData). |
| `tools/int_hw_probe.py` | 1016 | SHADER-137: RDNA2 hardware probe for the integer contract rows checked only on the CPU. |
| `tools/interp_hw_probe.py` | 292 | SHADER-430: what the RDNA2 v_interp ALU ops compute from the attribute words, measured on an AMD GPU. |
| `tools/isa_coverage.py` | 461 | RDNA2 ISA coverage of the translator (tools/rdna2spirv.py), measured against AMD's machine-readable ISA XML. |
| `tools/lane_masks.py` | 304 | Which scalar registers hold a lane mask? |
| `tools/lds_fuzz.py` | 1276 | Differential fuzzer for the translator's LDS and ds_* instructions (FUZZ-6), standard library only. |
| `tools/leonida_mods.py` | 1068 | Install and manage Leonida mods (docs/modding.md). |
| `tools/leonida_tui.py` | 1165 | Stdlib LEONIDA build console: setup form + switchable per-stage log panes. |
| `tools/mad_denorm_probe.py` | 342 | Measure RDNA1 f32 denormal flush in v_mad_f32, v_mac_f32 and v_mad_legacy_f32 (SHADER-340). |
| `tools/manifest.py` | 280 | SHA-256 manifest of a game folder, so later copies (and the exe, when it arrives) can be checked. |
| `tools/manifest_verifier.py` | 612 | Parallel SHA-256 manifest integrity verifier with progress reporting. |
| `tools/mask_channels.py` | 128 | Per-channel counts for a mask texture: which channels carry content and what shape it has. |
| `tools/mc_command.py` | 100 | Resolve LLVM_MC to an argv list. |
| `tools/mem_diff.py` | 841 | Differential fuzzer for the translator's memory instructions (FUZZ-5; standard library only). |
| `tools/mesh_compute_vs.py` | 311 | Vertex shaders that draw what rdna2spirv.py --mesh-compute mesh shaders wrote. |
| `tools/meshlet_page.py` | 1023 | Index the '63 30 00 00' mesh pages (vertex blocks plus meshlet groups) in carved streams or an RPF9 archive. |
| `tools/mission_tables.py` | 601 | Read mission-state tables: 32-byte state records and the 64-byte pool elements they point at. |
| `tools/mode_round_probe.py` | 303 | Measure RDNA2 (GFX10) f16/f64 results under MODE.FP_ROUND[3:2] nearest-even vs toward-zero (SHADER-132). |
| `tools/navmesh_cell_fit.py` | 222 | Measure the cell grid the local-space navmeshes of ''navmeshes.rpf'' were built on (ASSET-332, docs/navmesh.md). |
| `tools/navmesh_decode.py` | 1145 | Decode the resource classes in a navmesh archive (''navmeshes.rpf'') without the encrypted TOC. |
| `tools/navmesh_index.py` | 363 | Index the RPF9 resource page-directory streams in a navmesh archive or a carve folder. |
| `tools/navmesh_links.py` | 623 | Decode and verify navmesh +0x88 adjacent-polygon edges and +0xA8/+0xB0 special links. |
| `tools/navmesh_nodes.py` | 348 | Count which navmesh polygon flags lie under the lane-graph nodes. |
| `tools/navmesh_tile_obj.py` | 76 | Export one static navmesh tile's polygon boundaries to a private Wavefront OBJ. |
| `tools/odrp_7a.py` | 1265 | Distinguish and parse standalone '0DRP'/'0x7a' resources vs 'PSIN' 'LRSC' records. |
| `tools/op_reach.py` | 198 | How many shaders of a disassembled corpus use each instruction, all encodings of it counted as one. |
| `tools/opus_tags.py` | 278 | Write stream names, dialogue labels and subtitles into the Ogg Opus files that awc_opus.py exports. |
| `tools/page_index.py` | 494 | Index RPF9 '0DRP' page directories without the encrypted table of contents. |
| `tools/pal_stages.py` | 273 | Read the PAL stage descriptors that head an FSSM blob. |
| `tools/pe_inspect.py` | 569 | Inspect a PE image (exe or dll) for porting work. |
| `tools/pe_shader_containers.py` | 329 | Inventory the DXBC shader containers embedded in a PE image, outside 'shaders.rpf'. |
| `tools/ped_assemble.py` | 2457 | Modular ped body assembler, standing-space aligner, and coplanar seam stitcher. |
| `tools/ped_catalogues.py` | 1618 | Parse and resolve resource-packed '0DRP' v122 and standalone 'PSIN' ped appearance catalogues ('0x8dcb4cf5'). |
| `tools/pk16_diff.py` | 613 | Differential fuzzing of packed and 16-bit integer VALU (v_pk_* integer ops, 16-bit VOP3 integer ops). Standard |
| `tools/pk_int16_clamp_probe.py` | 420 | Measure RDNA2 VOP3P clamp behavior on v_pk_mul_lo_u16 and v_pk_{max,min}_{u16,i16}. Standard library only. |
| `tools/pknorm_probe.py` | 232 | What do v_cvt_pknorm_i16_f32 / v_cvt_pknorm_u16_f32 give for NaN on RDNA2? A probe, standard library only. |
| `tools/pool_workers.py` | 19 | How many worker processes one pool can have on this platform. |
| `tools/probe_gfx1036_vcmp16.py` | 370 | Probe gfx1036 silicon across wave32 and wave64 for 16-bit vector compares: |
| `tools/progress_dashboard.py` | 1056 | Progress dashboard: what works, what changed and what blocks next, from outcomes the project already produces. |
| `tools/ps_helper_probe.py` | 289 | SHADER-681: measure pixel-shader helper lanes at a triangle edge on an AMD GPU (raw RDNA2 ISA, ps_oracle.py). |
| `tools/ps_interp_modes.py` | 482 | Decode pixel-shader interpolation modes from a DXBC container and its FSSM sidecar. |
| `tools/ps_oracle.py` | 858 | Run hand-written RDNA2 pixel-shader ISA on an AMD GPU: a hardware oracle for what only a pixel shader can do. |
| `tools/ps_sweep.py` | 573 | Draw a stratified sample of translated pixel shaders on one GPU at a time and compare two GPUs. |
| `tools/psin_data.py` | 625 | Decode the instance data of PSIN containers: the data blocks between the header and LSCH, read through the schema. |
| `tools/psin_index.py` | 468 | Index the PSIN metadata container used by the game's data and script archives. |
| `tools/psin_names.py` | 254 | Resolve the name-hash fields of PSIN instance data against a local list of resource names. |
| `tools/psin_psch.py` | 524 | Decode the PSO-style PMAP/PSCH/PSIG/STNS PSIN containers (docs/psin.md). |
| `tools/psocache_extract.py` | 316 | Carve the PSO/shader cache out of an RPF9 archive, reproducibly and without the encrypted TOC. |
| `tools/psocache_inspect.py` | 767 | Check a PSO/shader cache blob against the measured 0x80/0x70/0x18 layout. |
| `tools/psocache_usersig.py` | 464 | Describe the root-signature / user-data side of the graphics PSO records in a PS\0\x09 cache. |
| `tools/ptr_truncation_census.py` | 350 | Census of the pointers whose high dword a shader forces to zero (stdlib only, no SPIR-V written). |
| `tools/rbf0_encode.py` | 327 | Encode an RBF0 tree document back to bytes, and check that decoding and encoding give the original resource. |
| `tools/rbf0_index.py` | 567 | Index RBF0 resources in an RPF9 data area or a folder of carved streams. |
| `tools/rbf0_tree.py` | 767 | Decode RBF0 resources into strict JSON trees, with an optional CSV node table and simple XML rendering. |
| `tools/rdna2_decode.py` | 1447 | Pure-Python RDNA2 (GFX10.1 / GFX10.3) binary ISA decoder (no LLVM dependency). |
| `tools/rdna2_isa_tables.py` | 1537 | RDNA2 (GFX10.1/GFX10.3) opcode tables for tools/rdna2_decode.py. |
| `tools/rdna2spirv.py` | 18877 | RDNA2 (gfx10) shader -> SPIR-V assembly. |
| `tools/recomp_census.py` | 222 | Recompiler census over a PE image's .pdata. |
| `tools/replace_retry.py` | 33 | os.replace() that rides out the short windows in which Windows refuses to rename a file it has just seen written. |
| `tools/report_paths.py` | 93 | Do two report paths name the same file? One answer for the tools that refuse an output that would overwrite |
| `tools/restofworld_index.py` | 578 | Index the stream signatures in the rest-of-world and script RPF9 archives. |
| `tools/restofworld_model_join.py` | 1310 | Join RPF9 model system pages to their graphics pages without the encrypted TOC. |
| `tools/root_sig_contract.py` | 278 | Reconcile a root signature against the translator's user-SGPR reads (stdlib only). |
| `tools/root_spill_census.py` | 187 | Census of root-signature spill evidence in the translated corpus (stdlib only). |
| `tools/rootsig_census.py` | 343 | Census of the root signatures the package carries next to a shader, and the two packing models (stdlib only). |
| `tools/rpf9_carve.py` | 287 | Carve the zlib-compressed files out of an RPF9 archive without its (encrypted) table of contents. |
| `tools/rpf9_names.py` | 278 | Index named RPF9 resources without the encrypted table of contents. |
| `tools/rpf_probe.py` | 137 | Survey the .rpf archives in a game folder: version magic, header words, and whether the table looks encrypted. |
| `tools/rsc8_audio.py` | 2104 | Decode RSC8 (version 8001) facial/lip-sync animation clips embedded in AWC (ADAT) speech banks. |
| `tools/rtti_scan.py` | 550 | Recover C++ class names, hierarchies and vtables from an x64 MSVC PE via RTTI. |
| `tools/sad_diff.py` | 619 | Differential fuzzer for the RDNA2 sum-of-absolute-differences family (standard library only). |
| `tools/salu_diff.py` | 816 | Differential fuzzing of the scalar ALU and SCC (FUZZ-4 in #156). Standard library only. |
| `tools/sampler_lod_probe.py` | 248 | SHADER-539: measure on an AMD GPU (tools/hw_oracle) which mip level image_sample_l and image_sample_lz read under the |
| `tools/sampler_source_census.py` | 624 | Where does the S# of every sampling instruction come from? (SHADER-735, stdlib only) |
| `tools/sandbox.py` | 174 | Real-shader sandbox: walk, fly or drive through placed props drawn by the game's own translated shaders. |
| `tools/sandbox_scene.py` | 208 | Pack the placed props around one spot into a scene file for runtime/test/vk_sandbox. |
| `tools/save_keys.py` | 366 | save_keys: collect the hashed '$0x…' keys of GTA VI saves and resolve them against a local name dictionary. |
| `tools/saves.py` | 707 | saves.py: inspect, extract, rebuild and import the game's save containers (ASAV / JSAV / SLAYER1). |
| `tools/script_qtable.py` | 628 | Decode the 0x71717171-tagged ('qqqq') object-tree container in script.rpf. |
| `tools/sdwa_diff.py` | 831 | Differential fuzzing of SDWA (sub-dword addressing) VALU forms. Standard library only. |
| `tools/semantic_mutants.py` | 276 | Op-scoped mutation testing for a tests/semantic file: which one-line translator bugs would it miss? |
| `tools/sgd5_effects.py` | 277 | Look up a stored effect/technique pass in the Xbox 1.0.2.1 shaders.rpf. |
| `tools/shader_carve.py` | 155 | Carve shader containers out of an RPF9 archive without reading its (encrypted) table of contents. |
| `tools/shader_diff.py` | 391 | Differential oracle for the RDNA2 translator (standard library only). |
| `tools/shader_disasm.py` | 286 | Disassemble the native RDNA2 code in unpacked FSSM shader blobs with LLVM's AMDGPU disassembler. |
| `tools/shader_ir.py` | 314 | Parse LLVM AMDGPU disassembly (.s from shader_disasm.py) into a structured instruction list. |
| `tools/shader_library.py` | 395 | Unpack the pipeline library in shaders.rpf: the zlib part of the archive that tools/shader_carve.py does not read. |
| `tools/shader_link_check.py` | 350 | Check user-defined varying interfaces between translated graphics stages. |
| `tools/shader_meta.py` | 759 | Static build-time metadata extracted from RDNA2 disassembly, DXBC containers and FSSM blobs. |
| `tools/shader_profile.py` | 419 | Profile the disassembled shader corpus: which RDNA instructions the translator must support. |
| `tools/shader_stage_manifest.py` | 43 | Read shader names from a completed carve or unpack index. |
| `tools/skin_streams.py` | 516 | Measure the vertex skin streams (joint indices and weights) of recovered drawable models (ASSET-12). |
| `tools/skinned_glb.py` | 1085 | Export a skinned GLB from a decoded RAGE pack model and its 8-influence skin streams. |
| `tools/smem_neg_probe.py` | 166 | SHADER-336: measure s_load / s_buffer_load with sOFF + IMM < 0 on an AMD GPU (tools/hw_oracle), and compare |
| `tools/smh_groom.py` | 1968 | Clean-room decoder, strand generator, census tool and GLB exporter for RAGE "smh" strand grooms. |
| `tools/spirv_asm.py` | 645 | Pure-Python SPIR-V binary assembler (standard library only, no external spirv-as). |
| `tools/spirv_asm_tables.py` | 1108 | SPIR-V core and GLSL.std.450 grammar tables for tools/spirv_asm.py. |
| `tools/spirv_gs_to_compute.py` | 407 | Lower a translated geometry shader to a compute "capture" pass, for GPUs without a geometry stage (SHADER-159). |
| `tools/spirv_interface.py` | 534 | Reflect the user-defined graphics interfaces in a binary SPIR-V module. |
| `tools/spirv_metal_set0.py` | 216 | Give set 0's descriptor arrays fixed, non-overlapping Metal argument-buffer IDs (SHADER-161). |
| `tools/spirv_opt.py` | 1203 | Sound dominator-tree SPIR-V optimizer for tools/rdna2spirv.py output (#884). |
| `tools/spirv_stubs.py` | 316 | Generate minimal SPIR-V partner stubs for graphics pipeline compilation checks. |
| `tools/story_index.py` | 408 | Index the story/minigame resource pages in an RPF9 data area or a folder of carved streams. |
| `tools/swizzle_search.py` | 178 | Search for the in-tile swizzle of a GPU texture by brute force. |
| `tools/tex_export.py` | 265 | Export textures found by tex_headers.py as standard DDS files (top mip, slice 0), detiled from AMD SW_4K_S. |
| `tools/tex_headers.py` | 939 | Find texture headers in carved CPU-segment blobs and pair them with their GPU blob. |
| `tools/tex_mode_probe.py` | 148 | For textures with known size/format/mips, find which AMD swizzle mode and top-mip placement decodes cleanly. |
| `tools/texoff_probe.py` | 168 | SHADER-337: measure the y and z texel-offset fields of image_sample_lz_o on an AMD GPU (tools/hw_oracle). |
| `tools/text_tables.py` | 575 | Decode the localized string tables and build the dialogue subtitle table. |
| `tools/tile_probe.py` | 204 | Find the memory layout of a GPU texture blob empirically. |
| `tools/timecycle_pairs.py` | 319 | Measure the two-number pairs in plain timecycle_modifier_data entries. |
| `tools/ttpf_assets.py` | 213 | Complete the rpf9dump texture-pool manifest during the assets build. |
| `tools/ttpf_mips.py` | 2247 | Measure and decode TTPF (RDS5) textures: 2D compact mip chains, 2D arrays, |
| `tools/turing_atlas.py` | 298 | Turing bring-up atlas for Leonida (stdlib only, run with python -I). |
| `tools/user_sgpr_census.py` | 286 | Census of user-SGPR reads per shader stage, from the translator's index.json (stdlib only). |
| `tools/valu_fuzz.py` | 1248 | Differential fuzzer for the translator, two instruction families on the GPU (FUZZ-2, FUZZ-5), standard |
| `tools/valu_int_diff.py` | 561 | Differential fuzzer for the translator's 32-bit and 16-bit integer VALU instructions (standard library only). |
| `tools/vcmp16_opsel_probe.py` | 196 | TEST-650: measure on an AMD GPU (tools/hw_oracle) whether VOP3 op_sel picks the half of each source on the 16-bit |
| `tools/veh_spawn_intensities.py` | 168 | Aggregate census of the 'VehSpawnIntensities' table in 'CGraphicsDefinitionsData'. |
| `tools/vertex_types.py` | 771 | Measure the component types of the vertex streams in '42 40 00 00' blocks (follow-up to ASSET-57). |
| `tools/vint_diff.py` | 649 | Differential fuzzing of 32-bit integer VALU in every encoding and source kind. Standard library only. |
| `tools/vinterp_hw_probe.py` | 578 | SHADER-430: RDNA2 hardware probe for 32-bit and 16-bit v_interp reads on a packed parameter channel. |
| `tools/vk_fuzz_run.py` | 213 | Run assembled RDNA2 programs through the translator on a real Vulkan GPU (standard library only). |
| `tools/vk_subgroup_info.py` | 402 | Ask the Vulkan driver whether this PC's GPU can run 64-wide compute and mesh subgroups. |
| `tools/wave32_pixel_scan.py` | 292 | Count the wave32 pixel shaders whose results can depend on the subgroup being 32 lanes wide. |
| `tools/wave64_emulate_opt.py` | 1812 | Exact CFG/dataflow optimizations for --wave64-emulate (Issue #884). |
| `tools/wave64_graphics_scan.py` | 201 | Count candidate cross-half wave ops in wave64 graphics shaders over the whole corpus. |
| `tools/wave64_halves.py` | 1777 | Wave64 vertex, pixel and geometry shaders on 32-wide subgroups: is the program partition invariant? (SHADER-10) |
| `tools/wave64_pipeline_audit.py` | 206 | Audit graphics subgroup-width fidelity from a translator index and Vulkan device properties. |
| `tools/wave_diff.py` | 531 | CPU-only differential fuzzer for wave and cross-lane operations (standard library only, no GPU, no llvm-mc). |
| `tools/wint_diff.py` | 939 | Whole-wave differential fuzzing of integer VALU under random EXEC masks. Standard library only. |
| `tools/world_archetypes.py` | 319 | Read archetype definitions (CMapTypes / CBaseArchetypeDef) from 0x7a resources, and check map extents. |
| `tools/world_build.py` | 2016 | Build streamed spatial world.json and .glb chunks from rpf9dump asset exports. |
| `tools/world_census.py` | 897 | Census every 0x7a map resource in an RPF9 archive, with measured byte coverage. |
| `tools/world_drawable_lookup.py` | 135 | Predict model export offsets from local world_archetypes and rpf9_names reports. |
| `tools/world_drawable_verify.py` | 109 | Verify single-offset world_drawable_lookup predictions against normal model exports. |
| `tools/world_grid_atlas.py` | 697 | Place the tile textures of the GridAtlasInfo map grids and check what the grid documents' zInflation box covers. |
| `tools/world_index.py` | 931 | Index world metadata and map-placement resources in an RPF9 archive or a folder of carved files. |
| `tools/world_interior_names.py` | 422 | Resolve the interior keys of the location-name table to interior archetypes and their placements. |
| `tools/world_lights_paths.py` | 881 | Measure the map lights and the vehicle lane graph in the 0x7a world resources of an RPF9 archive. |
| `tools/world_names.py` | 683 | Resolve 0x7a placement name hashes against locally recovered model and map names. |
| `tools/x64_recomp.py` | 2623 | x86-64 to arm64 static recompiler groundwork (the mobile "custom emulator" core). |
| `tools/xcl_compare.py` | 511 | Run XCL's synthetic test title under gta6rt and under XCL, and compare the results. |
| `tools/xdxr_bvh.py` | 1082 | Clean-room reference encoder, validator and traversal for the Xbox XDXR BVH4 runtime layout. |
| `tools/zero_span.py` | 36 | Classify the bytes between two carved streams without copying them. |
| `scripts/audit_shader4_regs.py` | 446 | Audit initial SGPR/VGPR read-before-write registers and RSRC2/PSV0 headers across CS, MS, and GS shaders. |
| `scripts/bench_wave64.py` | 165 | Time a wave64 compute shader (runtime/test/shaders/cs_bench_mix.asm) on AMD/NVIDIA or the current Vulkan device. |
| `scripts/check_apt_key.py` | 93 | Check that a downloaded apt signing key is the one CI pins, before apt is told to trust it. |
| `scripts/check_no_game_data.py` | 290 | Fail when a tracked file starts like game data, whatever its name. |
| `scripts/ci_expected_failures.py` | 62 | Judge a CI test run that has known failures, each fixed by an open pull request. |
| `scripts/dev.py` | 125 | Use development tools from this Leonida checkout. |
| `scripts/merge_check.py` | 216 | Before a merge round: is a pull request already on main, and would merging it again duplicate text? |
| `scripts/run_gpu_tests.py` | 1654 | Assemble, translate, and run the synthetic RDNA2->SPIR-V GPU test suite on Vulkan. |
| `scripts/run_guest_scene.py` | 365 | Run the 600-frame synthetic Win32 guest scene headless in full, save and resume mode and check the results. |
| `scripts/run_semantic_tests.py` | 88 | Run the translator regression suite (tests/semantic) one process per test file, several files at a time. |
| `scripts/run_wine_tests.py` | 413 | Run the Windows build's CTest tests under Wine, on Linux. |
| `scripts/shader_stats.py` | 217 | How efficiently the driver compiles the translated shaders, against the game's own RDNA code. |
| `scripts/vs_input_survey.py` | 188 | SHADER-33: which hardware vertex-shader layout the corpus uses (legacy VS or NGG), and which input VGPRs it reads. |
| `scripts/vs_layout_census.py` | 221 | SHADER-33 follow-up: which PAL hardware stage every FSSM payload runs on, and whether the VS input layout holds. |
