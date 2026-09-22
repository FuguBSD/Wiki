# Session Workspace 2026-09-21 3

Session: 85c63c8c-1326-444c-b94e-216955a9a178
Project: Workspace
Opened: 2026-09-21T13:01:03Z

## Observations

Claim: `fuguvm ssh` allocates no pty, so a `readpassphrase(3)` prompt cannot
run over it. The command runs through libssh2 `channel->exec`. FuguPass
TEST-HARNESS-8 settles the design: `fuguvm expect` runs a caller script
against the serial console of the guest.
Evidence: `Fugu::SSH::run_command` in Projects/Fugu, and the FuguPass leg
`tests/harness.d/7-create.pl`, which passes with 33 assertions.

Claim: the console root password of a FuguVM guest is a random 32-character
value. `App::FuguVM::Guest::_root_password` makes it, and it writes it into
the guest state file. `fuguvm` exposes it through no command and no status
key, so a caller reads the `root_password` key of that file.
Evidence: the state file of the guest of this run, and the FuguPass harness
variable `FUGUPASS_CONSOLE_PASSWORD`.

Claim: an unveil violation on OpenBSD 7.8 gives `ENOENT` for a path outside
the list, and `EACCES` for an unveiled path used beyond its permission. It
never gives `EPERM`.
Evidence: `src/regress/sandbox` of FuguPass, measured in the arm64 guest.
FuguPass plan 006 claimed `EPERM`, and the measurement corrected it.

Claim: `unveil(2)` state survives `execve(2)` on OpenBSD 7.8 only when the
process gives `pledge(2)` a non-NULL `execpromises`. With `execpromises` NULL,
the child starts with a view of the whole filesystem. `unveil(NULL, NULL)`
makes no difference to this: the lock disables a later `unveil()` call, and it
does not change what an `execve` child inherits. A `fork(2)` child inherits the
unveil either way.
Evidence: eleven probe runs in the arm64 guest, 2026-09-22. The same binary
and the same unveil list, with only `execpromises` changed: with
`pledge("stdio rpath exec proc", "stdio rpath")` the child cannot read the path
outside the list, and with `pledge("stdio rpath exec proc", NULL)` it reads it.
`man 2 unveil` of OpenBSD 7.8 states nothing about `fork` or `execve`, so the
page can neither support nor refute the claim; only a probe settles it.
Admitted: never record the short form "unveil does not survive execve". The
short form is the trap that produced the first, wrong conclusion of this run,
which was that a rule deriving paths for a child process protects nothing.

Claim: `poll(2)` on an OpenBSD `video(4)` descriptor starts the read stream by
itself. `videokqfilter()` of `sys/dev/video.c` rev 1.61 holds the same
`sc_vidmode == VIDMODE_NONE` guard and `hw_if->start_read()` call as
`videoread()`, and `poll(2)` reaches a character device through the kqueue
filter, because `struct cdevsw` carries no `d_poll` member. So a wait before
the first read is correct, and it does not stall a working camera.
Evidence: the source tagged `OPENBSD_7_8`, read by three agents of the
FuguPass plan 012 run, 2026-09-22. `docs/analysis/video-read-stream.md` of
FuguPass records it.

Claim: `videoread()` of `video(4)` reads no bit of its `ioflag` and sleeps
`INFSLP`, so `O_NONBLOCK` never gives `EAGAIN` on such a descriptor. A design
of "read, then wait after EAGAIN" therefore degenerates to an unbounded read.
Evidence: the same source. The main session ordered that design as a remedy,
and the fixer refused it with this reading. Two reviewers then confirmed it.

Claim: `kern.video.record` is 0 on a fresh OpenBSD, and the `video(4)` driver
blanks the image data of every reader while it is 0. No capture works until the
superuser sets it to 1.
Evidence: measured in the arm64 guest, 2026-09-22. It is now FuguPass
PROG-SCAN-11.

Claim: `setrlimit(2)` needs the `proc` promise. A child that the core execs
under an `execpromises` set without `proc` dies with `SIGABRT` at that call.
Evidence: both FuguPass helpers died at their first syscall in the guest,
2026-09-22. Each helper now reads the limit with `getrlimit(2)` and writes it
only when it is not zero.

Claim: the OpenBSD port `graphics/libquirc` cannot install on a base system. It
declares `sdl-gfx`, SDL and the X11 shared libraries as run dependencies, for
its demonstration programs, and `pkg_add -I -n -v libquirc` answers `library
X11.19.0 not found`. The library itself needs the standard C functions alone.
Evidence: measured in the guest, 2026-09-22. FuguPass vendors quirc v1.2 in
tree instead, because a port would pull SDL and X11 onto a signing machine.

Claim: `SSH_AUTH_SOCK` that points at the fuguvm agent breaks `git commit`.
That agent holds no signing key, so the commit fails with `Couldn't find key in
agent?`. Commit first, then export the harness environment in a subshell.
Evidence: repeated in three agents of this run.
