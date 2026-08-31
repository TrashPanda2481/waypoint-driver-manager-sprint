# My Make and Do sprint

**This is yours now.** You took a copy; nothing you write in here goes back to the class repo
unless you decide to put it there.

The class handbook lives at **[vrnico/make-and-do](https://github.com/vrnico/make-and-do)** —
that's where each day's page appears. You read it. You don't push to it. This folder is where
you actually work.

---

## What's in here

```
00-SCOPE.md         the one thing + your demo-ready definition
01-MAP.md           the four stages + your genesis component
02-CHECKPOINTS.md   your eight checkpoints + the riskiest part
log/day1.md         one paragraph a day
scratch/            yours. gitignored. never leaves this machine.
```

**Your actual project goes in here too** — alongside these files, in whatever shape it wants.
A folder of code, a Unity project, a set of audio stems, a single `index.html`. The five
markdown files are the sprint record; the rest of the folder is the work.

---

## `scratch/` is not a mistake

It's in `.gitignore`, which means git ignores it completely and nothing in it is ever committed
or pushed. Put the things in there that shouldn't be public: API keys, a client's real name,
the ugly first draft, a to-do list that's really a diary.

**Knowing which parts of your project are public and which aren't is most of what a `.gitignore`
is for**, and it's worth getting the habit now rather than after you've pushed something you
regret.

---

## Making this your own repo

**If you cloned this**, git still thinks it belongs to the class. Cut that link and start your
own history:

```bash
rm -rf .git          # forget where this came from
git init             # start your own history
git add -A
git commit -m "day 1"
```

**If you downloaded the ZIP**, there's no git in here at all — skip `rm -rf .git` and start at
`git init`. Shorter path, same destination.

Then make an empty repo on GitHub — **no README, no .gitignore**, it wants to be empty — and:

```bash
git remote add origin https://github.com/YOUR-NAME/YOUR-PROJECT.git
git push -u origin main
```

**That's the Shipping Software hour, not the class hour.** If any of it doesn't work, bring it
there and we'll sort it out. Nothing in the 90 minutes depends on this working.

---

## Committing as you go

Once a day is plenty for a fortnight:

```bash
git add -A
git commit -m "day 2 — the assumption held"
git push
```

Eight commits across eight days is a real record of how the thing was built, and it's worth
more than it looks. You'll want it when someone asks what this actually took.
