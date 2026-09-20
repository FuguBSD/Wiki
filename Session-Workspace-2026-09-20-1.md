# Session Workspace 2026-09-20 1

Session: 587f573d-9a39-5cda-b9ea-bb2549afb66a
Project: Workspace
Opened: 2026-09-20T10:31:19Z

## Observations

Claim: on OpenBSD 7.8 arm64, file(1) cannot prove a static link.
Evidence: `file` prints "ELF 64-bit LSB shared object, AArch64, version 1" for
the static `fuguoracle` and for `/bin/ls` alike. `/bin/ls` is a bad control,
because the OpenBSD base programs link static too. `readelf -l` shows no INTERP
segment in either one.

Claim: ldd(1) discriminates a static program from a dynamic one on that guest.
Evidence: `ldd fuguoracle` lists one `dlib` line and no `rlib` line.
`ldd /usr/bin/ssh` lists an `exe` line and one `rlib` line for each shared
library. A test of a static link must read `ldd`, never `file`.
