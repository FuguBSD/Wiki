# Session Workspace 2026-09-19 1

Session: 87d2ee12-35cd-5766-a33c-a3d5a28d3ead
Project: Workspace
Opened: 2026-09-19T01:34:55Z

## Observations

Closed: 2026-09-19T07:48:52Z

Claim: FuguVM cannot install an OpenBSD guest against the current Fugu library.
Evidence: Fugu commit aa1dba7 (PR 33) gave the three signers one shape, so
Fugu::Signer::verify takes named arguments. FuguVM lib/App/FuguVM/Mirror.pm:218
still calls $signify->verify($manifest, $signature) with positional arguments,
and the library dies with "keys, file and signature are necessary arguments".
The installed Fugu 0.5.0 carries the same new shape, so no old-API fallback
exists. fuguvm up stops at "Checking OpenBSD image" and installs nothing.
FuguVM plan 006 is the merged, unimplemented adaptation.

This blocks every FuguOracle plan and every FuguPass plan, because each one
needs make regress in the OpenBSD guest. FuguSeed is host-only Perl, so it runs.
