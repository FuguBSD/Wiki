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

Claim: FuguOracle holds 1,597 lines of C outside the comments and the blank
lines, against the 1,500-line target of ARCH-LAYOUT-4.
Evidence: a Perl strip of every `/* */` and `//` comment over cipher.c 509,
http.c 247, keygen.c 85, main.c 170, oracle.c 229 and pindb.c 357. The six files
hold 2,969 raw lines with the headers. The rule does not say what it counts, so
the raw reading misses the target by 98 percent and the code reading by 6.

Claim: an implementer reported 1,271 lines for the same tree, and the number was
wrong by 326 lines.
Evidence: the two measurements ran on one commit, 02e52c3. Measure a number of a
report before it enters a register note or a commit message.
