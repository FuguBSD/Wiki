# FuguSTX — Qwen3-32B under vLLM, SSH tunnel, judge filter

Rehearses: FuguTTX TRN-AUG, FuguTTX D4

This rehearsal is planned. No campaign has run it, so this page holds no claim.

**A first teach-serve on a fresh instance needs near six minutes, not tens of
minutes.**

Evidence: run 33342530998, the serve log. The pull of vllm/vllm-openai:v0.28.0
took 72 seconds, vLLM answered 290 seconds after the pull ended, and the job ran
6 min 19 s end to end.
Scope: Qwen3-32B-FP8 on the 80 GB H100 of run gh-33342320766. The serve log does
not name the GPU type; the offer comes from the up log. The 15-second health
poll bounds each ready time to one window.
Maps to: FuguTTX TRN-AUG.

**The teach-serve action reads the action input only.**

Evidence: run gh-33342320766; scripts/train at the run commit 6e93620. The
script rejects `--run-id` for every verb except promote and teach.
Scope: the train.yml inputs at commit 6e93620.
Maps to: FuguTTX TRN-AUG.

**The teach path reaches vLLM over the SSH tunnel only.**

Evidence: run 33342530998, the line `teach-serve: vLLM answers on
127.0.0.1:8000`. The container binds 127.0.0.1:8000 on the instance, and no
public port opens.
Scope: the tunnel of FuguSTX TRN-TEACH-3, at commit 6e93620.
Maps to: FuguTTX TRN-AUG.

**The CI path captures no vLLM GPU memory number at teach-serve.**

Evidence: run 33342530998 and the driver code at commit 6e93620. The driver
starts the container with `docker run -d`, the runner log holds the pull and the
ready line only, and no train.yml verb fetches a container log.
Scope: the teach-serve verb at commit 6e93620. The FP8 fit confirmation is the
health answer alone.
Maps to: FuguTTX TRN-AUG, FuguTTX TRN-EXEC.

**Teach batch 1 accepted 15 of 200 proposals, and the disagree reason dominated
the rejects.**

Evidence: the teach report and the log of run 33342886087, under run
gh-33342320766. Rate 0.075, and the teach step ran 22 min 49 s. The rejects:
disagree 159, tag 12, tree 7, word 7, count 0.
Scope: one batch, generation temperature 0.9, at commit 6e93620.
Maps to: FuguTTX TRN-AUG.

**The reject reason `disagree` counts a disagreement of the two seeded
annotation passes.**

Evidence: run gh-33342320766; judge.py and teacher.py at the run commit 6e93620.
Check 1 of the judge filter runs two passes at temperature 0.2, seeds 11 and 23,
and compares them after normalization.
Scope: the judge filter at commit 6e93620. In batch 1 the passes disagreed on
159 of 200 proposals, so check 1 is the dominant gate, by a factor of six over
all other reject reasons combined.
Maps to: FuguTTX TRN-AUG.
