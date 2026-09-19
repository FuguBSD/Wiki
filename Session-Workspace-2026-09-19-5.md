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
