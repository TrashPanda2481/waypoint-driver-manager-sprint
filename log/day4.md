# Day 4

*2026-09-04. One paragraph. What I made, what broke, what I decided.*

**Made:** the decision to stop writing Python and rewrite the whole thing in C# / .NET 8, written
up as `ADR-0001` so it is a decision with reasons attached and not a mood. Then most of the port
in one sitting: the pure core (models, matching) with tests, the engine slice (audit log, plan,
orchestration, mock backend), the local content-addressed cache, all four OEM sources (Dell
per-device, Dell driver-pack, Lenovo, HP), the Windows Update Catalog source with hand-declared
COM interfaces, and Authenticode signing wired into the publish pipeline behind a self-signed
development certificate. Twenty commits. The driver of the whole thing is one line from day 1's
faked list that never got better with more Python: PyInstaller builds get flagged by Defender and
SmartScreen, and a driver tool that antivirus quarantines is not a driver tool. Native AOT gives a
~7MB signed binary with no runtime to install.

**Broke / found:** four real bugs, three of them in the Python I was porting from, which is the
honest argument for a port doing more than changing language. The signature enum's zero value was
`WHQL`, so an unset signature defaulted to the most-trusted tier - the policy gate was fail-open,
and every `default()` sailed through it. The `Device` record compared its hardware-ID list by
reference where Python's frozen dataclass compares by value. `rank_candidates` sorted the driver
date ascending inside a trust tier, so `build_plan` was being handed the *oldest* candidate in the
best tier, not the newest. And CAB extraction was broken on Windows in both trees at once:
`expand.exe` refuses to "expand a file onto itself" and still exits 0, and for a single-member cab
it ignores `-F:` and names the output after the CAB - which means day 1's "all four OEM pipelines
live-validated" was validated on Linux with `cabextract`, and the Windows path had never actually
run. Both Dell sources were dead on arrival and nothing had noticed. Separately, signing hooked
`Publish`, but AOT's `CopyNativeBinary` also hooks `Publish` and replaces the apphost afterwards -
signtool reported success on a file that was then thrown away, so the shipped binary was unsigned
while the build said otherwise.

**Decided:** port rather than translate. Where the Python was wrong, the .NET side fixes it and
`ADR-0001` gets a "deliberate behavioural divergences" section, so nobody later "fixes" the
divergence back. Branch the rewrite rather than replace `main`, so the working tree stays working
until each module has a validated replacement. And self-signed for now, with the real certificate
still to come - the pipeline is the thing being proved today, not the trust.

**Checkpoint - hit or missed, and why:** hit for the port, and it moved faster than expected
because the core was already pure and well-tested - that day-1 decision paid here. Missed on the
thing that actually matters: still nothing has touched real Windows hardware, and P2, the
reversible install chain, is exactly as unproven tonight as it was on day 2. Finding that the CAB
path had never run on Windows is a warning about the rest of the faked list. "Validated once" is
not the same claim as "validated on the platform it ships to."
