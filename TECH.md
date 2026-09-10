# Waypoint Driver Manager - Technology Stack

A driver detection, sourcing, and install manager for Windows, with a
cross-platform core. This is a short map of what the project is built with
and why.

## Language and runtime
- Python. `pyproject.toml` sets `requires-python >=3.10`; the architecture
  spec targets 3.12+. (Reconcile these before release.)
- Modern-Python idioms: `dataclasses`, `enum`, `typing.Protocol` for
  interfaces, `from __future__ import annotations`.

## Shape of the codebase
- Zero third-party dependencies in the core (`dependencies = []`). All OS-
  and GUI-specific code lives behind optional extras, so the pure
  matching/engine logic installs and tests on any OS.
- Layered: `core` (pure logic, no I/O) / `sources` (pluggable driver
  sources) / `engine` (scan -> plan -> apply orchestration + audit) /
  `platform` (OS backends behind one interface) / `cli` / `gui`.

## GUI
- PySide6 (Qt for Python). Background scans run on a `QThread` worker so the
  window never blocks.

## Windows device and install layer
Written against documented Windows tooling; not yet validated on real
hardware.
- pywin32 + wmi: device enumeration via `Win32_PnPEntity` and
  `Win32_PnPSignedDriver`.
- `Microsoft.Update.Session` COM (via `win32com.client`): the Windows Update
  Catalog source.
- pnputil (`/add-driver`, `/export-driver`): install and per-driver backup.
  DISM for offline-image scenarios.
- WMI `SystemRestore`: mandatory restore point before any install batch.

## Linux layer (parity/testing only)
- pyudev, with `lspci`/`lsusb` as a documented fallback. Exists to keep
  core/engine cross-platform and testable, not as a full driver-management
  target.

## Driver sourcing (OEM catalogs) - standard library only
- `urllib.request` for streaming downloads (no `requests` dependency).
- `xml.etree.ElementTree` for catalog parsing (Dell, Lenovo, HP feeds).
- `hashlib` for MD5/SHA-256 integrity checks.
- CAB extraction by shelling out to `expand.exe` on Windows or `cabextract`
  on Linux/macOS.

## CLI, engine, audit
- `argparse` CLI with JSON in/out and defined exit codes (0 clean, 1 action
  needed, 2 error). Console scripts: `waypoint` and `waypoint-gui`.
- Append-only JSON Lines audit log (`json` + `datetime`) of every scan,
  plan, and action.

## Dependencies, by extra
- core: none
- windows: pywin32, wmi
- linux: pyudev
- gui: PySide6
- dev: pytest, ruff

## Build, test, tooling
- pytest for tests, run headless with `QT_QPA_PLATFORM=offscreen` for the Qt
  path; fixtures are trimmed real vendor-catalog data, so CI needs no
  network.
- ruff for lint/format (line length 110, target py310).
- setuptools + wheel build backend, `src/` layout, pip-installable.
- PyInstaller one-file build is the planned path for a portable Windows
  binary (not set up yet).

## Version control and hosting
- Git + GitHub. Product repo: github.com/TrashPanda2481/Waypoint-Driver-Manager (main).
- The sprint/class walking-skeleton page (`index.html`) is plain HTML +
  minimal inline CSS + vanilla JavaScript, no frameworks or build step,
  served on GitHub Pages.

## Status notes
- The Windows device backend and Windows Update source are implemented but
  not yet run on real hardware - the stated next milestone.
- CLI `apply` is currently a stub; the engine apply path has no fetch/verify
  wiring yet.
