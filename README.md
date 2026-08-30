# The FuguBSD learning library

This repository is the working record of every campaign of FuguSTX, FuguCTX and
FuguTTX. It holds every observation and every admitted claim.

A project ledger in `spec/LEARNING.md` is the delivered record. It receives one
batch for each campaign, and each batch cites the pages here that hold its
evidence.

## Pages

Pages stay flat, and the page name carries the structure.

| Name                          | Holds                                       |
| ----------------------------- | ------------------------------------------- |
| `Library-<project>-<part>`    | The admitted claims of one pilot component. |
| `Session-<project>-<date>-<n>` | The raw observations of one session.        |
| `Rule-candidates`             | Each candidate rule, with its date.         |
| `Process`                     | The process learnings.                      |

## The content rule

This repository is public. A page must not hold a credential, a bucket suffix, a
Scaleway Project identifier, or an IAM application name. A page can hold a
price, a quota state, an error string and a run identifier.

## How a page changes

`scripts/wiki.pl` of the FuguBSD workspace writes every page. It commits at
capture time and then pushes, so a crash loses nothing. A ruleset forbids a
force push, so the history stays append-only.
