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
