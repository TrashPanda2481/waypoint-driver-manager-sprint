# Waypoint Driver Manager - Technology Stack

A driver detection, sourcing, and install manager for Windows. This is a
short map of what the project is built with and why.

*Rewritten 2026-09-10. The previous version of this file described the
Python implementation, which was replaced on 2026-09-04 (see `ADR-0001` in
the product repo). Python is preserved unmaintained on the
`python-reference` branch.*

## Language and runtime
- C# on .NET 8. Libraries and the CLI target `net8.0`; the GUI targets
  `net8.0-windows` because WPF needs it.
- The CLI publishes with **Native AOT**: `waypoint.exe` is a single
  ahead-of-time compiled 7.9MB native binary, Authenticode signed, with no
  runtime to install and zero trim warnings. That is the whole reason for
  leaving Python - PyInstaller builds get flagged by Defender and
  SmartScreen, and a driver tool that antivirus quarantines is not a tool.
- `InvariantGlobalization` on the CLI, nullable reference types and implicit
  usings everywhere.

## Shape of the codebase
- **Zero third-party runtime dependencies.** Not one `PackageReference`
  across Core, Sources, Engine, Platform, Cli or Gui. Test projects pull
  xUnit; nothing that ships pulls anything.
- Layered, same as before the rewrite: `Core` (pure logic, no I/O) /
  `Sources` (pluggable driver sources) / `Engine` (scan -> plan -> apply
  orchestration + audit) / `Platform` (OS backend behind one interface) /
  `Cli` / `Gui`.
- The contracts - `IDriverSource`, `IModelDriverPackSource`,
  `IDeviceBackend` - live in `Core`, so `Engine` depends only on interfaces
  and never on a concrete source or backend.
- One `EngineFactory` builds the engine for both the CLI and the GUI, so the
  two cannot drift.

## GUI
- WPF. Tier -> PnP Setup Class -> device tree, with a diff card comparing the
  installed driver against the candidate field by field.
- Scans run on a thread-pool thread through `Task.Run` and `await`, so the
  window never blocks on enumeration or a first-run catalog download.
- **Not AOT**, and it will not be: WPF has no Native AOT story and its
  binding stack is reflection-based. So the GUI ships two ways, with .NET
  bundled (about 67MB as an MSI) or requiring the .NET 8 Desktop Runtime
  (about 11MB). `waypoint.exe` is identical in both and needs neither.

## Windows device and install layer
Direct P/Invoke against three libraries. No WMI, no COM.
- **CfgMgr32** (`cfgmgr32.dll`) for device enumeration:
  `CM_Get_Device_ID_ListW`, `CM_Locate_DevNodeW`,
  `CM_Get_DevNode_Registry_PropertyW`, `CM_Get_DevNode_PropertyW`,
  `CM_Get_DevNode_Status`. Chosen over `System.Management`, which is neither
  trim- nor AOT-safe; a bulk `Win32_PnPSignedDriver` query also costs 2.4
  seconds where this does the whole tree in about 400ms.
  Where a `CM_DRP_*` integer constant and a DEVPKEY GUID both exist, the
  integer wins: a mistyped GUID fails silently as "no value", a wrong
  integer does not.
- **kernel32** `GetSystemFirmwareTable('RSMB')` for SMBIOS, parsed by hand
  for Type 1 (system) and Type 2 (baseboard), to answer "what model is this
  machine" for the model-keyed vendor catalogs.
- **srclient** `SRSetRestorePoint` for the mandatory restore point before any
  install batch.
- **pnputil** for the rest: `/enum-drivers` to read signer names,
  `/export-driver` to back up the currently bound driver, `/add-driver
  /install` to install. No OEM helper executables.

## What is deliberately not here
- **The Windows Update Catalog source.** `PublishAot` sets
  `BuiltInComInterop.IsSupported=false`, even under `dotnet run`, so
  `[ComImport]` activation throws in the configuration Waypoint actually
  ships. It is excluded from the default source list rather than shipped
  broken. Reaching Windows Update needs a `ComWrappers` /
  `[GeneratedComInterface]` rewrite.
- **A Linux backend.** The Python tree had one for parity and testing. This
  is Windows-only by decision (`ADR-0001`); `BuildDefaultBackend()` throws
  rather than pretending.

## Driver sourcing (OEM catalogs) - standard library only
- `HttpClient` for streaming downloads.
- `System.Xml.Linq` with local-name matching, so the same parser handles
  Dell's namespaced `DriverPackCatalog.xml` and its namespace-free
  `CatalogPC.xml`.
- `System.Security.Cryptography` for MD5 and SHA-256. Dell publishes only
  MD5 per component, so a search returns no SHA-256 and a fetch verifies the
  MD5 then computes a real SHA-256 over the verified bytes.
- CAB extraction shells out to `expand.exe`, and has to work around it: it
  refuses to "expand a file onto itself" yet exits 0, and for a single-member
  cab it ignores `-F:` and names the output after the CAB. So extraction
  stages into a private directory and trusts neither the exit code nor the
  output name. The Python had the same bug and never hit it, because its
  live validation ran on Linux with `cabextract`.

## CLI, engine, audit
- Hand-rolled argument parsing. The surface is four verbs and a handful of
  flags, and a parsing dependency would have to earn its place in an AOT
  binary that currently has none.
- `System.Text.Json` with **source generators**, not reflection, so JSON
  survives trimming and AOT.
- Exit codes are contract: 0 clean, 1 action needed, 2 error. Failures print
  a message, never a stack trace, so scripts have something to branch on.
- Append-only JSON Lines audit log of every scan, plan and action.

## Build, test, tooling
- xUnit. 134 tests across five test projects, against fixtures trimmed from
  real vendor catalogs, so CI never depends on Dell's servers being up.
- **WiX 5** for the MSI, deliberately not 6, which requires accepting the
  Open Source Maintenance Fee EULA - a licensing decision, not a technical
  one.
- Authenticode signing via `signtool`, SHA-256 with RFC3161 timestamping,
  certificate referenced by thumbprint from `CurrentUser\My` so no key
  material is in the repo. Signing hooks AOT's `CopyNativeBinary` step and
  not `Publish`, because that target replaces the apphost afterwards and a
  signature applied on `Publish` is silently thrown away.
- Native AOT publish needs the MSVC C++ toolchain and `vswhere.exe` on PATH.

## Version control and hosting
- Git + GitHub. Product repo: github.com/TrashPanda2481/Waypoint-Driver-Manager (main).
- This sprint repo's `index.html` is plain HTML with inline CSS, no
  framework and no build step, served on GitHub Pages.

## Status notes
- Device enumeration is **validated on real hardware**: 233 devices, an exact
  set match against `Get-PnpDevice` with zero missing and zero extra, and
  signature detection agreeing with WMI's `IsSigned` on 233 of 233 without
  using WMI.
- The **install and rollback chain has never run on a live machine.**
  Restore point, driver export, `pnputil` install, rollback - all written,
  all tested against a mock, none fired for real. This is the riskiest part
  of the project and it is the part with no evidence.
- The signing certificate is **self-signed**. Antivirus and SmartScreen
  reputation, the reason for leaving Python, stays unmeasured until a real
  certificate signs a build.
