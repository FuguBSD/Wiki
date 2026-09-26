# Session Workspace 2026-09-26 1

Session: d1255b02-81ad-5714-805e-151b057cd998
Project: Workspace
Opened: 2026-09-26T05:38:25Z

## Observations

Claim: the OpenBSD 7.8 aarch64 package index holds no p5-App-cpanminus, so the FuguOracle guest bootstraps cpanm from https://cpanmin.us after `pkg_add p5-IO-Socket-SSL`, and `cpanm --notest Fugu` then installs Fugu 0.5.1 from CPAN in 14.5 s.
Evidence: the Oracle 006 package 1 run of 2026-09-26; `pkg_add -I p5-App-cpanminus` printed "Can't find p5-App-cpanminus", and the mirror index matched no "cpanminus" line.
Claim: `cpanm --version` hangs under `fuguvm ssh`, because cpanm reads module names from a non-tty stdin when no argument remains after the options; a cpanm step in a guest script must carry an argument.
Evidence: the same run; the wait channel of the hung process read `piperd`.

Claim: a uniform byte mutation of a well-formed protocol v2 get_pin envelope reaches the reject class alone; 10000 random mutations gave 10002 rejects, no junk and no real decision, on both FuguOracle and the upstream server.
Evidence: the Oracle 006 package 2 run of 2026-09-26, `scratch/fuzz-006-p2.log` of the FuguOracle clone in the bridge-cse_01JurJhpREZHMfnGGBDFKrDM worktree; the summary line reads `10000 mutations in 558 s: reject 10002`.
The implementer's explanation, unverified: the tag covers every byte of the envelope after the counter, so any single-byte change breaks the tag before the PIN comparison. The junk and real classes of the differential fuzzer come from the two directed cases and the premise check alone. A fuzzer of the junk surface needs a mutation of the plaintext before the encryption, which the plan does not hold.

Claim: FuguOracle plan 006 merged as PR 15 (`3c2b630`) on 2026-09-26 after two panel rounds; the guest takes the Fugu distribution as a host tarball through `FUGUORACLE_FUGU` and installs it with its own `Makefile.PL`, with no cpanm, no extra package and no network.
Evidence: PR 15 of FuguBSD/FuguOracle, and `scratch/interop-006-fix1.log` of the FuguOracle clone in the bridge-cse_01JurJhpREZHMfnGGBDFKrDM worktree (`1..31`, exit 0 after 900 s).
Claim: two panel members named `pkg_add p5-App-cpanminus` as the remedy for the cpanm fetch, and the 7.8 aarch64 package index (12508 lines, 1946 `p5-` packages) holds no `cpanminus`; a measurement before the fixer brief settled the remedy in one round.
Evidence: `scratch/index-7.8-aarch64.txt` of the same clone, fetched from cdn.openbsd.org on 2026-09-26; the ledger entry 2 of `scratch/review/ledger.md`.
Claim: `gh pr checks --watch` issued in the same command as the `git push` can race the check registration and exit 1 with "No checks reported"; a second watch a few seconds later sees both checks.
Evidence: `scratch/pr-checks-2.log` of the same clone, first and second run, 2026-09-26.
