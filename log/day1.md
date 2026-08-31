# Day 1

*One paragraph. Five minutes. What you made, what broke, what you decided.*

**Made:** the launcher (manifest rendering, WinPE/live detection, launching tools, the Manage
Tools screen), seven built-in scripts, the WinPE build pipeline, my own branded UEFI GRUB2 menu
(WinPE plus Clonezilla, ShredOS, Memtest86+, GParted, SystemRescue), the deploy script, and about
41 tools auto-fetched. Built a real 239 GB stick and boot-tested it on real UEFI hardware.

**Broke:** reboot/shutdown was calling shutdown.exe, which just doesn't work inside WinPE — had
to switch to wpeutil. GRUB's theme and font stopped loading because grub-mkstandalone pins
$prefix to its own memdisk, so the font path I was using didn't actually exist — fixed by
pointing it at the ESP instead. The USB wipe kept racing Windows, which auto-inits a freshly
cleaned disk back to MBR before I could convert it to GPT — fixed by looping the convert until
it actually sticks. And Bitdefender quarantined and write-locked a bunch of repo files it didn't
like the look of (NirSoft, vt-cli, FRST) until I added exclusions.

**Decided:** build my own branded GRUB2 menu instead of trying to do the Ventoy arbitrary-ISO
thing — I already know the whole payload, so I don't need that. Split tools into a small WinPE-
baked core plus a big exFAT data partition so the same folder works whether it's booted or just
plugged in. Ship this as source with a fetch script instead of a prebuilt image, since most of
the tools are freeware I can't redistribute anyway.

**Checkpoint — hit or missed, and why:** Hit, and honestly went further than I expected. The
launcher rendering inside WinPE was the actual goal and it proved out in a VM, then the next day
it boot-tested clean on real hardware too.
