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
