# FuguSTX — Budget ownership in the shared Organization

Rehearses: FuguTTX IAC-PREREQ, the shared instructions

**A budget is a resource of the Organization, not of a Project.**

Evidence: 2026-08-26. `scaleway_billing_budget` applies at the Organization.
Every FuguBSD project shares one Organization, so a second project's persistent
stack collides on the same budget.
Scope: one Organization. The organization must decide budget ownership before a
second apply.
Maps to: FuguTTX IAC-PREREQ, the shared instructions.

**The forecast gate works at the live price.**

Evidence: 2026-08-28, run gh-33203797910. The gate printed "forecast: go: EUR
1.23 consumed plus EUR 11.47 forecast stays under the EUR 300.00 budget" before
the good boot, at EUR 2.8665 per hour. Boot friction cost about EUR 3, and the
whole campaign cost about EUR 21.
Scope: one campaign, one budget.
Maps to: FuguTTX IAC-PREREQ, the shared instructions.
