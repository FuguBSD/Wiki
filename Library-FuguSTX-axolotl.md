# FuguSTX — Axolotl in Docker on the GPU OS image

Rehearses: FuguTTX TRN-EXEC, FuguTTX D3

**Axolotl in Docker works on the GPU OS image.**

Evidence: 2026-08-28, run gh-33203797910. Cloud-init pre-pulls the Axolotl image
during the SSH wait. The AWS bundled installer supplies awscli, which Ubuntu
Noble does not package. A clean CPT run doubles as the cloud-init probe.
Scope: one image tag, `main-20260827-py3.12-cu130-2.12.1`, on one image family.
Maps to: FuguTTX TRN-EXEC, FuguTTX D3, FuguTTX IAC-DURA.
