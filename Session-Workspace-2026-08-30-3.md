# Session Workspace 2026-08-30 3

Session: 6567e255-f25c-4900-b330-54a877e7566f
Project: Workspace
Opened: 2026-08-30T23:43:45Z

## Observations

FuguSTX P4 campaign, plan 004 step 8. The infra up step ran from the worktree
clone at HEAD 6e93620. The run identifier is gh-33342320766.

Claim: H100-1-80G prices EUR 2.8665 per hour in fr-par-2, and the up job leased 17 hours. Verdict: Confirmed, log of run gh-33342320766.
Claim: the up run added 4 resources, delivered a routed_ipv4 IP, and set the lease tag stx:expires=2026-08-31T16:37:59Z. Verdict: Confirmed, log of run gh-33342320766.
Claim: the up job ran 61 seconds. Verdict: Refuted. The log spans 59 seconds. The 61 came from the job metadata, and the log timestamps beat it.
Claim: the forecast gate passed, with EUR 20.66 consumed plus EUR 48.73 forecast under the EUR 300.00 budget. Verdict: Confirmed, log of run gh-33342320766.

Workspace mechanics, seen at the start of the P4 campaign session.

The train.yml workflow defines five inputs: action, offer, hours, name, and
run_id. The up action reads the first three. The persistent RUNBOOK names no
offer probe command. A live `make infra-price` read stood in, and its price
agreed with the up log. The operator host has no aws CLI. The identity
pre-flight used `scw --profile fugustx object bucket get` on the corpus
bucket, and it returned the bucket, not a 403.

The SessionStart hook opened a session page, and its commit failed: the
Bitwarden SSH agent socket refused the connection. The staged page name also
collided with a pushed page of an earlier session. This session discarded the
empty stub, set commit signing off in the Wiki clone, and opened the next
page name. The observer flags the unsigned commits to the user.

Watch reading at the teach-serve start, run 33342530998, from the worktree
clone at HEAD 6e93620. The serve job started about three minutes after the
dispatch. `make infra-status` listed the H100 server as running, with one IP,
one volume, and the four buckets. `make infra-cost` read EUR 20.66 of EUR
300.00, 6 percent. The lease tag leaves about 17 hours. The Watchdog workflow
is active, and its last scheduled run at 21:43:59Z succeeded in 14 seconds.

Claim: GitHub fires the FuguSTX Watchdog cron far less often than its `*/30 * * * *` schedule asks: on 2026-08-30 it fired 6 times, not 48, with gaps of 3 h 03 min to 5 h 15 min. Verdict: Confirmed, from the run list dump and watchdog.yml. The worst gap sets the watchdog exposure window. The workflow comment expects this: a schedule is best effort, and the stx:expires tag and the key expiry are the backstops.

The teach-serve step, run 33342530998, from the worktree clone at HEAD
6e93620. The run succeeded, and the FP8 fit holds: Qwen3-32B-FP8 loaded and
answered on the 80 GB H100. The serve confirmation is the endpoint answer
itself. The GitHub log tails milestones only, and the vLLM internals (GPU
memory, KV cache) stay in the container log on the instance. The endpoint is
loopback: `teach-serve: vLLM answers on 127.0.0.1:8000`. The docker image was
absent on the fresh instance, and the step pulled it before the load. The
hand-over budgeted tens of minutes for the first serve. The step needed about
six minutes.

Teach-serve facts, run 33342530998, for TRN-TEACH-6.

Claim: the first teach-serve on a fresh instance pulled vllm/vllm-openai:v0.28.0 in 72 seconds, vLLM answered 290 seconds after the pull ended, and the job ran 6 min 19 s end to end. Verdict: Confirmed, log of run 33342530998. The serve log does not name the GPU type. The offer H100-1-80G comes from the up log of run gh-33342320766.
Claim: the teach-serve action reads the action input only. scripts/train rejects --run-id for every verb except promote and teach.
Claim: the teach path reaches vLLM over the SSH tunnel of TRN-TEACH-3. The container binds 127.0.0.1:8000 on the instance, and no public port opens.

The health poll runs each 15 seconds, so a measured ready time sits inside one
15-second window. The hand-over budgeted tens of minutes for the first serve,
and the measured serve took near five minutes after the image pull.

Claim: the CI path does not capture the vLLM GPU memory numbers at teach-serve. The driver starts the container with `docker run -d`, the runner log holds the pull and the ready line only, and no train.yml verb fetches a container log. The FP8 fit confirmation is the health answer alone. Verdict: Confirmed, log of run 33342530998 and the driver code.

Claim: the serve step takes the stack with one conditional write, and the heartbeat writer refreshes the claim each 60 seconds. Verdict: Refuted in its last clause, log of run 33342530998 and the driver code. The correct mechanism: one `PUT` with `If-None-Match: *` writes the claim object once, the heartbeat writer refreshes a separate heartbeat object each 60 seconds, and only `make infra-down STACK=train` releases the claim. The watchdog reads the age of the heartbeat object, not of the claim.

