# Alien: Isolation (`010075D00E8BA000`)

Base build 1.1_46609 (v0), Nintendo SDK 8.2.0. Feral's Switch port uses NVN, Havok and Wwise. The profile registers the base build and loads `main`, the movie library (`subsdk0`), GLSLC (`subsdk1`) and `sdk`.

## Profile

- The NPDM declares no system resource, so the profile uses the 4 GB guest layout with 256 MB each for the alias and stack regions. The rewritten modules need more than the default 64 MB of used memory. The profile reserves 104 MiB for them.
- The offline split reads `resources/nvn_shader_cache`. Its NVNC v1 container holds 3,219 complete GLSLC outputs, which the loose-file GLSLC scanner already reads. The split registers 3,388 shader keys, including six compute and two geometry keys, and 3,211 vertex/pixel pairs.
- The game ships GLSL source and imports its compiler. In the boot tested here, the lookup at `main+0xddd20` hits the on-disk cache and bypasses `glslcCompile`. A miss can call the compiler at `main+0xd7f9c`. Every key observed during the capture boot was present in the offline split. Later cache misses remain untested.

## Found on this game

- [Opening the display named Default](../../../docs/reference/services.md#vi-layer-and-display-calls-without-vi-service-objects). The SDK hit a null service client at `sdk+0x3b65e4` during window setup.
- [Offline coverage after an empty GLSLC capture](../../../docs/reference/shaders.md#offline-coverage-after-an-empty-glslc-capture). The compiler is available but the boot serves shader programs from its cache.
- [Viewport indices in geometry compute records](../../../docs/reference/shaders.md#viewport-indices-in-geometry-compute-records). The two geometry shaders failed Metal compilation until the translator carried their viewport index in the emitted vertex record.

## Mac gameplay

On an M4 Mac running macOS 27.2, the registered base build played through the intro and menus to the first playable room on the Torrens. Hands-on play confirmed that the room and HUD rendered correctly. The run reached frame 5,700 with no crash or missing shader. Audio was verified in the earlier bring-up of the same game build. Start Alien: Isolation from the main menu, choose New Game and a difficulty, then continue through the intro. For the standalone development host, `DM_WINDOW=1 DM_AUDIO=1` opens a window and enables audio.

Later scenes, updates and iOS gameplay remain untested. The NVN device query warnings for items `0x60` and `0x61` were observed during the successful run.
