# Session Workspace 2026-09-21 2

Session: 746bcad8-f93b-4ab7-8689-46b5bf96ffa5
Project: Workspace
Opened: 2026-09-21T05:17:53Z

## Observations

Claim: the upstream `blind_pin_server` runs on the macOS host as a FuguPass
harness counterparty, and it answers `GET /` with 200.

Evidence: a clone of Blockstream/blind_pin_server under `scratch/`, a venv on
Python 3.13, and a start from a fresh working directory.

The recipe. Build the venv with `uv venv --python 3.13`, and install the
requirements with `uv pip install --require-hashes -r requirements.txt`. Python
3.14 fails: the pinned wallycore 1.5.3 publishes no cp314 wheel, and the source
build needs autotools that the host does not hold. Generate the key with
`PYTHONPATH=<parent of the checkout> python -m
blind_pin_server.generateserverkey`. Start the server with `python -m flask
--app blind_pin_server.flaskserver run --host 0.0.0.0 --port 8096`.

The working directory of the server holds `server_private_key.key` at mode 0600
and an empty `pins` directory. `pindb.py` takes the record store from the `pins`
directory of the working directory, so a fresh working directory gives a fresh
store (FuguPass TEST-MASK-6).

The server speaks plain HTTP. FuguPass `http_post()` accepts the scheme http, so
a client in the OpenBSD guest can reach the host server at the QEMU gateway
address (FuguPass TEST-HARNESS-7).

Claim: a reused value form gave the FuguPass replay counter a slot-index bound,
and the client would stop sending a valid counter in 2038.

Evidence: `src/vault.c` `value_ok()` maps `VAULT_VALUE_NUMBER` to
`vault_number(text, len, VAULT_SLOT_MAX, &value)`, and `VAULT_SLOT_MAX` is
`INT32_MAX`. The counters file reuses that form. The implementer read the clamp
as a conflict between VAULT-FORMAT-7 and ORC-COUNTER-5, and it wrote the clamp
into `src/oracle.c` with a comment.

The reading was wrong. VAULT-FORMAT-7 states a form, "unpadded decimal ASCII",
and no bound. `src/vault.h` ties the 2^31 bound to KEY-ENTRY-1, which bounds a
slot index. A replay counter is not a slot index, and ORC-COUNTER-5 states that
the Unix-seconds scheme stays below `0xFFFFFFFF` until 2106. No specification
change was needed, and the code disagreed with two rules that were already
correct.

Claim: FuguPass TEST-MASK-2 cannot be written against a client that returns the
share alone.

Evidence: `oracle_reveal()` gives `share(K_e, i)`. KEY-SHARE-5 makes that value a
function of `K_e`, the oracle index and the threshold, and not of the mask.
`oracle_enroll()` rewrites the wrap as `c_ei = share(K_e, i) XOR f(s_ei, ...)`.
A re-enrollment therefore changes the mask and the wrap together, and the
revealed share stays byte-identical.

TEST-MASK-2 asserts that a re-enrollment changes the answer. A test over the
share asserts the opposite of that rule. The interface needs an optional out
parameter for the answer plaintext, and the mask-stability test is its one
caller.

Read an interface against the test that must use it. A test list in a plan can
name a rule that the landed interface cannot express, and every gate stays
green.

Claim: `src/oracle.c` of package 1 compiles clean on OpenBSD 7.8 arm64 under
`-Wall -Wextra -Werror`, and it lands in `libfugupass.a`.

Evidence: a `git archive` of the branch, `fuguvm put`, and `make obj && make` in
the guest. `ar t` lists `oracle.o` in the archive, and the 25 transport tests
pass.

Claim: `make ste-lint` checks no C comment, so the largest body of prose in
FuguPass and in FuguOracle passes every gate unread.

Evidence: `_files()` of `scripts/ste-lint` collects `.md` files of the root and
of `spec/` only. Tooling STE-SCOPE-1 states that scope, and it names `.github/`,
`.claude/`, `lib/`, `plans/` and `t/`. It names no source directory. The
FuguPass copy of the script is byte-identical to `org/sync/scripts/ste-lint` of
the org pack, so the copy has not diverged.