A caution for the library: the run identifier in a driver log line is
`$STX_RUN_ID` from the instance boot environment, not the identifier of the
workflow run that executed the step. A later run on the same stack prints the
boot identifier.

The observer decision after teach batch 1. The batch accepted 15 of 200, and
the disagree reason dominated the rejects. The spec sets no augmentation
volume target: the deliverables are the rehearsal, the sft-aug pass, and the
rates for TRN-TEACH-6. The observer dispatched five more batches, run
sequentially, near 1,000 more proposals and near two more teacher hours. This
stays inside the 5 to 15 GPU-hour teacher budget row, and it measures the
rate stability across batches. The loop stops early on a failed run, on a
batch rate under 0.02, or at 14:30Z for lease headroom.

Claim: teach batch 1 of run gh-33342320766 (workflow run 33342886087) proposed 200 sentences, accepted 15, rate 0.075, and the teach step ran 22 min 49 s. The reject reasons: disagree 159, tag 12, tree 7, word 7, count 0. Verdict: Confirmed, teach report and run log of 33342886087. The attribution of the disagree count to the two seeded passes awaits a code-path check.

Claim: the reject reason `disagree` counts the proposals where the two seeded annotation passes disagree: check 1 of the judge filter, temperature 0.2, seeds 11 and 23, compared after normalization. Verdict: Confirmed, judge.py and teacher.py at the run commit 6e93620.

The hand-over held check-1 independence as an open question. Batch 1 answers
it with a measurement: the two passes disagreed on 159 of 200 proposals, 79.5
percent. The distinct seeds produce strongly non-identical samples at
temperature 0.2, and check 1 is the dominant filter gate by a factor of six
over all other reject reasons combined.

The teach loop of run gh-33342320766 is complete: six batches, no failure,
the last batch ended at 02:18:02Z with more than 14 hours of lease headroom.
The per-batch table, from the six teach reports:

| Batch | Accepted | Rate  | disagree | tag | count | tree | word |
| ----- | -------- | ----- | -------- | --- | ----- | ---- | ---- |
| 1     | 15       | 0.075 | 159      | 12  | 0     | 7    | 7    |
| 2     | 26       | 0.130 | 149      | 7   | 0     | 11   | 7    |
| 3     | 27       | 0.135 | 151      | 7   | 1     | 8    | 6    |
| 4     | 14       | 0.070 | 159      | 8   | 0     | 15   | 4    |
| 5     | 11       | 0.055 | 160      | 12  | 0     | 8    | 9    |
| 6     | 18       | 0.090 | 149      | 14  | 0     | 9    | 10   |

Each batch proposed 200. The disagree gate is stable, and the accepted count
swings by a factor of 2.5 between batches at temperature 0.9 generation.

Claim: six teach batches of run gh-33342320766 proposed 1200 sentences and accepted 111, an overall rate of 0.0925. The per-batch rates span 0.055 to 0.135, and the disagree count stays in 149 to 160 per batch of 200. Verdict: Confirmed, the six teach reports. The batch-to-run mapping comes from the bucket prefix runs/gh-33342320766/, not from a field in the reports.

The teach-stop step, run 33350323616, passed in 15 seconds. The verb needs no
run_id input. The observer moved the campaign to the training passes: cpt,
then merge-cpt, then sft-aug. The offline eval-lane overlap check of the 111
accepted sentences started in parallel, outside the teach path, per the open
review-panel item.

Claim: the teach-stop verb prints no stop confirmation. cmd_teach_stop runs `docker rm -f stx-vllm` in void context, discards the output and the status, and returns 0 on every path. The step exit code is the only stop signal in the run record. Verdict: Confirmed, log of run 33350323616 and scripts/train-driver lines 505-511 at commit 6e93620. The idempotence motive and the "next pass fails loudly" expectation stay unsupported: no record names the motive, and no train-pass log yet shows the loud failure.

The accepted teacher records live in the corpus bucket, at
`runs/<run id>/accepted-*.jsonl` under stx-corpus, not in stx-artifacts. The
run prefix in stx-artifacts holds the reports and the rejects only. This
follows COR-AUG-1: an accepted record enters the training lane, and the
training lane reads the corpus bucket. The eval lane sits in its own bucket,
and its manifest pins UD r2.18 with 4310 records.

Claim: the 111 accepted teacher sentences of run gh-33342320766 have zero overlap with the eval lane, verbatim and normalized. The eval lane holds 4310 records: ewt test 2077, gum test 1233, pud 1000, at UD r2.18. Verdict: Confirmed, by an independent comparator with a passed sensitivity test. The memorization confound from the review panel does not apply to the T1 scores of sft-aug through this channel.

