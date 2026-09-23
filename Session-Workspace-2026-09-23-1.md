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
