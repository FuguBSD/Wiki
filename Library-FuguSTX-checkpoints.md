# FuguSTX — Checkpoint sync per epoch

Rehearses: FuguTTX IAC-DURA, FuguTTX TRN-EXEC

**Each pass synced its checkpoints to Object Storage.**

Evidence: 2026-08-28, run gh-33203797910. Each SFT pass wrote checkpoint-232 and
checkpoint-464, and the bucket listing confirms both.
Scope: one campaign, one model size.
Maps to: FuguTTX IAC-DURA, FuguTTX TRN-EXEC.
