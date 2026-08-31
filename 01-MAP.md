# 01 · The map

*Day 1, Exercise 2. Wardley, cut down to the one question that matters this fortnight.*

**The rule: build only what's in Genesis. Buy, borrow or fake everything else.**

| Component | Stage | If Product/Commodity — what I'll use instead |
|---|---|---|
| The launcher itself (knows WinPE vs. live, reads the manifest, Manage Tools GUI) | Genesis | — |
| My branded GRUB2 multiboot (BIOS + UEFI, own theme) | Custom | — |
| WinPE build pipeline (copype / DISM / optional components) | Custom | — |
| Tool resolver (pulls from GitHub releases, NirSoft, Scoop) | Custom | — |
| USB deploy script (partitioning, splitting what's baked in vs. on the data drive) | Custom | — |
| Hardware diagnostics & sensors | Commodity | HWiNFO, GPU-Z, CrystalDiskInfo/Mark, Sysinternals |
| Data recovery | Commodity | TestDisk/PhotoRec, Recuva, DMDE Free |
| Cloning / imaging | Product/Commodity | Clonezilla + ddrescue, not writing my own cloner |
| Remote support | Product | RustDesk, AnyDesk as backup |
| Malware scanning | Commodity | KVRT, Emsisoft Emergency Kit, AdwCleaner, rkill |

---

## The one genesis component

*The one where, if you only shipped that, it would still be recognisably the idea.*

**→** The launcher — specifically the part that knows whether it's in WinPE or on a live box and
changes what it shows accordingly.

**Why couldn't somebody else have built this?**

*If you can't answer that in a sentence, it's Custom, and it's not where your fortnight goes.*

Because it's built around every call I already made — my manifest format, which tools I hide in
WinPE and why, how Manage Tools works. Nobody else's launcher is shaped like mine.

---

## Retired

*Things I was going to build and won't. One line each — what it was, what I'm using instead.
Every one of these buys back build hours.*

- The old HTA launcher — it kept crashing silently on startup and I never ran that down, so I
  just rewrote it in WPF instead.
- CPU-Z, HWMonitor, Speccy — cut, HWiNFO already covers all of it.
- CCleaner, TeamViewer QuickSupport, Chrome Remote Desktop — not going near these (trust/adware
  concerns), using RustDesk/AnyDesk and BleachBit instead.
- A Ventoy-style "drop any ISO on it and it boots" layer — skipping it. I already know the whole
  payload, so a fixed GRUB menu is enough.
