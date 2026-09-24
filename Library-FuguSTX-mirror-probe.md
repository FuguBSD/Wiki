# FuguSTX — Generator memorization and the mirror method

Rehearses: FuguTTX TRN-AUG, FuguTTX D4

**A famous OpenBSD 3.0 page sits in the training set of the generator, and an
early-deleted page does not.**

Evidence: the synthetic mirroring probe of 2026-09-24, run in a session with no
instance. Each model wrote a page from memory, with no input. Two scores compare
the candidate against the original: the word-sequence ratio, then the 8-gram
containment. `claude-opus-5`: strlcpy.3 0.99 and 0.97, lam.1 0.90 and 0.74, and
cat.1 0.58 and 0.54. Then skeyaudit.1 0.51 and 0.15, pfctl.8 0.38 and 0.34, and
photurisd.8 0.12 and 0.02. `claude-fable-5-1`: pfctl.8 0.39 and 0.21, and
photurisd.8 0.14 and 0.02. OpenBSD deleted photurisd.8 in 2003, and the page
sits in the Attic.
Scope: six pages of OpenBSD 3.0, two models, one day. The word-sequence ratio is
difflib SequenceMatcher on the `mandoc -T ascii` rendering of each page. Each
page comes from `https://cvsweb.openbsd.org/checkout/src/<path>?rev=OPENBSD_3_0`.
The scripts sit in the worktree `bridge-cse_01KmRHSWLANTZzDoncoqHoXC` under
`Projects/FuguSTX/scratch/mirror/`: fetch.sh, skeleton.sh, compare.py, score.sh,
tells.py, NOTES.md, and tasks/.
Maps to: FuguTTX TRN-AUG.

**A mirror from a mechanical mdoc skeleton plus the source rebuilds a memorized
page.**

Evidence: method C of the same probe, with `claude-opus-5`. The skeleton of
skeleton.sh plus the source gave cat.1 0.93 and 0.72, against 0.58 and 0.54 from
memory alone. The other pages: skeyaudit.1 0.57 and 0.14, pfctl.8 0.45 and 0.08,
and photurisd.8 0.27 and 0.02. A source-only mirror, method B, gave cat.1 0.43
and 0.24, skeyaudit.1 0.28 and 0.02, pfctl.8 0.20 and 0.02, and photurisd.8 0.11
and 0.00.
Scope: four pages, one model. The skeleton gives a memorized page back, so a
memorization check must precede pairing.
Maps to: FuguTTX TRN-AUG, FuguTTX D4.

**A mirror from the source differs from the human page in completeness,
rationale sentences, and invented facts.**

Evidence: methods A and B of the same probe. In method A, a Sonnet model wrote a
brief from the original, and `claude-opus-5` wrote the page from the brief plus
the source. The scores: cat.1 0.40 and 0.10, skeyaudit.1 0.45 and 0.06, pfctl.8
0.40 and 0.06, and photurisd.8 0.35 and 0.01. The brief held 0.00 of the 8-grams
of the original on each of the four pages. The cat.1 brief stated six flags, and
the source has seven. In method B, the source-only mirror ran at 1.1 to 2.5
times the original length, with added sections and an invented `/etc/skey` path.
Scope: four pages, one generator, and one brief writer. The path of skeyaudit.1
is `/etc/skeykeys`.
Maps to: FuguTTX TRN-AUG.

**Surface markers do not separate a human page from its mirror.**

Evidence: tells.py of the same probe, on sentence length, hedge words, and
section counts, over the originals and the candidates. No marker split the human
pages from the mirrors.
Scope: three markers, six pages, one probe. Completeness, rationale sentences,
and invented facts are the differences that a reader sees.
Maps to: FuguTTX TRN-AUG.
