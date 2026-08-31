# FuguSTX — CPT and SFT passes end to end

Rehearses: FuguTTX TRN-CPT, FuguTTX TRN-SFT, FuguTTX D4

**Training cost at 0.6B, measured.**

Evidence: 2026-08-28, run gh-33203797910. CPT, one epoch over 7,009 prose
paragraphs: 8 optimizer steps, 18.8 s train time, final loss 5.97, a 2m01s job.
Each SFT pass ran two epochs over 22,123 pairs: 464 steps, about 813 s, 1.48e4
tokens per second per GPU. The final losses were 0.187 for base and 0.185 for
cpt, at 29.66 GiB peak memory. The two SFT wall clocks differ by 0.5 s, so the
cost is deterministic. GGUF conversion took about 90 s, and dev scoring 28 to 31
minutes per model. The full matrix used about 2 h of the 4-hour lease, retries
included.
Scope: Qwen3-0.6B-Base with LoRA on one H100-1-80G. This predicts no 4B number.
Maps to: FuguTTX TRN-CPT, FuguTTX TRN-SFT, FuguTTX TRN-EXEC, FuguTTX D4.

**The CPT pass earns its place.**

Evidence: 2026-08-28. `sft-cpt` beats `sft-base` on every dev metric: ewt LAS
0.7725 against 0.7488, and gum LAS 0.7652 against 0.7555. It also cuts the parse
failures, ewt 80 to 47 and gum 25 to 19.
Scope: one run, dev split, 0.6B, UD r2.18, llama b10666. The comparison isolates
the CPT increment only. No scorecard measures the base model without SFT, so the
SFT gain itself is unmeasured.
Maps to: FuguTTX TRN-CPT, FuguTTX D4.

**A retry of an idempotent verb repairs a runner-side TLS fault.**

Evidence: runs 33350659650 and 33350760205. The first merge-cpt attempt failed
in the deps step on `Unable to establish SSL connection.` from the opentofu
download, 6.5 seconds into the job log, before any Scaleway or instance work.
The identical retry passed, spans 39.9 seconds in the log, and produced
/scratch/outputs/cpt-merged.
Scope: a transient TLS fault between the runner and the GitHub release CDN. The
fault sits outside the verb and the project scripts.
Maps to: FuguTTX TRN-EXEC.
