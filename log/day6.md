# Day 6

*2026-09-09. The day it touched hardware. What I made, what broke, what I decided.*

**Made:** the Windows device backend, and then everything that became possible once it existed.
Enumeration reads the device tree through CfgMgr32 P/Invoke rather than WMI, because
`System.Management` is neither trim- nor AOT-safe and a bulk `Win32_PnPSignedDriver` query costs
2.4 seconds where CfgMgr32 does the whole tree in about 400ms. Then the CLI against real hardware,
`waypoint driverpack` for the model-keyed vendor catalogs that had been implemented and
unreachable since day 1, the WPF GUI on the shared engine, `docs/INSTALL.md`, two installer
variants, and a tagged release with four downloadable artifacts. Deleted the Python implementation
from `main` and moved it to a `python-reference` branch. Twelve commits.

**Broke / found:** the good kind of finding, which is the kind that only happens on real hardware.
233 devices enumerated, an exact set match against Windows' own `Get-PnpDevice` - zero missing,
zero extra - and signature detection agreed with WMI's `IsSigned` on 233 of 233. **103 of those
233 devices share a hardware ID with another device**, including sixteen CPU cores presenting as
one ID. That is the SDI failure mode this project exists to catch, and it is not a rare edge case
on a normal desktop; it is 44% of the machine. Also found: inbox INFs never appear in `pnputil
/enum-drivers`, so absence there is not evidence a driver is unsigned - treating it as such would
have falsely flagged 182 of 233 devices. `PublishAot` disables built-in COM even under `dotnet
run`, which means the Windows Update Catalog source ported on day 4 cannot run in the
configuration this actually ships in. Dell's catalog listed every OptiPlex 5070 pack twice because
one release covers the model under two system IDs, and a test I wrote asserted the duplicate as
correct behaviour. And the signing target matched only `OutputType == Exe`, so the GUI - a
`WinExe` - would have shipped unsigned while the build reported success.

**Decided:** CfgMgr32 over WMI permanently, and accept that Windows Update sourcing needs a
`ComWrappers` rewrite before it can come back. Never report WHQL, because WHQL and attestation
share a signer name and telling them apart needs catalog inspection - a signed driver reports the
weaker claim rather than asserting trust it has not verified. Ship the GUI in two installer
variants, one carrying .NET and one requiring it, because WPF has no Native AOT story and the
machine that most needs a driver tool is the one whose network adapter has no driver and cannot go
fetch a runtime.

**Checkpoint - hit or missed, and why:** the biggest hit of the sprint. Steps 1 and 2 of the
walking skeleton went from GAP to proven on real hardware, the GUI stopped being a promise, and
there is a signed installer a stranger can download. And P2 is *still* unproven. `apply --apply`
has never run against a live driver, the restore point has never been created, and the rollback
has never been fired. Four sessions in, the riskiest bet - the one day 2 said the entire margin
over an SDI reskin rests on - has exactly as much real-hardware evidence behind it as it had on
day 2, which is none. Everything around it got real today. It did not.
