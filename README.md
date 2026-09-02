# Waypoint Driver Manager — Make and Do sprint

**This is the sprint record, not the product.** The actual code lives in its own repo
(**[TrashPanda2481/Waypoint-Driver-Manager](https://github.com/TrashPanda2481/Waypoint-Driver-Manager)**).
This folder is the fortnight-shaped account of getting it from "passes its own tests" to
"safely installs and rolls back a driver on a real Windows machine" — one scope, one map, one
riskiest part, and a paragraph a day.

---

## What's in here

```
00-SCOPE.md         the one thing + the demo-ready definition I'll be held to
01-MAP.md           the four stages + the one genesis component
02-RISK.md          the riskiest part + tomorrow's binary checkpoint
log/dayN.md         one paragraph a day
scratch/            mine. gitignored. never leaves this machine.
```

The five markdown files are the sprint record. The work itself is the separate Waypoint repo —
this folder just tracks the two weeks honestly.

---

## `scratch/` is not a mistake

It's in `.gitignore`, which means git ignores it completely and nothing in it is ever committed
or pushed. Put the things in there that shouldn't be public: a client's real machine's HWIDs, a
driver-pack URL I haven't decided whether to trust, the ugly first draft, a to-do list that's
really a diary.

**Knowing which parts of a project are public and which aren't is most of what a `.gitignore`
is for.** Worth keeping the habit.

---

## History

This repo started life as the Vector Toolset sprint and was overwritten and renamed to track
Waypoint instead — Vector's fortnight had already landed, Waypoint's riskiest part hadn't. The
old day-1 Vector entry stays in the git history; the working tree is Waypoint from here.

---

## Committing as you go

Once a day is plenty for a fortnight:

```bash
git add -A
git commit -m "day 2 — restore point actually created on real hardware"
git push
```

Eight commits across eight days is a real record of how the thing was built, and it's worth more
than it looks. You'll want it when someone asks what this actually took.
