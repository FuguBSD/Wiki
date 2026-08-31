# FuguSTX — Promotion and the artifacts scorecard

Rehearses: FuguTTX D5, FuguTTX TRN-EXEC

**A promote must not need the instance.**

Evidence: 2026-08-29, run 33240656338. The scratch volume dies with the stack,
and an SSH promote path dies with it. The GGUF survives, because the gguf step
uploads it to the checkpoint bucket at once. The promote of `sft-cpt` ran
stackless, and the scorecard hash matched.
Scope: one campaign, one artifact.
Maps to: FuguTTX TRN-EXEC, FuguTTX D5.

**The tier T1 baseline ran on free CPU shards.**

Evidence: 2026-08-29, run 33241110946. Twelve CI shards swept the
4,310-sentence eval lane in 71 minutes wall clock. Each shard ran 36 to 71
minutes on four threads, with no GPU and no instance. The aggregate scorecard:
ewt LAS 0.7719, UPOS 0.9354, lemma 0.9509, 46 failures; gum LAS 0.7647, UPOS
0.9310, lemma 0.9492, 24 failures; pud LAS 0.7817, UPOS 0.9515, lemma 0.9613, 6
failures. The ewt and gum eval scores sit within 0.001 of the dev scores, so the
dev split predicted the eval lane on the shared treebanks.
Scope: one model, 0.6B at Q8_0, UD r2.18, llama b10666, greedy CPU decoding. The
pud treebank has no dev split.
Maps to: FuguTTX D2, FuguTTX D5, the FuguTTX inference specification.

**The sft-aug Q8_0 artifact scored ewt LAS 0.7604 and gum LAS 0.7621 on the dev
split.**

Evidence: runs 33351940505 (gguf) and 33352183041 (score). ewt: LAS 0.7604,
lemma 0.9411, upos 0.9267 over 2001 sentences with 76 failures. gum: LAS 0.7621,
lemma 0.9562, upos 0.9374 over 1383 sentences with 22 failures. The counts agree
with the ratios: 19122 of 25148, and 18795 of 24661. The export gave a Q8_0
artifact of 633.5 MB with 310 tensors, model hash
8aa0b9340f979dca1f1cdd09da62c6d10a07ad7996c919e5e5c8a694ec5582be.
Scope: dev split, llama.cpp b10666, GPU decoding. Every artifact key carries the
stack-up run identifier gh-33342320766, because the instance boot environment
fixes STX_RUN_ID at up.
Maps to: FuguTTX D5, FuguTTX IAC-DURA.
