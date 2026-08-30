# FuguSTX — Corpus lanes and bucket policies

Rehearses: FuguTTX IAC-PERSIST, FuguTTX D6

**A bucket policy is an allow list, and the applying principal must name itself.**

Evidence: 2026-08-26. The four buckets applied with the multipart lifecycle
rule. An unnamed principal cuts its own key off from the state bucket.
Scope: one bucket set, one provider version.
Maps to: FuguTTX IAC-PERSIST, FuguTTX D6.

**A per-bucket setting needs a per-bucket check.**

Evidence: 2026-08-26. The versioning setting differs per bucket, and the
checkpoint bucket keeps it off. `tofu validate` reads no value, so it catches
nothing here.
Scope: one bucket set.
Maps to: FuguTTX IAC-PERSIST, FuguTTX D6.

**S3 suspends versioning, and it never removes versioning.**

Evidence: 2026-08-26. A bucket that starts with versioning on can never return
to the unversioned state, so the correct setting must land at creation.
Scope: the S3 API of this platform.
Maps to: FuguTTX IAC-PERSIST, FuguTTX D6.

**The lanes and the pairs are in the buckets.**

Evidence: 2026-08-28. The upload wrote 32,516 training-lane records, 22,123 SFT
pairs, and 7,009 prose paragraphs to the corpus bucket. The eval lane holds
4,310 sentences. The keys are flat, and a manifest records the `r2.18` tag.
Scope: one corpus release.
Maps to: FuguTTX IAC-PERSIST, FuguTTX D6.
