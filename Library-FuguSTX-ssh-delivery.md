# FuguSTX — Train key over SSH, expiry backstop

Rehearses: the shared instructions

**Root key delivery goes through IAM, not cloud-init.**

Evidence: 2026-08-28, runs 33201226106 and 33202448810. Two probes left root
without the key, and the SSH wait loop burned about 11 minutes of "Permission
denied (publickey)" each time. The key agent of the image writes
`/root/.ssh/authorized_keys` from the registered project keys, and it overrides
cloud-init. The fix registers the campaign IAM SSH key before the boot, and it
grants the pipeline `SSHKeysFullAccess`.
Scope: one GPU OS image family. A cloud-init template fix also needs a fresh
boot, because user_data applies at first boot only.
Maps to: the shared instructions, FuguTTX IAC-TRAIN, FuguTTX D9.

**The runner NAT kills a silent SSH stream at ten minutes.**

Evidence: 2026-08-28, run 33207137344. Dev scoring emits nothing while it runs,
and the stream broke 9m46s after the last output with "client_loop: send
disconnect: Broken pipe". Keepalives fix it, at `ServerAliveInterval=30` and
`ServerAliveCountMax=10`.
Scope: the GitHub runner NAT. A dead scoring session also leaves its container
behind, so every quiet remote step needs both the keepalive and a sweep.
Maps to: the shared instructions, FuguTTX TRN-EXEC.
