# 03 - The path

*Day 3, Exercise 1. The walking skeleton: the whole path, start to end, in its ugliest honest
form. A way in, a thing that happens, a way out. The handbook says draw this one by hand - it's
the one nobody else can write for me - so this is a starting point to put in my own words, not
the last word.*

**The fewest steps it can be and still be the path. Over seven and I've written the product, not
the skeleton.**

| # | Step | State |
|---|---|---|
| 1 | **Way in** - a tech opens a terminal on a Windows machine and runs `waypoint scan`. | HAVE (code) / GAP (never run on real Windows) |
| 2 | Devices print grouped by PnP Setup Class into Missing / Problem / Upgrade available. | HAVE (mock + Linux parity) / GAP (real WMI on hardware) |
| 3 | `waypoint plan` prints each candidate as a diff card: installed vs candidate (version, date, publisher, signature) + source + SHA-256. | HAVE |
| 4 | `waypoint apply --dry-run` prints the exact `pnputil` / DISM commands and the diff, and applies nothing. | HAVE |
| 5 | `waypoint apply` creates a verified restore point, `pnputil /export-driver` backs up the current driver, installs one driver. | GAP (never run on hardware) |
| 6 | **Way out** - one result line, written to the append-only audit log: "1 installed; restore point RP123; backup at ...; roll back with `waypoint rollback <device>`." | GAP (depends on 5) |

**The ending.** Step 6 is the way out - the audit-log summary plus a named rollback command. That
line is what turns a pile of subcommands into a path: the tech knows it worked, knows where the
backup is, and knows the one command that undoes it.

---

## What goes in each gap today

*The move is not to build the hard step - it's to put its dumbest honest fake in the path so the
path connects now, and the real thing becomes an upgrade instead of a hole. Keep the list; Day 7
is about exactly this.*

| Gap | Layer | Cheapest fake that connects the path today |
|---|---|---|
| Steps 1-2 real enumeration | MACHINE / PROJECT | Run on the **mock backend** (already exists) - canned devices, so scan -> plan -> apply runs end to end on any OS, no Windows needed. |
| Step 5 real install | PROJECT | Mock backend's `apply` performs a no-op "install" and still writes the audit line, so the way-out in step 6 genuinely exists today. |

**Faked, and written down as faked:** real device enumeration, the real restore point, the real
`pnputil` install and rollback. **Real today:** the matching, the plan, the diff cards, the
dry-run command generation, the audit log, and the CLI path itself.

*Not applicable:* the browser / GitHub Pages / `index.html` lane. Waypoint isn't a web page - the
walkable artifact is the CLI on the mock backend (or a recorded terminal session for a remote
walker).

---

## Somebody else walks it

*Exercise 3. I can't walk my own path honestly - I know which button is dead. Hand it to someone
who isn't me, say nothing, and watch where they stop.*

**Setup:** give a tech (or a cohort-mate - a poor substitute for a stranger, an excellent
substitute for me) the CLI on the mock backend and one instruction: "run `waypoint scan`, then
`plan`, then `apply --dry-run`, then `apply`, and tell me where you got confused or stopped."
Then stay quiet.

[ ] FILL AFTER THE WALK: where they stopped and what they were looking at; anything they had to
guess; what they thought a step did versus what it did. Then fix the first thing they stopped on
- not the next feature.
