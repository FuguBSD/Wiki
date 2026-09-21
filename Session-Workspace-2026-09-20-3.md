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

Claim: two implementer agents that share one checkout race on the git index,
even when their file sets are disjoint.
Evidence: FuguPass plan 003, 2026-09-20. Three agents ran in parallel on
disjoint files. The vectors agent ran `git add` on its six paths, and the C
agent staged its own files before the first agent reached `git commit`. The
commit took both sets. The vectors agent repaired with `git reset --soft
HEAD~1`, the C agent ran the same repair, and that reset the first repair away.
The history came out correct after the second rebuild, and no work was lost.
A disjoint file set is not enough, because `.git/index` is one shared file. The
recipe of the master plan says to run packages in parallel when their file sets
are disjoint. That rule needs a staging rule beside it: each agent must commit
with `git commit --only -- <its paths>`, and never with a bare `git add` plus
`git commit`. A per-agent worktree would also settle it.

Claim: the custody regress tests of FuguPass plan 003 catch the defects they
target. Two mutations prove it, in the OpenBSD 7.8 guest at commit `a39bd73`.
Evidence: mutation 1 changed the GF(256) reduction constant `FIELD_LOW` from
`0x1b` to `0x1d` in `src/share.c`. The field, split and combine groups all
failed, with 21 mismatch lines and exit 1. The restored build gave exit 0.
Mutation 2 replaced `BN_mod(key, num, mod, ctx)` with `BN_copy(key, num)` in
`derive_client_reduce()` of `src/derive.c`, which skips the modulus of
KEY-CLIENT-2 and keeps the addition of 1. One line failed: "the edge client
key". The restored build gave exit 0.
The second mutation is the useful one. Every real `t_ei` stays below `q - 1`,
so the reduction is a no-op for each derived vector, and the slot 17 vector
cannot catch a missing modulus. Only the synthetic edge vector of 32 `0xff`
bytes catches it. A KAT of a reduction needs an input that the derivation
cannot reach.
Method note: run the built program from the `obj` directory, because `make obj`
puts it there and the source directory holds none. Also run `fuguvm` from the
project directory, because the untracked `.fuguvmrc` sets `state_dir`, and
`fuguvm` reports "VM 'default' not found" from anywhere else.

Closed: 2026-09-21T00:24:14Z
