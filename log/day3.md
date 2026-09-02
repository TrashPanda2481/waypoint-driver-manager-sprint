# Day 3

*One paragraph. The faked list is the one that matters - I'll want it on Day 7.*

**WHAT CONNECTS:** on the mock backend the path runs start to end - `scan` -> `plan` ->
`apply --dry-run` -> `apply` -> audit-log summary line. Confirm by running it before handing it
to anyone: [ ] ran `waypoint scan` / `plan` / `apply` on the mock backend and reached the summary
line.

**WHAT I FAKED:** the mock backend standing in for real Windows device enumeration; the real
restore point; the real `pnputil` install and rollback (mock `apply` is a no-op that still writes
the audit line, so the way-out exists). Real: matching, plan, diff cards, dry-run command output,
audit log.

**WHAT BROKE:** [ ] FILL AFTER SOMEONE WHO ISN'T ME WALKS IT - the first place the path stopped for
them. Not that I can reach the end; I always can. That someone else did, without me sitting next
to them.
