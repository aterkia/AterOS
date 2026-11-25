# Repository Guidelines

## Project Structure & Module Organization

AterOS is a C++17 ncurses TUI for two camera streams, MAVProxy, and Ethernet target discovery.

- `src/config.*`: configuration defaults, validation, JSON persistence, and legacy fallback.
- `src/process.*` and `src/controller.*`: child-process lifecycle, captured output, GStreamer pipelines, and MAVProxy commands.
- `src/network.*`: physical Ethernet classification, target probing, and scanning.
- `src/app.*`, `src/app_view.cpp`, and `src/main.cpp`: application state, keyboard handling, rendering, and startup.
- `tests/`: C++ core checks and Python pseudo-terminal startup scenarios.
- `scripts/build-release.sh`: Debian 12 builds for x86-64 and ARM64.

Keep `ateros_core` independent of ncurses. The Unicode wordmark lives in `src/app_view.cpp`; there is no separate assets directory. Generated `build/` and `release-output/` directories are ignored.

## Build, Test, and Development Commands

```sh
cmake -S . -B build                         # Configure the project
cmake --build build --parallel 2            # Compile executable and tests
ctest --test-dir build --output-on-failure  # Run registered tests
./build/ateros                             # Launch the TUI
```

Install a C++17 compiler, CMake, ncurses development headers, and Python 3 for development. Runtime streaming also requires GStreamer and MAVProxy. Run release builds inside Debian 12 using the Docker command in `README.md`; ARM64 core tests use QEMU.

## Coding Style & Naming Conventions

Match surrounding code: two-space C++ indentation, four-space Python indentation, and lowercase filenames with underscores. Use namespace `ateros`, PascalCase classes, camelCase functions, and trailing underscores for private members. Keep implementation-only helpers in anonymous namespaces. Compiler checks include `-Wall -Wextra -Wpedantic`; no formatter or standalone linter is configured. Avoid unrelated formatting changes.

## Testing Guidelines

CTest runs assert-based `core_tests` and, when Python is available, `startup_scan_tests`. Extend the existing tests for affected behavior; no numeric coverage threshold is configured. Keep tests isolated from user configuration, real devices, and live network scans. Successful-scan PTY scenarios require physical Ethernet metadata and otherwise skip. Document hardware checks separately.

## Commit & Pull Request Guidelines

Follow existing imperative commit subjects: `feat: configure MAVProxy path`, `fix: stop overriding MAVProxy stream rate`, or `test: ...`. Keep changes focused. PRs should explain behavior changes, link relevant issues, report test results and skips, and include terminal captures for visual changes. Update documentation when commands, configuration, or packaging change.

## Configuration & Runtime Safety

Use `~/.ateros.json`; `~/.rov_control.json` is a fallback only when the new file is absent. Preserve migration compatibility and never commit personal configuration. Launching AterOS can automatically probe Ethernet and start streams, so use the simulated tests for routine verification.
