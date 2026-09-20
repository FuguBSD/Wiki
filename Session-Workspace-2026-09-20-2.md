# Session Workspace 2026-09-20 2

Session: 2026-09-20
Project: Workspace
Opened: 2026-09-20T16:52:27Z

## Observations

Claim: BSD make evaluates a `.if` condition at parse time, and `bsd.own.mk` sets
`__objdir` after that, so a condition `.if exists(${LIBFUGUPASS}/${__objdir})`
tests the bare directory and always takes the first branch.
Evidence: the FuguPass 001 guest build, OpenBSD 7.8 arm64. At use time the same
variable expands to `obj`, so the link line read `-L.../lib/obj` while the
archive sat in `.../lib`, and it failed with `ld: error: unable to find library
-lfugupass`. The empty parse-time expansion was visible as a double solidus in
`Warning: target .../lib//libfugupass.a (prerequisite of: kat) does not have any
command (BUG)`. The literal name `obj` in the condition and in both lines of the
branch repaired it, and the link succeeded.

Claim: `make regress` in a `bsd.subdir.mk` directory runs the regress loop only,
and never builds a library that the test program links, so the command fails on
a clean tree.
Evidence: the same guest build. A clean copy of the tree gave the same
`-lfugupass` error after the first defect was repaired, because the `lib`
subdirectory never built. The line `regress:: all` above `SUBDIR` repaired it.
The double-colon operator is a requirement, because `bsd.subdir.mk` declares the
target that way: a single colon gave `Parse error in /home/clean2/src:
Inconsistent dependency operator for target regress (was regress:, now
regress::)`. The cost is that `bsd.regress.mk` ties its `all` target to the test
run, so the test program runs twice, and both runs pass.

A host gate can catch neither defect. The host carries no BSD make, so `make
check` stayed green through both.
