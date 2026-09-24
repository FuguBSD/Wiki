# Session Workspace 2026-09-23 1

Session: eef85c91-68f1-5909-bf65-d8e3d9ae2d61
Project: Workspace
Opened: 2026-09-23T20:25:47Z

## Observations

Claim: the oracle exclusion of FuguPass ORC-CANARY-4 has no owning plan.
Evidence: plan 008 states "Implements: ORC-CANARY without ORC-CANARY-10", and
its Acceptance names ORC-CANARY-10 as the one absent rule. The STATUS row of
ORC-CANARY names a second absent part, "the oracle exclusion of ORC-CANARY-4".
That rule lets a re-run keep a suspect oracle out of the quorum while more than
k oracles are reachable. The body of plan 008 designs no such option. A grep of
plans/ for ORC-CANARY finds plan 008 and plan 013 alone, and plan 013 holds the
documentation rule ORC-CANARY-10.
This session keeps the STATUS sentence about the exclusion, and it reports the
gap to the operator. A takeover must not re-plan.

Claim: FuguPass plan 008 asks for one read more than ORC-ENROLL-8 requires.
Evidence: the plan states "The change reads both passphrases twice". ORC-ENROLL-8
requires the double read of the new passphrase alone, and it verifies the old
passphrase against the canary record of each live oracle. ORC-ENROLL-10 requires
a resumed change to read "both passphrases again", which is one read of each.
The canary verification is the stronger check, so the second read of the old
passphrase adds a prompt and proves nothing.
The design of record is the specification, because a plan holds steps and the
specification holds the design. Package 1 followed the plan, so this session
sends a fixer to follow ORC-ENROLL-8 instead.

Claim: ORC-ENROLL states no rule for a change that starts below k reachable
oracles.
Evidence: ORC-ENROLL-11 covers the mid-run case, "With a live oracle
unreachable, the change stays incomplete, and the marker stays". A change that
starts below k can reconstruct no K_e, by ORC-QUORUM-6, so it stops before its
first set_pin and before the marker exists. Package 1 stops with the per-oracle
state report and writes no marker. The last package of this plan states that
case in ORC-ENROLL-11, because the code and the specification must agree in the
same change.

Claim: leg 8 of the FuguPass harness fails four rotation assertions at random.
Evidence: the run of 2026-09-23 at commit 8b4148a reported "not ok 28" to
"not ok 31" of tests/harness.d/8-session.pl, the rotation of add, because
"show a1" gave an empty standard output. The same commit passed those four on
the re-run, and the parent commit 3f83219 passed them as well. Three runs of
this session hold two passes and one failure of one leg. The console read of
the guest is the suspect, because no code of this branch reaches a subcommand
of the tool yet.
A run that fails leg 8 alone needs a second run before anyone calls it a
defect of the change. A run that fails leg 8 twice is a defect.

Claim: a revocation kit of FuguPass is derived data, so no ceremony must export
one.
Evidence: ORC-REVOKE-6 states that a kit holds the machine name and, for each
oracle, that oracle's record file names. A record name is the hash of the
record's compressed public key. KEY-CLIENT-1 derives the client key from the
device factor X, and KEY-CLIENT-4 forbids a dependency on the passphrase.
KEY-DEVICE-2 keeps X on disk in the machine-local set. So a machine derives its
own kit with no plate, no passphrase, no quorum and no network. KEY-DEVICE-4 and
ORC-REVOKE-4 regenerate X and the client keys of a lost machine from the plate.
An exported file is therefore a cache of derived data, and it goes stale in
silence. A refill adds slots, and the kit of the last ceremony lists none of
their records. The owner then revokes the listed records, the tool reports
success, and the new records still answer at every oracle. A missing kit fails
loudly, and a stale one does not.
The operator authorized the design on 2026-09-24. The kit becomes the output of
one command, CER-CREATE-9 and CER-PROVISION-9 go, and ORC-REVOKE-6 states the
production instead of the export.

Claim: a mutation run finds a test leg that hangs in place of a failure.
Evidence: the FuguPass plan 008 run proved each new harness leg with the
mutation it targets. Leg 7, the refill that must refuse while the change marker
exists, held no answer for the passphrase prompt. Against the correct code the
refusal came first, so the leg passed and the prompt never appeared. The
mutation that removed the marker gate let the refill start, and the leg then
waited on the console for the timeout of 24 minutes. It reported a timeout, and
no assertion named the cause. One answer in the step fixed it, and the same
mutation then failed six assertions in seconds.
A leg that passes for the wrong reason can also fail for the wrong reason. The
mutation run is the only way to see either, and a timeout is the signature of a
leg that cannot express its own failure.

Closed: 2026-09-24T08:39:51Z
