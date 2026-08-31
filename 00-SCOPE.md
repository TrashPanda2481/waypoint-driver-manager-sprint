# 00 · Scope

*Day 1, Exercise 1. Written before you started, on purpose.*

## The project

**Name:** Vector Toolset

## Demo-ready

*One paragraph. This is the definition you will be held to — it was written on day one
specifically so that day-six you can't move it.*

It's a branded, self-contained WinPE stick for doing IT repair work. Boot a dead machine off it
and you get my own GRUB2 menu: WinPE with my launcher running as the shell, or a handful of
Linux repair tools (Clonezilla, ShredOS, Memtest86+, GParted, ddrescue, chntpw) if that's what
the job needs instead. Plug the same stick into a machine that's already running and the same
tools folder just shows up in Explorer — launcher and all, if it's Windows. The launcher reads a
manifest to build its menu, shows what's actually on the stick vs. missing, knows whether it's
running in WinPE or on a live box, and has a Manage Tools screen so I can add or pull tools
without touching code.

**What they SEE the first time:** my GRUB menu, then the launcher taking over the whole
screen — orange on charcoal, tool cards sorted into categories.

**What they can DO:** pick WinPE or one of the Linux repair tools off the boot menu; browse the
~41 tools I've already auto-fetched; run a built-in script (SMART check, full diagnostics,
stress test, DISM imaging, offline driver injection); reboot or shut down from the launcher; add
or edit tools through Manage Tools; plug the stick into a live Windows box and get the same
tools and the same launcher.

**What they CANNOT do:** boot on an old BIOS-only machine yet — I've only got the UEFI GRUB core
built. Use fan control or the AV scanners from inside WinPE — those need a real running Windows,
so they're hidden there on purpose. Count on every tool being there — about 25 of them are
EULA-gated and still just a manual download checklist. Drag some random ISO onto the stick and
have it boot — this isn't Ventoy, the boot menu only knows about the payload I built in.

---

## What I'm deliberately faking

*Every prototype fakes something. Naming it up front is the difference between a demo and a
lie. Fill this out properly on Day 7 — for now, anything you already know.*

- The "curated tool list" is really just whatever auto-fetches without a fight. The ~25 EULA
  tools are a checklist right now, not actually sitting on most sticks I build.
- No BIOS/legacy boot support at all — every test so far, hardware or VM, has been UEFI.
- "Offline driver injection" sounds solved but isn't really — Vector itself still just leans on
  DISM /Add-Driver and SDI as a last resort. Waypoint Driver Manager, the real fix, is actually
  pretty far along on its own (device matching, engine, and live-validated Dell/Lenovo/HP catalog
  sourcing all pass their tests) but it isn't wired into Vector yet, and its own Windows backend
  hasn't been run on real hardware either.

---

## Cut check

*The first scope is always too big.*

- **If I only got half of this done, would the half still show the idea?** Yeah — WinPE plus
  the launcher plus the tools I've already got already boots and runs on real hardware. That's a
  demoable stick on its own, even without the Linux boot entries or the last 25 tools.
- **What would it take to cut it in half again?** *(You don't have to. You have to be able
  to answer.)* Drop the Linux boot entries entirely and ship WinPE-only, and skip Manage Tools —
  just hand-edit the manifest JSON when I need to.
