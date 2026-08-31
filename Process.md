# Process

The process learnings of the campaigns. A claim about how the work runs belongs
here. A claim about a platform belongs on a `Library-` page.

**A fix that matches the symptom is not a fix.**

Evidence: run 33201226106 and run 33202448810 of FuguSTX. A cloud-init `users:`
block matched the symptom of the key failure, and the error came back unchanged.
The mechanism was the key agent of the image, which overrides cloud-init.
Scope: one image family, one campaign.
Maps to: FuguTTX D9.

**A recorded price goes stale in weeks.**

Evidence: the FuguSTX price read of 2026-08-28 gave EUR 2.8665 per hour against
EUR 2.73 in the earlier table.
Scope: one offer, one region. Only the pre-apply read counts.
Maps to: FuguTTX IAC-PREREQ.

**A cheap gate catches an expensive failure.**

Evidence: the first tier T1 sweep of FuguSTX failed on all twelve shards in
under a minute at zero GPU cost (run 33240741185).
Scope: a CPU sweep before a GPU run.
Maps to: FuguTTX D2.

**Every numbered claim met a verifier before the library.**

Evidence: the FuguSTX P4 teacher campaign, run gh-33342320766, session page
2026-08-30-3. The observer, operator, and verifier loop held for 22 claims. The
verifier refuted one full claim and two claim parts, all operator-reported
numbers or mechanisms, and only the corrections moved. Three more claims moved
with minor corrections.
Scope: one campaign, 22 claims, 21 admitted entries.
