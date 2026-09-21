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