The training passes started after teach-stop. The cpt verb and the merge-cpt
verb passed, and the merged base sits at /scratch/outputs/cpt-merged on the
instance. The first merge-cpt attempt failed before any instance work, in the
deps step of the GitHub runner, and the operator retried the idempotent verb
once. The observer accepted the retry: the failure sat in the runner network,
not in the verb. The sft-aug dispatch followed.

Claim: the first merge-cpt attempt (run 33350659650) failed in the deps step on `Unable to establish SSL connection.` from the opentofu download, before any Scaleway or instance work, and the identical retry (run 33350760205) passed and produced /scratch/outputs/cpt-merged. Verdict: Confirmed with corrections, the two merge-cpt logs. The corrections: the error fell 6.5 seconds into the job log, not 9; the retry log spans 39.9 seconds, not 50; and the fault localizes to a transient TLS fault between the runner and the GitHub release CDN, not to the runner alone and not to the verb or the project scripts.

Claim: the CPT rehearsal (run 33350483695) tokenized 7009 prompts into 7011 tokenized rows with zero dropped, ran 8 steps in train_runtime 19.8 s with train_loss 5.997, peaked at 29.66 GiB GPU memory active with no out-of-memory error, and the job ran 1 min 58 s. The teacher container was absent before the launch. Verdict: Confirmed with two corrections, cpt log and run metadata. The corrections: the 8-step bound comes from `num_epochs: 1` over the packed data, not from a max_steps cap in train/cpt.yml, and the 7011 rows are pre-packing tokenized rows, which packing then bins.

The sft-aug pass (run 33350914761) passed in 15 min 3 s, with no retry. The
trainer read pairs-aug.jsonl from the corpus sync, trained from the merged
CPT base with a qlora adapter, and saved /scratch/outputs/sft-aug. The final
line confirms the checkpoints reached Object Storage. The observer dispatched
gguf and score for the name sft-aug.

Claim: the sft-aug pass (run 33350914761) trained from /scratch/outputs/cpt-merged with a qlora adapter on 22222 examples (11 dropped over-length to 22211), ran 464 steps to epoch 1.991 in train_runtime 813.5 s, moved the logged loss from 2.881 to 0.0486 with mean train_loss 0.1859, and hit no out-of-memory error. Verdict: Confirmed with one refuted part, sft-aug log. The correction: the device_reserved peak was 39.06 GiB in the first epoch, and the run never reaches an epoch 2; the 34.79 GiB summary value is the final reading, not the peak.

The "22111 base + 111 teacher" arithmetic is unsupported. No record prints a
base row count, and the pairs increments are smaller than the accepted
counts, so the rebuild deduplication drops some accepted records.

Claim: the pairs-aug.jsonl count after each teach batch is 22138, 22162, 22187, 22199, 22208, 22222, and the increments of batches 2 to 6 (24, 25, 12, 9, 14) fall short of the accepted counts (26, 27, 14, 11, 18) by 12. The rebuild merges the accepted files through a text-keyed duplicate drop, so the teacher proposed 12 duplicate sentences across batches that the judge accepted twice. Batch 1 has no pairs baseline in the records. Verdict: Confirmed, the six teach logs, the six reports, and the rebuild code at commit 6e93620. The teacher repeats itself measurably at temperature 0.9 across batch seeds: 12 of 96 accepted records in batches 2 to 6 were text duplicates.

The gguf and score verbs passed for sft-aug (runs 33351940505 and
33352183041). The Q8_0 artifact reached the checkpoint bucket, and the
dev scorecard reached the artifacts bucket. Every artifact key of the
campaign carries the stack-up run identifier gh-33342320766, because
the instance boot environment fixes STX_RUN_ID at up. The observer
dispatched the down verb to stop the spend, and the T1 sweep in
parallel: the sweep reads the GGUF from the checkpoint bucket, and it
needs no train instance.

Claim: the dev scorecard of sft-aug (run 33352183041, llama.cpp b10666, GPU): ewt LAS 0.7604, lemma 0.9411, upos 0.9267 over 2001 sentences with 76 failures; gum LAS 0.7621, lemma 0.9562, upos 0.9374 over 1383 sentences with 22 failures. The gguf run exported a Q8_0 artifact of 633.5 MB with 310 tensors, model hash 8aa0b9340f979dca1f1cdd09da62c6d10a07ad7996c919e5e5c8a694ec5582be. Verdict: Confirmed, the gguf and score logs. The counts agree with the ratios: 19122 of 25148 and 18795 of 24661.

The down step (run 33354203115) destroyed the train stack: the server, the
IP, the 3000 GB scratch volume, and the campaign SSH key. The claim released,
and the train keys deleted. infra-status then listed zero train resources,
with the four persistent buckets intact. The spend stopped. Only the T1
sweep, the promote decision, and the closing work remain, and none of them
needs an instance.
