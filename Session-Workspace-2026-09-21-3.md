# Session Workspace 2026-09-21 3

Session: 85c63c8c-1326-444c-b94e-216955a9a178
Project: Workspace
Opened: 2026-09-21T13:01:03Z

## Observations

Claim: `fuguvm ssh` allocates no pty, so a `readpassphrase(3)` prompt cannot
run over it. The command runs through libssh2 `channel->exec`. FuguPass
TEST-HARNESS-8 settles the design: `fuguvm expect` runs a caller script
against the serial console of the guest.
Evidence: `Fugu::SSH::run_command` in Projects/Fugu, and the FuguPass leg
`tests/harness.d/7-create.pl`, which passes with 33 assertions.

Claim: the console root password of a FuguVM guest is a random 32-character
value. `App::FuguVM::Guest::_root_password` makes it, and it writes it into
the guest state file. `fuguvm` exposes it through no command and no status
key, so a caller reads the `root_password` key of that file.
Evidence: the state file of the guest of this run, and the FuguPass harness
variable `FUGUPASS_CONSOLE_PASSWORD`.

Claim: an unveil violation on OpenBSD 7.8 gives `ENOENT` for a path outside
the list, and `EACCES` for an unveiled path used beyond its permission. It
never gives `EPERM`.
Evidence: `src/regress/sandbox` of FuguPass, measured in the arm64 guest.
FuguPass plan 006 claimed `EPERM`, and the measurement corrected it.
