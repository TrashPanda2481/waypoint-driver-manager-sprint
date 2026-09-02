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

---

# Day 2 · Kill the riskiest assumption

*Day 2, Exercises 2 and 3. The riskiest part above was named on Day 1; today it gets ranked
against every other bet and actually probed. Full bet list, with a "how sure" and a "cost of
being wrong" column, is in `01-BETS.md` — sorted by cost, not by likelihood.*

## The bet, circled

**→** [P2] The safe install/backup/rollback chain actually leaves a device recoverable on real
Windows. A restore point is created and verified, `pnputil /export-driver` produces a
re-installable backup, and re-adding it puts the device back the way it was.

**Why this one, and not a comfortable one.** The comfortable bet is "my HWID matching picks the
right driver" — technical, specific, and already covered by 36 passing tests, so being wrong
about it costs an afternoon. The other project-ender is P1, "anyone actually wants a safer SDI
enough to switch" — but that one already has evidence (the Architecture doc cites real SDI
ticket #108 / forum / Reddit complaints about exactly this pain), and it isn't testable in
twenty minutes. P2 has *zero* real-hardware evidence, it's the entire reason to exist over SDI,
and it's cheaply testable this week. So P2 is the one to probe today. Cost of being wrong: the
whole project — I'd have shipped SDI's ticket #108 with a nicer UI, which is worse than SDI.

## The smallest test

*Under 20 minutes, and not a line of code. The honest test for an idea this size is a thought
experiment: hold it up against reality, strip the project to the one part that makes it itself,
and ask whether reality already answers the question — and whether the idea still stands if the
scariest bet goes the wrong way.*

Stripped to its genesis, the question is one line: **is this more than a reskin of something
techs already have for free, and does its value survive resting on the one chain that isn't
proven yet?**

## What actually happened

*Exercise 3. Ran the idea against reality, honestly, and against the thing it replaced.*

**Vector Toolset failed this test.** Held against reality its genesis is thin — a branded assembly
of tools that already exist for free (Hiren's BootCD PE, MediCat, Ventoy), with the one genuinely
hard piece of value, driver injection, punted to SDI/Waypoint. Reality already answers "why not
just use the free one," so the sprint's energy moved to the idea that survives. That is why this
repo is Waypoint's now and not Vector's.

**Waypoint passes — barely.** The safety-and-provenance answer to SDI's *documented* structural
failures (ticket #108 irreversibility, wrong-driver installs, opaque sourcing) is a real gap that
nothing techs actually use has closed, so reality does not already answer it. It passes only
barely because the single thing that makes it more than an SDI reskin — reversible, verified,
rollback-able installs — is the one subsystem with zero real-hardware evidence. The margin between
"real product" and "SDI with a nicer UI" is exactly that one unproven chain thick. The idea
stands; it is standing on one leg until P2 is proven on hardware.
