# Day 7

*2026-09-10. What I made, what broke, what I decided.*

**Made:** the window honest. Fixed an overlap where the empty-state message and the device tree
shared one grid cell, added a filter that pulls up only the devices sharing a hardware ID, gave
the first run something to say instead of a blank pane, and stopped the source options staying
live during a scan. Wrote a parametric icon generator that draws a mark as vector geometry and
rasterises it at every size Windows asks for, rather than downscaling one bitmap into mush at
16px. No mark chosen yet.

**Broke / found:** the overlap was mine, from assuming an empty tree draws nothing - the tier rows
render at count zero, so "Missing (0)" and the message painted on top of each other. But it was
hiding something worse. The window said **"Nothing needs attention"** while the status bar
underneath it said 103 of 233 devices share a hardware ID. Both statements were true and the
headline was still a lie, because the thing the tool exists to find was sitting one line below a
message saying nothing was found. It was reachable only by ticking a checkbox and then hunting
badges across 24 class groups. Separately, driving the window through UI Automation instead of
looking at screenshots caught two bugs a screenshot cannot show: every tree row exposed
`Waypoint.Gui.TreeNode` as its accessible name, so a screen reader would announce the class name
for every device, and `IsExpanded` was bound to nothing, so the tiers the spec says must open on
scan were staying shut.

**Decided:** an empty state has to report what was actually found, not what was absent. "Nothing
needs attention" is a defensible summary of the tier counts and a bad summary of the machine. And
test a GUI by driving it, not by looking at it - the two real bugs today were both invisible in a
screenshot and both obvious the moment something tried to *use* the window.

**Checkpoint - hit or missed, and why:** hit for the window, which is now something I would put in
front of a technician. Missed, for the fourth session running, on P2. Worth writing the pattern
down plainly rather than logging it politely again: every session since day 3 has improved the
parts that were already working and left the one unproven subsystem alone, because enumeration and
packaging and windows are things I can finish in a sitting and a real install on a real machine is
a thing that can break the machine. That is a reasonable fear and it is also the whole reason day
2 circled P2 in the first place. The sprint is not short of progress. It is short of the specific
evidence it said on day 2 it needed.
