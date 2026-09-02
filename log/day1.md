# Day 1

*One paragraph. Five minutes. What I made, what broke, what I decided.*

**Made:** the whole cross-platform core (device/candidate matching, no I/O, fully unit-tested),
the engine orchestration (scan → plan → backup → install → verify → rollback with an append-only
JSON-Lines audit log), the content-addressed local cache source, the CLI (`scan`/`plan`/`apply`,
JSON in/out, real exit codes, dry-run), and the PySide6 GUI wired to the engine (scan button →
background QThread → engine → tree view, headless-Qt tested), all sharing one
`build_default_engine()` factory so the GUI and CLI can't drift. Then four OEM catalog sources —
Dell per-device (`CatalogPC.cab`, a real `DriverSource`), Dell driver-pack and Lenovo (model-keyed
via a new `ModelDriverPackSource` protocol), and HP (platform-list lookup only). 36 tests passing,
and all four OEM `refresh()` pipelines live-validated once against the real vendor catalogs.

**Broke / found:** HP's real per-update feed (`HpCatalogForSms.latest.cab`) turned out to be a
WSUS SDP with applicability as arbitrary WQL, not a flat HWID/model list — so per-device HP
matching got scoped out honestly instead of faked. Lenovo's `crc` field is actually a SHA-256, not
a CRC32, so it's reported as `sha256` rather than trusted at face value. Dell publishes only MD5
per component, so `search()` honestly returns `sha256=""` and `fetch()` verifies the MD5 then
computes a real SHA-256 of the verified bytes. And the Lenovo pack's SHA-256 claim rests on a
64-char length match, not a downloaded-and-hashed file (the pack was ~300MB) — flagged as unproven
for Lenovo specifically, unlike Dell where the real 10.37MB file was downloaded and hashed.

**Decided:** on-demand pluggable sourcing instead of a monolithic 20–60 GB offline archive; a
separate `ModelDriverPackSource` protocol for the model-keyed catalogs rather than stretching
`DriverSource` into a shape it wasn't built for; OEM sourcing opt-in (not on every scan) because
Dell's `CatalogPC.xml` alone is ~57MB; and CI runs against small real-data fixtures trimmed from
the live catalogs, not the live network, so it never depends on Dell's/Lenovo's/HP's servers.

**Checkpoint — hit or missed, and why:** Hit for what day 1 was actually about — the engine,
sources, and GUI wiring all pass, and every OEM pipeline proved out against live data. But honest:
nothing has touched real Windows hardware. The device backend and the entire safe install/rollback
chain are written and unrun, which is exactly what day 2 onward targets.
