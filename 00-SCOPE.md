# 00 - Scope

*Day 1, Exercise 1. Written before the risky part started, on purpose.*

## The project

**Name:** Waypoint Driver Manager

## Demo-ready

*One paragraph. This is the definition I will be held to - it was written on day one
specifically so that day-six me can't move it.*

It's a driver detection, sourcing, and install manager for Windows built to fix the structural
problems in Snappy Driver Installer, not reskin them. Point it at a machine and it enumerates the
devices, groups them by the same PnP Setup Class taxonomy Windows itself uses, and sorts them into
three tiers: Missing, Problem, and Upgrade available. Every candidate driver is a diff card -
installed version/date/publisher/signature vs. the candidate's, plus exactly which source it came
from and that source's pinned SHA-256. Nothing installs from a summary number. Before any install
batch it takes a verified restore point and exports the currently-bound driver to a versioned
local backup, installs only the INF/CAT payload via pnputil (no OEM helper bloat), and can roll a
single device back with one command regardless of whether System Restore held. The whole engine is
scriptable from a CLI with real exit codes and an append-only JSON-Lines audit log, so it drops
into an RMM/imaging toolchain instead of being double-clicked one tech at a time.

**What they SEE the first time:** `waypoint scan` (or the GUI), devices grouped by Setup Class
into Missing / Problem / Upgrade available, each candidate a diff card - amber on charcoal,
Meridian-family branding.

**What they can DO:** scan a machine and get structured candidates from the local signed cache
and, opt-in, Dell's real per-device catalog; read a dry-run plan that prints the exact
pnputil/DISM commands and the diffs before anything runs; drive the whole thing from the CLI with
`scan` / `plan` / `apply --dry-run` and defined exit codes; read the JSON-Lines audit log of every
scan and action; run the identical engine path from the GUI button or a script, so the two can't
drift.

**What they CANNOT do:** trust a real install or rollback on live Windows hardware yet - that
chain (restore point -> pnputil export -> pnputil add-driver -> rollback) is written but has never
run on a real machine. Use HP per-device matching - HP is scoped to a platform-list lookup on
purpose. Rely on the Lenovo or Dell driver-pack catalogs inside a scan - those are model-keyed and
aren't wired into the HWID search path, only Dell's per-device catalog is. Point it at a fleet and
count on WMI enumeration speed - untested at scale. Install unsigned or test-signed drivers without
an explicit, logged policy override - blocked by default.

---

## What I'm deliberately faking

*Every prototype fakes something. Naming it up front is the difference between a demo and a lie.
Fill this out properly on Day 7 - for now, anything I already know.*

- **"Installs drivers on Windows" is unproven, not working.** The Windows device backend
  (`platform/windows`) and the Windows Update Catalog source are written against documented APIs
  (`Win32_PnPEntity`/`Win32_PnPSignedDriver`, `Microsoft.Update.Session` COM) but have never
  executed on real Windows hardware. On Windows today they run real code paths, just unverified;
  on Linux they run a parity/testing backend that's for engine and GUI development only.
- **The safety chain is coded, never fired.** Verified restore point, `pnputil /export-driver`
  backup, `pnputil /add-driver /install`, one-command rollback - all implemented, none run on a
  live machine end to end. Safety is the entire reason this exists over SDI, and it's the part
  with zero real-hardware evidence behind it.
- **OEM sourcing is real but only partly wired.** All four OEM `refresh()` pipelines (Dell
  per-device, Dell driver-pack, Lenovo, HP) were live-validated once against the real catalogs on
  2026-08-30, but only Dell's per-device `CatalogPC.cab` implements the `DriverSource.search()`
  shape and actually feeds a scan. The model-keyed ones don't, and HP only answers "is this a
  known platform."
- **Lenovo's SHA-256 rests on a length match.** The `crc` field is 64 hex chars so it's almost
  certainly SHA-256, but I never downloaded a ~300MB Lenovo pack to hash it end to end (I did for
  Dell). Honest for now, not proven for Lenovo.
- **The GUI looks live but sits on the unproven backend.** The scan button -> background thread ->
  engine -> tree-view path is real and headless-Qt tested; what's under it on Windows is the part
  that hasn't touched hardware.

---

## Cut check

*The first scope is always too big.*

- **If I only got half of this done, would the half still show the idea?** Yes. The scan, the
  three-tier triage, the provenance diff cards, and the dry-run plan already demonstrate "know
  exactly where a driver came from" without installing anything - the plan prints the exact
  commands and diffs and applies nothing. The provenance-and-safety pitch is demoable as a
  dry-run before a single real install exists.
- **What would it take to cut it in half again?** *(I don't have to. I have to be able to
  answer.)* Drop the OEM catalogs entirely and ship Windows Update Catalog + local cache as the
  only two sources, and drop the GUI - ship CLI-only `scan` / `plan` / `apply --dry-run`. That's
  still recognizably Waypoint.
