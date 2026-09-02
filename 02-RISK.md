# 02 · The riskiest part

*Day 1. Today's checkpoint is that this is written down and I've started it.*

## What's most likely to not work?

**→** The safe install/backup/rollback chain on real Windows hardware. Restore point →
`pnputil /export-driver` backup → `pnputil /add-driver /install` → one-command rollback is all
written and none of it has ever run on a real machine. The device backend that feeds it
(`platform/windows`, WMI enumeration) has also never touched real Windows.

Be specific. Not *"the whole thing"* — the one piece where, if it turns out to be impossible or
much harder than I think, everything else stops mattering.

**Why is it the riskiest?**

Because reversibility is the entire pitch. Waypoint's reason to exist over SDI is SDI's ticket
#108 — "not even System Restore could undo it." If `CreateRestorePoint` silently fails, or the
`pnputil /export-driver` backup doesn't faithfully restore the prior driver, or rollback leaves a
device worse than it started, then I've rebuilt SDI with a nicer UI and a false promise, which is
worse than SDI. Everything upstream — the matching, the OEM catalogs, the diff cards — is already
tested and doesn't matter if the machine can't be put back the way it was. And it's the one whole
subsystem with zero real-hardware evidence behind it.

---

## Tomorrow

**A checkpoint is a thing that either exists or doesn't** at the end of a day. Not *"work on the
Windows backend"* — **"scan lists real devices on a real Windows box."**

A percentage-complete estimate is a wish. It sits at 80% until you run out of days. Something
binary is the only kind that tells you you're behind while there's still time to do anything
about it.

**Tomorrow, this will exist:**

▢ `waypoint scan` runs on a real Windows machine (or a Windows VM) and lists actual devices
grouped by PnP Setup Class, using `platform/windows` (WMI) — not the mock or Linux parity backend.

*One thing. Small enough that I'd be embarrassed to miss it.* It's the first link that has to
hold before any of the risky chain above is even reachable: restore point (day 3), real
`export-driver` backup (day 4), a real install (day 5), a real rollback (day 6).
