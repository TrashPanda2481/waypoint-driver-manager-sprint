# 01 - The map

*Day 1, Exercise 2. Wardley, cut down to the one question that matters this fortnight.*

**The rule: build only what's in Genesis. Buy, borrow or fake everything else.**

| Component | Stage | If Product/Commodity - what I'll use instead |
|---|---|---|
| The safety + provenance engine (scan -> plan -> backup -> install -> verify -> rollback, hash-pinned candidates, signature-policy gate, append-only audit log) | Genesis | - |
| The pluggable `DriverSource` interface + structured versioned candidate metadata | Custom | - |
| OEM catalog adapters (Dell per-device + driver-pack, Lenovo, HP platform-list parsing) | Custom | - |
| Three-tier triage UI (Missing / Problem / Upgrade) with diff cards | Custom | - |
| CLI ↔ GUI shared-engine wiring (one factory, no drift) | Custom | - |
| Device install / backup / enumerate primitives | Commodity | `pnputil /add-driver /export-driver /enum-drivers`; DISM only for offline images - not writing a driver installer |
| Restore points | Commodity | Windows System Restore (`SystemRestore.CreateRestorePoint`), not a homegrown snapshotter |
| Signature verification | Commodity | Windows cert store / `WinVerifyTrust`, not a hand-rolled checker |
| Driver metadata feeds | Product/Commodity | Windows Update Catalog COM, Dell `CatalogPC.cab`, Lenovo `catalogv2.xml`, HP `platformList.cab` - consume vendors' feeds, don't build a database |
| Device HWID taxonomy | Commodity | PnP Setup Class GUIDs (Windows' own), not an invented grouping |
| Cab extraction | Commodity | `cabextract` |
| GUI toolkit | Commodity | PySide6/Qt (already the Compass GUI toolkit) |
| Binary packaging | Commodity | PyInstaller one-file, not a custom bundler |

---

## The one genesis component

*The one where, if I only shipped that, it would still be recognisably the idea.*

**->** The safety + provenance engine - specifically the part that makes an install reversible:
verified restore point, then `pnputil /export-driver` of the current driver to a versioned backup,
then install, with a one-command rollback scoped to the single device, and every candidate pinned
to a known source and SHA-256 the whole way through.

**Why couldn't somebody else have built this?**

*If I can't answer that in a sentence, it's Custom, and it's not where the fortnight goes.*

Because it's the exact inversion of SDI's ticket #108 failure - "not even System Restore could
undo it" - built as a first-class guarantee instead of an afterthought: nothing installs that
isn't backed up and rollback-able first, and nothing installs whose origin and hash I can't name.
That "know where it came from, and how to get back" shape is the product; a driver installer
without it is just SDI again.

---

## Retired

*Things I was going to build and won't. One line each - what it was, what I'm using instead.
Every one of these buys back build hours.*

- The monolithic offline driver mega-archive (the SDI / DriverPack Solution model, 20-60 GB over
  BitTorrent) - cut entirely. On-demand pluggable sourcing plus an opt-in content-addressed local
  cache instead. Buys back the whole "host and maintain 60 GB of packs" problem.
- P2P / torrent distribution - not going near it; it's the exact thing that trips enterprise AUP
  and got SDI banned on managed networks.
- An HP per-device applicability evaluator - cut this pass. HP's real feed
  (`HpCatalogForSms.latest.cab`) turned out to be WSUS SDP with applicability as arbitrary WQL, and
  faking per-device matching on top of that partially would be dishonest, so HP stays a
  platform-list lookup only.
- Bundled vendor "helper" / updater software - deliberately not installing OEM toolbars and
  updater bloat; INF/CAT/driver payload via `pnputil` only.
- Trusting vendor field names at face value - retired after finding Lenovo's `crc` is actually a
  SHA-256 and Dell publishes only MD5 per component; the code reports what the bytes actually are,
  not what the label says.
- Writing my own restore-point / signature-verification mechanisms - retired in favour of the OS
  primitives (System Restore, the Windows certificate store).
