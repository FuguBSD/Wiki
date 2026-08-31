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

**The CPT rehearsal ran 8 steps over 7009 prompts, with zero dropped rows.**

Evidence: run 33350483695, the cpt log and the run metadata. Tokenization gave
7011 pre-packing rows, which packing then bins. train_runtime 19.8 s, train_loss
5.997, GPU memory peak 29.66 GiB active, no out-of-memory error, and a job of
1 min 58 s. The teacher container was absent before the launch.
Scope: one pass on the H100-1-80G stack of run gh-33342320766. The 8-step bound
comes from `num_epochs: 1` over the packed data, not from a max_steps cap in
train/cpt.yml.
Maps to: FuguTTX TRN-CPT, FuguTTX TRN-EXEC.

**The sft-aug pass trained from the merged CPT base with a qlora adapter, with
no out-of-memory error.**

Evidence: run 33350914761, the sft-aug log. 22222 examples, with 11 dropped
over-length to 22211. 464 steps to epoch 1.991 in train_runtime 813.5 s. The
logged loss moved from 2.881 to 0.0486, at mean train_loss 0.1859. The
device_reserved peak was 39.06 GiB, in the first epoch; the 34.79 GiB summary
value is the final reading, not the peak.
Scope: one pass from /scratch/outputs/cpt-merged, on the stack of run
gh-33342320766. The run never reaches an epoch 2.
Maps to: FuguTTX TRN-AUG, FuguTTX TRN-SFT, FuguTTX TRN-EXEC.
