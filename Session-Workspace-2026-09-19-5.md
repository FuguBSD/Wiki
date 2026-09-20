# Session Workspace 2026-09-19 5

Session: a1ffef58-9def-4f84-a8ea-85736d9af5bf
Project: Workspace
Opened: 2026-09-19T19:55:44Z

## Observations

Claim: FuguPass ORC-REVOKE-8 and ORC-REVOKE-11 state a retirement guarantee that the FuguOracle code breaks for a record at the last strike.

A revocation sends get_pin at counter 0xFFFFFFFF with a wrong PIN. When the
record already holds count 2, oracle.c takes the wipe branch of OPS-GET-6
instead of the strike branch of OPS-GET-5, so pindb_wipe unlinks the file. A
later set_pin then loads PINDB_MISSING and enrolls the name again. ORC-REVOKE-11
claims that the records of that name never enroll again, and that claim fails in
this one case. The lock holds in every other case, because get_pin rejects a
counter at or below the stored counter before the PIN comparison. A locked
record at 0xFFFFFFFF therefore answers junk and moves no count.

Evidence: oracle.c of FuguOracle 6a78eb5, the replay guard above the hash
comparison in get_pin, and the else branch that calls pindb_wipe. The defect
sits in FuguPass, not in FuguOracle, and it is older than Oracle 003. It belongs
to the Pass 009 wave that lands the retirement rule.

Claim: a STATUS row reads `partial`, not `done`, when one rule of the unit names an HTTP status that no program can send yet.

FuguOracle plan 003 asked for `done` on every unit it cites. Six units hold a
rule that the state machine cannot implement: PROTO-ENVELOPE-2 (400),
PROTO-PAYLOAD-5 (500), OPS-SET-7, OPS-GET-7, OPS-JUNK-1 to OPS-JUNK-3, and
OVW-PURPOSE-3 with OVW-PURPOSE-4. The legend of the register decides it, and
PROTO-RESPONSE already read `partial` for this reason before the change.

Claim: that one reading costs three more edits, and a review round finds each one late.

The plan contract makes a plan cite each unit that is not `done`, so plan 004
gained six citations. The test list of that plan then had to reach the 500 case
and the junk case that the new citations claim. The roadmap makes each unit of a
phase read `done` at the exit of that phase, so three rows moved from P2 to P3.
Plan the three edits with the first one; each round that finds them costs a
panel pass.

Claim: a panel writes a new falsehood into the text it repairs, twice in one change.

Round 1 repaired the OVW-PURPOSE note and left it asserting and denying the same
two rules. Round 2 repaired that, and kept the clause "The design names no
client product", which D-01 contradicts: Blockstream Jade is the reference
client. `origin/main` held no copy of the clause, so the change wrote it. Read
the cited decision, not only the cited rule.

Evidence: FuguOracle PR 12, merged 2026-09-20 as 626692e. The panel ran three
rounds and left ten residue entries.

Claim: the session rule of FuguBSD now triggers on the context, and it sits in one file for every repository.

The old rule landed one deliverable in one session. It was stated five times:
twice in the workspace rules, in the `pull-it` skill, in Tooling REV-MERGE-3,
and in the master plan. The new rule reads "Carry the work while the context has
room. Start a new session when it does not." It sits in the root `CLAUDE.md` of
the org pack, which every repository consumes. The `pull-it` skill lost its
`## Stop` section, and the workspace rules lost two bullets. The change removed
more words than it added in both repositories.

Claim: eleven consumers took the pack in one pass, and the sync carried older drift with it.

FuguCTX, FuguSTX, FuguWeb and Repositories also took `deps/KEYS.txt` from
Tooling 4ebcb0f, the releng key, which each had missed. The workspace `main` had
been red for three commits on that same drift: the Sync job reported
"deps/KEYS.txt: content differs". A sync gate catches a stale consumer, and a
merge from the main checkout hides the red from the session that caused it.

Claim: two recorded local test failures did not reproduce.

Fugu `openpgp.t` and FuguWeb `keys.t` were recorded in 2026-09 as failing on the
operator Mac while CI stayed green. Both passed on 2026-09-20, in full runs of
841 and 346 tests. FuguVM `mirror.t`, red since 2026-09-13, passed too, because
`15f6468` repaired it. A stale exemption hides a real defect.

Evidence: Tooling 6845c37, Workspace 2e1c48d and 6a4a577, and the eleven sync
commits of 2026-09-20. FuguSTX CI failed one time on an upstream Gutenberg 504
in a network step, and the rerun passed.
