# Day 5

*2026-09-06. Shared with Meridian. What I made, what broke, what I decided.*

**Made:** the parts that decide whether anyone can actually get this onto a machine. Settled the
artifact naming so the three things stop borrowing each other's names - `waypoint.exe` is the CLI,
`waypoint-desktop.exe` is the GUI, `waypoint-installer-*` is whatever carries them onto a machine.
Built both distribution shapes off one publish: a portable zip that unzips and runs and touches
nothing, and a per-machine WiX MSI that puts `waypoint` on PATH, adds a Start Menu entry, and
appears in Add/Remove Programs so it can be uninstalled like software rather than deleted like a
download. Added `docs/TODO.md` as the live worklist with the four cutover gates written down, so
status is a file I update instead of something I re-derive every session. Trimmed code comments
from 13.6% to 11.3% of source bytes, on the principle that a comment is a searchable label for a
section, not an essay about it.

**Broke / found:** the demo. There was a self-test that printed what looked like a real scan
finding a real out-of-date driver, and I let it stand because it looked good. It used a genuine
RTX 3060 hardware ID with entirely fabricated candidate data, and the fabricated "newer" driver
was actually older than the one really installed. Anyone shown that demo would have reasonably
believed the tool had found something on their machine. It had found nothing; it was printing a
fixture. Rewrote it with a deliberately impossible hardware ID and a banner saying it is a
self-test. This is the exact failure mode day 1 wrote the "what I'm deliberately faking" section
to catch, and it still got past me, because the fake was realistic enough to be flattering.

**Decided:** WiX 5, not 6 - version 6 requires accepting the Open Source Maintenance Fee EULA,
which is a licensing decision and not a technical one, and not one to make by accident while
picking a build tool. Ship both distribution shapes rather than choosing, because the portable zip
and the installed copy answer different questions and neither is the obvious default. And a demo
never gets to use realistic-looking invented data, however much better it looks - if the fixture
could be mistaken for a finding, it will be.

**Checkpoint - hit or missed, and why:** hit on packaging, which was never the risky part. Missed
again on P2. Two straight sessions of infrastructure while the one subsystem the whole pitch rests
on sits untouched. The honest read is that packaging was easier than hardware and I did the easy
thing twice. Worth naming so it does not happen a third time.
