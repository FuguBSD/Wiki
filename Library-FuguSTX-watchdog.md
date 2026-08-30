# FuguSTX — Watchdog, heartbeat, claim protocol

Rehearses: the shared instructions

**The conditional write holds on this platform.**

Evidence: 2026-08-28. A probe wrote one object with `If-None-Match: *`. The
first PUT gave 200, and the second gave 412. The delete gave 204, and a read
after it gave 404.
Scope: one bucket, one day. The claim protocol of the shared instructions works
here.
Maps to: the shared instructions, FuguTTX TRN-EXEC.

**The heartbeat and the claim survive a dropped session.**

Evidence: 2026-08-28. Each remote step starts its own idempotent writer. After
the broken pipe the claim held, and the retry ran on the same stack three
minutes later. The watchdog never threatened the stack.
Scope: one dropped session, one campaign.
Maps to: the shared instructions.

**GitHub cron is best effort, measured.**

Evidence: 2026-08-29, run 33227369925. The watchdog declares a 30-minute cron.
Between 18:00 and 02:00 UTC only 2 of 16 slots fired, 7 and 18 minutes late. The
expired stack lived 2 h 23 m past its expiry tag, at about EUR 6.8 of idle H100,
before the reap. The tag backstop carried the guarantee.
Scope: sixteen slots of one night, in one repository. The skip rate can differ
elsewhere. Dispatch a teardown at the end of the work.
Maps to: the shared instructions, FuguTTX IAC-TRAIN.
