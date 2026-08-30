# FuguSTX — Train stack up and down, teardown completeness

Rehearses: FuguTTX IAC-TRAIN, the shared instructions

**The GPU OS image needs the SBS variant.**

Evidence: 2026-08-28, run 33200578308. The default marketplace image resolves an
l_ssd snapshot, and a server create on an SBS root volume fails with "requested
volume type does not match the snapshot type, use 'l_ssd' instead".
`image_type = "instance_sbs"` on the data source fixes it.
Scope: label `ubuntu_noble_gpu_os_13_nvidia`, fr-par-2, provider v2.81.0.
Maps to: FuguTTX IAC-TRAIN.

**An SBS root volume needs a Block Storage permission.**

Evidence: 2026-08-28, run 33200948479. `InstancesFullAccess` alone gave
"insufficient permissions: write volume" at server create. The pipeline policy
needs `BlockStorageFullAccess`.
Scope: one policy set, one volume type.
Maps to: the shared instructions, FuguTTX D9.

**A correct boot is fast, and a teardown is faster.**

Evidence: 2026-08-28, run gh-33203797910. The good `up` ran 66 s end to end:
server create 20 s, boot to SSH about 30 s, then the key mint and delivery. Each
teardown ran 30 to 47 s, with a constant 18 s server destroy, the key delete,
and the claim release.
Scope: one offer, one image, one region. The lease clock starts at dispatch, not
at boot.
Maps to: FuguTTX IAC-TRAIN.

**A failed apply leaks adoptable state.**

Evidence: 2026-08-28. Attempt 1 created the IP and the scratch volume before the
server create failed. Attempt 2 adopted and retagged both, at "Plan: 1 to add, 2
to change, 0 to destroy".
Scope: one stack. A half-up stack bills until a teardown dispatch, and the
watchdog is not the cleanup path.
Maps to: the shared instructions, FuguTTX IAC-TRAIN.

**Teardown completeness holds under the watchdog.**

Evidence: 2026-08-29. The reap destroyed all four resources, the server, the
scratch volume, the IAM key and the IP, in 39 s. It deleted the train keys,
released the claim, and printed "the destroy is confirmed: no train server
remains".
Scope: one reap, one stack.
Maps to: FuguTTX IAC-TRAIN, the shared instructions.
