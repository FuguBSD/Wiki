# FuguSTX — llama.cpp at a pinned build, CPU inference

Rehearses: FuguTTX D2, and the FuguTTX inference specification

**ghcr publishes llama.cpp tags with gaps.**

Evidence: 2026-08-28. The pinned `full-b10665` never existed, and docker exited
125. The tags jump b10644 to b10666 across 11,004 tags, and the unauthenticated
first registry page truncates at b5350.
Scope: one registry, one day. Pin a tag only after a paginated registry probe.
Maps to: FuguTTX TRN-EXEC.

**q8_0 conversion works on Qwen3 directly.**

Evidence: 2026-08-28. The feared fallback, f16 and then `llama-quantize`, never
ran. The converter wrote `torch.bfloat16 --> Q8_0`, a 633 MB artifact at 174
MB/s.
Scope: one model family, one converter build.
Maps to: FuguTTX D2, the FuguTTX inference specification.

**llama.cpp split its CLI, and the pin crossed the split.**

Evidence: 2026-08-29, run 33240741185. At b10666, `llama-cli` is a chat tool: it
rejects `-no-cnv`, and its chat template would wrap the raw prompt. The raw
one-shot tool is now `llama-completion`, and the end marker arrives as
" [end of text]" with a leading space. The first sweep dispatch failed on all
twelve shards in under a minute at zero GPU cost, and a local probe with the
promoted GGUF proved the fix.
Scope: one build pin. The server transport never sees the flag, so dev scoring
missed the break. Each transport needs its own probe after a pin change.
Maps to: FuguTTX D2, FuguTTX TRN-EXEC, the FuguTTX inference specification.
