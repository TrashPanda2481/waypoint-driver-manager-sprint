# 02 · The riskiest part

*Day 1. Today's checkpoint is that this is written down and you've started it.*

## What's most likely to not work?

**→** BIOS/legacy boot. Every boot test I've run, real hardware or VM, has been UEFI. I haven't
even started on the BIOS-mode GRUB core (needs the ntldr module), so I genuinely don't know what
that's going to take yet.

Be specific. Not *"the whole thing"* — the one piece where, if it turns out to be impossible or
much harder than you think, everything else stops mattering.

**Why is it the riskiest?**

It's the only piece I haven't touched at all, and mixed BIOS/UEFI setups are known to be a pain.
If it's a lot harder than the UEFI side was, some of the older machines I actually want this
stick for just can't use it — and that's not something I can patch around later.

---

## Tomorrow

**A checkpoint is a thing that either exists or doesn't** at the end of a day. Not *"work on the
renderer"* — **"the renderer draws one frame."**

A percentage-complete estimate is a wish. It sits at 80% until you run out of days. Something
binary is the only kind that tells you you're behind while there's still time to do anything
about it.

**Tomorrow, this will exist:**

▢ An i386-pc GRUB core boots to my menu in a BIOS-mode VM.

*One thing. Small enough that you'd be embarrassed to miss it.*