The scope is deliberate. The consequence is not. `CLAUDE.md` of FuguPass says
"Write every artifact and every reply in ASD-STE100 Simplified Technical
English. `make ste-lint` enforces it." FuguPass holds about 5,000 lines of C,
and the comment blocks of `src/` carry the design prose of the tree. None of it
reaches the lint. The same holds for FuguOracle.

A change belongs in Tooling, in STE-SCOPE-1 and in the script. A C comment needs
a fence-aware reader, because a comment block holds code examples and unit IDs.
This is outside FuguPass plan 005.

Admitted: a rule that names its own enforcement can still leave the main
artifact unchecked. Read the scope of a gate before you trust the sentence that
cites it.

Claim: the FuguPass record client works end to end against the upstream
`blind_pin_server`, and a re-enrollment changes the mask while the revealed
share stays byte-identical.

Evidence: a build of branch `feat/005-oracle-records` at `5e7b1ee` in the
OpenBSD 7.8 arm64 guest, and `oracle-client` against a host counterparty at
`http://10.0.2.2:8096`.

The share after a re-enrollment stayed `62e0050f...`, and the mask moved from
`530718751db1e3fc...` to `f0e360b7b4ec5a33...`. This measures the finding that
the gate raised against the landed interface: a TEST-MASK-2 test over the share
asserts the opposite of the rule that it cites. The fifth parameter `maskout` of
`oracle_reveal()` carries the answer plaintext, and the test needs it.

The other legs also answered. Two wrong passphrases each gave `junk` with a
different answer, and the field widths stayed equal (ORC-REVEAL-4, TEST-MASK-4).
The third strike wiped the record, and the correct passphrase then gave `junk`
that never equalled the old mask (TEST-MASK-3). A canary enrollment and its
round trip gave `ok`, and a mistyped second read gave `error` (ORC-CANARY-6,
ORC-CANARY-7). The counters file held one line for each record name.

Claim: the root partition of the FuguVM OpenBSD guest holds 167 MB, and a build
of the FuguPass tree fills it.

Evidence: a build under `/root` stopped with "IO failure on output stream: No
space left on device" at `vault.o`, after it linked five of the six programs.
`df -h` then read 106% on `/dev/sd0a`, and 15% on `/dev/sd0l` at `/home`, which
holds 1.1 GB.

Put the work directory of a guest build on `/home`. The FuguOracle
`regress/guest` script already states that rule, and a session that copies a
tree by hand loses it. The failure looks like a compiler defect, and it is a
full disk.

Claim: the FuguPass interop harness invented a FuguOracle deployment that
FuguOracle has not designed, and every FuguPass gate stayed green.

Evidence: `tests/harness` at `76e0ab9` held a counterparty record that probed
`/etc/rc.d/fuguoracle`, the user `_fuguoracle` and `fuguoracle-keygen`, and that
started the service with `rcctl -f start fuguoracle httpd` against a store at
`/var/www/fuguoracle`. FuguOracle `spec/STATUS.md` reads `open` for DEPLOY-HTTPD
and for DEPLOY-SERVICE. Its SEC-SANDBOX note says the chroot and the dedicated
user need the rc.d script of DEPLOY-SERVICE. No FuguOracle unit names any of
those paths today, and `regress/cgi.sh` runs the CGI program directly, with the
variables in the environment and the body on standard input. No FuguOracle
service speaks HTTP yet.

Only the `probe` closure ever ran, so the start, stop and release paths were
unproven code that could not run. The probe rested on a build at
`/home/fuguoracle/src/fuguoracle`, which is a leftover of an earlier session and
not an artifact that any repository produces.

The plan asked for the record: "When a FuguOracle build exists, a second record
names its stack in the guest." A build of the program is not a deployment of the
service, and the plan read the one as the other.

Admitted: a test that skips is not a test that works. A counterparty record
whose start path never ran is a guess at a sibling interface, and a skip line
reports it as a healthy absence. Probe availability at the artifact that the
sibling actually publishes, and read the sibling register before you write the
recipe.

Claim: three FuguPass rules require an interop leg that cannot run, because
they trigger on a FuguOracle build and not on a FuguOracle deployment.

