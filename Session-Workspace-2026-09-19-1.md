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

Claim: an OpenBSD 7.8 arm64 guest now builds and runs on this host, and three
environment defects stood between fuguvm up and a working guest.

Evidence, in the order they appeared:
1. Fugu commit aa1dba7 gave the three signers one shape, and FuguVM still drove
   the old positional shape. FuguVM 15f6468 fixes it.
2. The QEMU control socket path reached 133 bytes, and a unix socket path must
   stay under 104. A worktree path is long, so the project .fuguvmrc needs a
   short state_dir. state_dir reads from the project file only: the global
   ~/.fuguvmrc does not supply it.
3. QEMU with the hvf accelerator limits addressing to 32 bits, so a memory value
   of 4096 fails and 3072 works on this host.

fuguvm ssh authenticates through the SSH agent alone, and the Bitwarden agent
holds no identity while the vault is locked. A throwaway key in a dedicated
ssh-agent serves the local guest, and it keeps the operator key out of the run.

Claim: FuguVM plan 006 named a false remedy, and the implementation departed
from it. The plan moved the manifest proof to the perl engine of Fugu::Signify.
A release of OpenBSD publishes SHA256.sig in the embedded form: the 7.8 amd64
file holds 27 lines, which are the two signature lines and the whole 25-line
manifest. The perl engine reads a two-line file alone, so it refuses every real
release. Every fixture in the repository used the detached form, so the gates
would have stayed green while fuguvm up verified nothing.

Claim: PKG-SECP-1 of FuguOracle names a closed list of modules, and upstream
added one that the list does not name.

Evidence: libsecp256k1 v0.8.0 ships a silentpayments module, and it builds by
default. PKG-SECP-1 says the port must enable ecdh, recovery and extrakeys, and
must not enable schnorrsig, ellswift or musig. It names no rule for a module
that upstream adds later. The guest build of plan 001 passes
--disable-module-silentpayments, so the built surface matches the intent.
Plan 007 must add the module name, or state the rule as an exhaustive list.

Claim: the v0.8.0 release tarball carries no configure script, and the guest
needs autotools once to build the library.

Evidence: the tarball is a git archive. regress/guest installs autoconf 2.71,
automake 1.16 and libtool, then runs ./autogen.sh. That step needs the guest
package mirror. make regress itself needs no network, so TEST-KAT-2 holds.

Claim: the guest root partition holds 167 MB, and a build under /root fills it.
Evidence: the first guest build failed there. regress/guest builds under
/home/fuguoracle instead.

Claim: a mutation test inside the guest can give a false pass, because a copied
*.d dependency file holds an absolute path. Evidence: a first mutation round
gave five false passes, because make rebuilt the original cipher.c from the
source directory. make clean in the copy fixed it.
