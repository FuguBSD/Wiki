# Session Workspace 2026-09-24 1

Session: be7efb8e-5446-5e0e-b244-291c0a126c38
Project: Workspace
Opened: 2026-09-24T18:01:43Z

## Observations

Claim: wallycore 1.5.3 builds from source in the OpenBSD 7.8 arm64 guest in
about nine minutes, offline, once pkg_add supplies bash, gmake and swig beside
autoconf 2.71, automake 1.16 and libtool.
Evidence: the FuguOracle interop harness, fresh-guest runs of 2026-09-25, and
the workspace scratch logs wally-build4.log and interop-fresh-2.log.

The pip run needs AUTOCONF_VERSION and AUTOMAKE_VERSION in the environment, a
make shim that execs gmake first in PATH, TMPDIR under /home because the root
partition holds 167 MB, and the venv on PATH so the wallycore configure finds
python. The four failures in order: tools/cleanup.sh has a bash shebang (exit
127); BSD make cannot run the automake tree (exit 2); cleanup.sh deletes the
shipped SWIG wrapper, so swig is required after all; the wheel package pulls
packaging, which the upstream pin lacks, and setuptools 82 needs no wheel.

Claim: the upstream blind_pin_server test suite at commit 62b920ef starts its
own Flask app in setUpClass and reads PINSERVER_PORT only, with no URL variable.
Evidence: test/test_pinserver.py of that commit, read during the FuguOracle
plan 005 run.

A runner that targets FuguOracle subclasses PINServerTest, replaces the two
class fixtures, and runs the v2 halves of the mixed tests through their _impl
methods. Record names match upstream (hex sha256 of the public key plus .pin),
so a pins symlink lets the upstream storage assertions read FuguOracle records.