Evidence: `spec/testing.md` TEST-HARNESS-2 and TEST-MASK-5 each read "When a
FuguOracle build exists, the harness must run against FuguOracle", and
`spec/DECISIONS.md` D-19 carries the same phrase. A build of the FuguOracle
program sits in the guest today. No FuguOracle service speaks HTTP, because
DEPLOY-HTTPD and DEPLOY-SERVICE of FuguOracle both read `open`.

So the rules, read to the letter, demand a leg against an artifact that answers
no request. The correction is one word, and it is not a typo: under "build" the
leg is mandatory now, and under "deployment" it waits on the FuguOracle
deployment units. That moves the acceptance bar of a whole phase, so the
operator decides it, not an agent. D-19 is a decision, and a change of a
decision text needs human approval.

Admitted: a trigger word carries an acceptance bar. Read the trigger of a rule
against the artifact that the sibling repository actually publishes, and count
every site before the fix: this one sits in two rules and one decision.

Claim: the third-strike leg of the harness proves FuguPass ORC-CONFORM-2, the
clause that a client key must not depend on the passphrase.

Evidence: three reveals under a wrong passphrase wiped the record that an
enrollment under the correct passphrase created. A client key that depended on
the passphrase would address another record, and the enrolled record would keep
its strikes. The coherence table of the master plan assigns that proof to the
harness, and the leg earns it without naming it.

Claim: the FuguPass interop harness proves FuguOracle CLIENT-MODEL, the clause
that a client key must not depend on the passphrase, and the leg that proves it
carries no name that says so.

Evidence: the coherence review of FuguPass plan 005 against FuguOracle. In
`2-reveal.pl`, the wiped-record vault takes three reveals under a wrong
passphrase, and then one reveal under the correct passphrase that must answer
junk. A passphrase-dependent client key would address a missing record at each
wrong guess, burn no strike, and let the fourth call answer `ok`. The leg
therefore fails when the client key depends on the passphrase.

The master plan assigns that proof to the harness, and the register claim on
ORC-CONFORM is honest. A test can earn a shared-artifact claim without citing
it, and the citation is what a later reader needs.

Claim: a register must not rest a unit state on an artifact that no repository
produces.

Evidence: the FuguPass TEST-HARNESS and TEST-MASK notes said "a FuguOracle build
exists in the guest". That build is a leftover of an earlier session in one
developer's guest. FuguOracle ARCH-STACK-5 is absent and TEST-INTEROP is open,
so no FuguOracle artifact states it, and no gate reproduces it.

The register now names what the leg needs, and the two FuguOracle units that are
open. That statement holds on any machine.

Admitted: a fact measured in a scratch guest is evidence for a session, and it
is not a fact for a register. Ask what reproduces a claim before it enters the
one record of implementation state.

Claim: a mutation of the counterparty proves an interop assertion, and it is
the one proof that separates a live assertion from a vacuous one.

Evidence: round 2 of the FuguPass plan 005 panel. Two rewrites of the same
reveal leg were vacuous, and each looked green over 100 assertions. The first
compared field widths that `print_hex()` makes equal by construction. The second
compared answers of four records, and `vault()` of `tests/harness` takes the
next slot at each call, so four client keys gave four answers under any oracle
behavior.

The third rewrite drives every cause against one record, and the fixer proved it
by mutating the upstream `blind_pin_server`. A fixed junk key in `pindb.py`
failed "the wiped record and the stale counter give two answers". A `pass` in
place of the counter assertion failed "a counter below the stored one gives
junk". It restored `pindb.py` and checked the digest.

Admitted: a passing count is no evidence. Ask of each new assertion what
mutation fails it, and prefer a mutation of the counterparty when the claim is
about the counterparty.

Claim: the strike arithmetic of a FuguOracle record does not follow the order
that a reader expects, and a test that assumes the expected order tests nothing.

Evidence: FuguOracle OPS-GET-4 persists `count = 0` on a correct pin, so a
successful reveal resets the strike count. OPS-GET-2 runs the anti-replay check
before the pin comparison, so after a forged low counter even a wrong passphrase
burns no strike. OPS-GET-5 persists the counter of the client on a wrong pin, so
the two sides stay in lockstep.

The main session briefed an order that rested on the expected reading, and the
fixer read the sibling rules and corrected it. Read the sibling specification
before you write the order of a test, and name the rule that each step rests on.
