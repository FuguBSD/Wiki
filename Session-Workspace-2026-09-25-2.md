# Session Workspace 2026-09-25 2

Session: 452a09c5-6eda-5c18-ac75-16ddbf8cc267
Project: Workspace
Opened: 2026-09-25T19:44:29Z

## Observations

Claim: a sibling change of the oracle JSON response form needs no FuguPass
follow-up, because the FuguPass reader is whitespace-tolerant.
Evidence: FuguOracle 005 (#14, `137cea0`) restated PROTO-RESPONSE-3 as the
no-space form `{"data":"<base64>"}` with one trailing newline. FuguPass
`src/regress/http.t` proves a body with tabs and newlines, and no FuguPass
document states the response form. Checked at the start of Pass 010,
2026-09-25.
The vocabulary gate stays byte-identical across the three repositories
(`eaf34b1f…`) after the three follow-ups, and OVW-VOCABULARY-3 holds the
vendored-copy sentence in all three.

Claim: a harness script that exports the fuguvm agent socket for its whole run
loses every git commit of that run with "Couldn't find key in agent".
Evidence: the Pass 010 mutation chain of 2026-09-25 23:50 ran nine mutations
in seconds with no proof, because `git commit` signs through the caller's
`SSH_AUTH_SOCK`, and the caller had exported `~/.fuguvm/agent.sock`. The
Bitwarden agent was unlocked the whole time. The chain passed once the script
set the fuguvm socket for `make harness` alone.

Claim: a fixer that gets a file list enumerates the sites in those files only,
so the list must be a grep over the whole tree, not a set of file names.
Evidence: the round 1 fix of Pass 010 (2026-09-26) corrected REC-PLATE-1 and
the five texts the dispatch named, and round 2 returned the same claim at
three prose sites of `spec/keys.md` and `spec/entries.md` that cite REC-PLATE.
The dispatch had said "every text" and then named the files. The round 2 fix
gets `grep -n REC-PLATE spec/*.md src tests` as the enumeration.

Claim: a specification rule that demands a computation with no consumer is a
false rule, and a panel reads it as a code defect first.
Evidence: Pass 010 merged 2026-09-26 as FuguPass #23 (`0418992`). REC-PLATE-1
asked the plate-alone scan to re-derive every entry key; an entry key opens an
entry file, and that path has none. Two reviewers of round 1 reported the code,
the remedy corrected the rule and five texts, and rounds 2 and 3 found the
same claim at four more prose sites, one in the approved share-split analysis.
The harness held seventeen legs and 868 assertions on `b4eacdc`.

Claim: a stale rule about a sibling's behavior survives every gate until a
document restates it, and the coherence review of that document is the one
reader that compares the two repositories sentence by sentence.
Evidence: Pass 013 merged 2026-09-26 as FuguPass #24 (`4800f58`). The runbook
followed FuguOracle OPS-GET-6 (the third strike wipes the record), and
ORC-REVOKE-8 and ORC-REVOKE-11 of FuguPass still claimed a permanent lock from
before Oracle 003; the Oracle 003 check of 2026-09-20 had recorded the gap and
assigned it to Pass 009, which landed the retired mark and left the rules.
Three panel members reported the rules in round 1, and D-03 settled the side.

Closed: 2026-09-26T05:38:07Z
