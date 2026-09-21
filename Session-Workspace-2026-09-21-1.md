# Session Workspace 2026-09-21 1

Session: d4f42283-d485-4655-a273-18d6564acaf6
Project: Workspace
Opened: 2026-09-21T00:24:44Z

## Observations

Claim: a plan's Files table must name every copy list, or a source that it adds builds nothing.

FuguPass plan 002 named src/seal.c, src/vault.c and src/entry.c. It named
neither src/lib/Makefile, which holds the SRCS line of the archive, nor
src/regress/Makefile, which holds the PROGS line of the test programs. A source
that no SRCS line names compiles into no archive, and a test program that no
PROGS line names never runs. The main session put both files in each
implementer dispatch, so the gap cost this run nothing.

This is the defect class that FuguOracle plan 004 recorded: regress/guest held
its own SOURCE list, six packages added six files to the tree, every gate
stayed green, and the documented entry point could build neither program.

Claim: a plan's Acceptance can name a register state that its own packages do not earn.

Plan 002 demanded that KEY-ENTRY read partial with KEY-ENTRY-1 as the only
absent rule, which asserts that KEY-ENTRY-3 landed. Its Purpose said "a test
can write a vault that no oracle has touched, and read it back". Four packages
later, grep found seal_seal() and seal_open() called from src/regress/vault.c
alone. The seal existed, the atomic write existed, and no function composed
them, so no code sealed an entry file under K_e. The implementer of the
register package reported the gap instead of writing the state the plan asked
for. Read a register state against the code, never against the plan that
requested it.
