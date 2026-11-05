# AGENTS.md

## Project
- C/raylib port "rayboing" of XBoing. Sources in `src/*.c` + `include/*.h`; entry point `src/rayboing_demo.c` (`main`).
- `archive/` is original X11 XBoing reference source (not built).
- Root `audio.c` and `version.c` are legacy metadata, not compiled; real audio code is `src/audio.c`.

## Build (Premake 5)
- Requires a populated `raylib/` submodule. An empty `raylib/` (common without `--recurse-submodules`) breaks the build: `git submodule init && git submodule update`.
- `build_files/` is gitignored, but its hand-maintained `build_files/raylib.lua` is required by Premake; don't delete it.
- Generate: `./premake5 gmake` (Linux/macOS/W64Devkit) or `.\premake5.exe vs2022` (Windows).
- Build: `make` / `make rayboing` / `make raylib`; or `MSBuild.exe xboing.sln -t:rayboing`.
- Config: `make rayboing config=release` / `MSBuild.exe xboing.sln -t:rayboing -p:Configuration=Release`.
- `xboing.sln` references generated `build_files/*.vcxproj`; run Premake before opening/building.
- Output: `bin/{Debug,Release}/`.
- Linux deps: `sudo apt install build-essential libasound2-dev libx11-dev libxrandr-dev libxi-dev libgl1-mesa-dev libglu1-mesa-dev libxcursor-dev libxinerama-dev libwayland-dev libxkbcommon-dev`.
- Windows make builds must run from the W64Devkit console; MSBuild needs the "Desktop development with C++" workload.
- Windows Release is `WindowedApp` with entrypoint `mainCRTStartup`; Debug stays a console app (use Debug to see stderr).
- Premake enables `ShadowedVariables` and (MSVC) `_CRT_SECURE_NO_WARNINGS`.

## Run
- Run from the repo root so relative paths resolve.
- With no CLI argument, shows the intro screen, then defaults to `resource/levels/level01.data`; a provided filename is validated to exist.
- Needs `resource/` relative to CWD (levels, `resource/textures/...`, sounds).
- Scores are read/written as `scores.txt` in CWD.
- Audio: an audio-device failure returns true (silently disabled). Missing/unloadable sound files make `initAudioFiles()` return false, and `main` treats that as fatal. Sound paths in `src/audio.c` must exactly match disk filenames; Linux is case-sensitive, so keep `Doh1.mp3`/`Doh2.mp3`/... (not `doh1.mp3`).

## Verification
- No CI or test suite beyond `tests/compile-test.sh`, which compiles every `*.c` in CWD and breaks on the legacy root files; don't rely on it. Treat a successful `make rayboing` (or MSBuild target) as verification.

## Conventions
- Regenerate build files via Premake instead of editing generated `Makefile`/`*.vcxproj`/`*.sln`.
- `build_files/` is gitignored; `raylib.lua` there is required for Premake generation.
