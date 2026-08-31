# FuguSTX — Live price read before apply

Rehearses: FuguTTX IAC-PREREQ, the shared instructions

**The live price moved away from the recorded table.**

Evidence: 2026-08-28. The price read gave EUR 2.8665 per hour for the
H100-1-80G, against EUR 2.73 in the earlier table, and EUR 1.4699 for the
L40S-1-48G.
Scope: two offers, one region, one day. A recorded price goes stale in weeks, so
only the pre-apply read counts.
Maps to: FuguTTX IAC-PREREQ, the shared instructions.

**The H100-1-80G price held at EUR 2.8665 per hour in fr-par-2.**

Evidence: run gh-33342320766, the price line of the up log. The up job leased 17
hours at this price, and a live `make infra-price` read gave the same number.
Scope: one offer, one region, 2026-08-30. The number equals the 2026-08-28 read,
so the price held for two days.
Maps to: FuguTTX IAC-PREREQ, the shared instructions.
