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
