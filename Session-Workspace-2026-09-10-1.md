# Session Workspace 2026-09-10 1

Session: 17f1df87-9b8e-5e04-9a92-051bd03430b8
Project: Workspace
Opened: 2026-09-10T06:41:15Z

## Observations

Claim: the shared perl-release workflow of Tooling publishes the two tarballs,
`SHA256`, and `SHA256.sig` only, so FuguBench DIST-ASSETS-1 needs a Tooling
change for the `fugubench` and `install.sh` assets before the first release.
Evidence: `.github/workflows/perl-release.yml` on Tooling origin/main, the
`files:` list of the "Release to GitHub" step.

Claim: `perl/sync/scripts/dist` hard-codes `MIN_PERL_VERSION => '5.036'`, and
the FuguBench floor is v5.34, so the CPAN tarball of FuguBench would state the
wrong floor. Evidence: line 276 of the script on Tooling origin/main.

Claim: the Workspace `.toolingrc` holds no `wiki.` key, so the checkout walk of
FuguBench CLI-CHECKOUT-2 cannot reach the workspace for the library from a
project clone until `wiki.origin` lands there. Evidence: the Workspace
`.toolingrc` holds `sync.pack org` and `sync.pack infra` only.

Claim: the latest Fugu release 0.4.0 holds every module FuguBench plan 001
needs, and it lacks `Fugu::Curl` and `Fugu::Ed25519`, so the deps verb and the
update verb wait on a Fugu tag after Fugu plans 009 and 010 land. Evidence:
`gh release list -R FuguBSD/Fugu` and the `lib/Fugu/` tree of `v0.4.0`.

Claim: the eight FuguBench implementation plans pass `make check` on the branch
`plans-implementation` of the FuguBench clone in this worktree, and they cite
every one of the 42 units under `Implements:` across the set. Evidence:
`spec-check: 23 documents pass, 42 units, 194 rules` and `ste-lint: prose
passes` on 2026-09-10.

Claim: the plan set found six specification rules that the implementation must
reword with the code: CLI-CONFIG-2 (the home of a key), CLI-CHECKOUT-5 (the
verbs that read no checkout), CLI-SANDBOX-2 (the trace root, the library
directory, the start directory of `deps`, the running file of `update`, and the
network promise of `fetch`), CLI-CONFORMANCE-1 and CLI-CONFORMANCE-2 (fixture
and token changes), DIST-INSTALL-1 (`make dist` writes install.sh), and
DIST-KEY-1 (a test binds the keys). Evidence: the Constraints section of each
plan.

Claim: four pull requests merged on 2026-09-10 through pull-it with a
three-member panel each: FuguBench PR 1 (eight plans, three rounds),
Tooling PR 38 (plan 009, extra release assets, two rounds), Tooling PR 39
(plan 010, the dist floor from `.toolingrc`, three rounds), and Workspace
PR 8 (plan 003, the library origin in `.toolingrc`, three rounds).
Evidence: the round tables in each pull request body.

Claim: the panel found the FuguBench sandbox design wrong in two rounds.
Round 1: the rows unveiled no exec path. Round 2: an unveil of the command
path alone still fails, because unveil inherits across exec and a child
needs `ld.so`, the libraries, `sh`, the git helpers, and the CA bundle. The
landed design is: a verb that runs a child pledges and does not unveil; only
`version`, `shim`, `install`, and `traces` unveil. Evidence: FuguBench
plan 001 after PR 1, and ledger entries 1 and 19 of the panel.

Claim: a plan must not name a rule number that does not exist yet, and must
not name a sibling plan in prose. `spec-check` rejects the first as an
unresolved citation, and the panel rejects the second under the citation
rule of `spec/CLAUDE.md`. Evidence: the gate output on Tooling plans 009
and 010 before the fix, and Tooling ledger 010 entry 1.

Claim: residue of FuguBench PR 1 for the implementer of plans 001 and 007:
`Fugu::Sandbox->unveil` dies on an absent required path, so the home paths
of the unveil classes must be optional, and `install` must unveil the parent
of `~/.local/bin` with `c`. CLI-SANDBOX-1 needs the same rewording as
CLI-SANDBOX-2. Evidence: the residue section of the PR body.
