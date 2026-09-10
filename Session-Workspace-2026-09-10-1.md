# Session Workspace 2026-09-10 1

Session: 17f1df87-9b8e-5e04-9a92-051bd03430b8
Project: Workspace
Opened: 2026-09-10T06:41:15Z

## Observations

Claim: the shared perl-release workflow of Tooling publishes the two tarballs,
`SHA256`, and `SHA256.sig` only, so FuguBench DIST-ASSETS-1 needs a Tooling
change for the `fugubench` and `install.sh` assets before the first release.
Evidence: `.github/workflows/perl-release.yml` on Tooling origin/main, the
`files:` list of the "Release to GitHub" step.

Claim: `perl/sync/scripts/dist` hard-codes `MIN_PERL_VERSION => '5.036'`, and
the FuguBench floor is v5.34, so the CPAN tarball of FuguBench would state the
wrong floor. Evidence: line 276 of the script on Tooling origin/main.

Claim: the Workspace `.toolingrc` holds no `wiki.` key, so the checkout walk of
FuguBench CLI-CHECKOUT-2 cannot reach the workspace for the library from a
project clone until `wiki.origin` lands there. Evidence: the Workspace
`.toolingrc` holds `sync.pack org` and `sync.pack infra` only.

Claim: the latest Fugu release 0.4.0 holds every module FuguBench plan 001
needs, and it lacks `Fugu::Curl` and `Fugu::Ed25519`, so the deps verb and the
update verb wait on a Fugu tag after Fugu plans 009 and 010 land. Evidence:
`gh release list -R FuguBSD/Fugu` and the `lib/Fugu/` tree of `v0.4.0`.
