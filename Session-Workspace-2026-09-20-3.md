# Session Workspace 2026-09-20 3

Session: 4669f3b0-1a71-40f4-ac52-b6e5a6a41651
Project: Workspace
Opened: 2026-09-20T18:09:18Z

## Observations

Claim: OpenBSD 7.8 `bsd.prog.mk` supports `PROGS`, so one regress directory can
hold several test programs with no subdirectory split.
Evidence: read in the guest through `fuguvm ssh -- grep -n -B2 -A6 PROGS
/usr/share/mk/bsd.prog.mk`. Lines 82 to 105 hold `.for p in ${PROGS}`, with
`SRCS_$p ?= $p.c`, `DPADD_$p ?= ${DPADD}` and `LDADD_$p ?= ${LDADD}`.
`bsd.regress.mk` lines 40 to 51 build a `run-regress-$p` target for each name of
`${PROG} ${PROGS}`.
This matters for FuguPass PROG-BUILD-2, which puts every test source in the one
directory `src/regress`. A second test program needs `PROGS= kat share` in the
one Makefile. Line 104 also sets `MAN ?= ${PROGS:=.1}`, so that Makefile must
suppress the man pages.
