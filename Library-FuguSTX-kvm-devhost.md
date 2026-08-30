# FuguSTX — KVM test and dev host selection

Rehearses: FuguTTX IAC-METAL, FuguTTX IAC-DEV, FuguTTX D9

**Nested KVM works on the POP2 range.**

Evidence: 2026-08-26. Both checks passed on a POP2-2C-8G virtual instance in
`fr-par-2`. `/dev/kvm` exists, with vmx on each vCPU. `fuguvm up` installed and
booted one OpenBSD 7.8 guest in 5.5 minutes, and guest SSH answered. The dev
host is an ephemeral virtual instance.
Scope: this proves nested KVM on the POP2 range today. It proves nothing about
another instance range, and it is not a platform guarantee.
Maps to: FuguTTX IAC-METAL, FuguTTX IAC-DEV, FuguTTX D9.
