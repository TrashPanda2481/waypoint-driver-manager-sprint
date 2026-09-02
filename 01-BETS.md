# 01 · The bets

*Day 2, Exercise 1. Ten assumptions this project is standing on, across three layers —
PROJECT (is it possible, does anyone want it, is it interesting), BUILDER (my skills, my time,
what I expect AI to do), MACHINE (my setup, whether I can get at and keep my own code). Each one
phrased as a claim that could turn out false.*

*Then two columns, and the sort that matters: **ranked by cost of being wrong, not by
likelihood.** Something with a one-in-ten chance of ending the project beats something certain
to annoy me for an afternoon.*

| # | Layer | The bet (a claim that could be false) | How sure | Cost if wrong |
|---|---|---|---|---|
| **P2** | PROJECT | The restore-point + `pnputil` export + rollback chain genuinely makes a bad install recoverable on real Windows. | Medium | **Project-ending** — it's the whole pitch |
| **P1** | PROJECT | Techs feel SDI's irreversibility as real pain and would switch for a safer tool (want, not just my taste). | Medium | **Project-ending** — but already has evidence (SDI ticket #108, forums) |
| **M1** | MACHINE | I have admin-rights Windows (real or VM) where restore points and `pnputil` actually work. | High | **Project-ending** — can't test anything without it |
| **B1** | BUILDER | I can get the Windows WMI/SetupAPI backend working correctly on real hardware inside this sprint. | Medium | Days |
| **B3** | BUILDER | The mock/Linux parity backend reflects real Windows closely enough that the port isn't a rewrite. | Low | Days |
| **P3** | PROJECT | OEM catalogs + Windows Update Catalog together cover enough real machines to beat carrying SDI's giant packs. | Medium | Days |
| **M3** | MACHINE | A Windows VM is a valid test surface for driver install/rollback (behaves enough like real hardware). | Low | Days — false negatives or false confidence |
| **M2** | MACHINE | Defender / Bitdefender won't quarantine or block Waypoint's driver operations. | Low | Days — it has flagged repo files before |
| **B2** | BUILDER | I can validate the full install -> backup -> rollback chain without bricking my own test machine. | Medium | An afternoon (that's what the restore point is for) |
| **P4** | PROJECT | Techs actually read the provenance diff-cards (source + SHA-256) and value them at install time. | Low | An afternoon — nice-to-have, not load-bearing |

---

## The circled one

**→ P2** — reversibility actually works on real Windows. It goes to `02-RISK.md` for the test.

**Guarding against the usual dodge.** The handbook's warning is that the dangerous bet is usually
"does anyone want this" (P1), and the comfortable one is technical. Here the technical bet *is*
the dangerous one: P1 already has documented evidence behind it (real people complaining about
exactly SDI's irreversibility) and can't be falsified in twenty minutes, while P2 has no
real-hardware evidence at all and is the reason the want in P1 would translate to a switch. If P2
is false, P1 stops mattering. So P2 is honestly the top, not a comfortable substitute for it.

## Watch list

*The bets I'm not testing today but shouldn't forget — the ones most likely to bite later.*

- **M2 (AV blocks driver ops).** Cheap to hit, annoying to diagnose. The Vector sprint already lost
  time to Bitdefender quarantining repo files; driver installs are exactly the kind of operation an
  AV heuristic dislikes. Worth an exclusion set up front.
- **B3 (parity backend != real Windows).** If the mock backend has quietly diverged from real WMI
  behavior, "works on Linux" has been buying false confidence for weeks. Only real hardware settles it.
