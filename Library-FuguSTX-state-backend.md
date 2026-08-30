# FuguSTX — State backend, native lock, encryption

Rehearses: the shared instructions

**The state bucket with a native lock worked on every apply.**

Evidence: 2026-08-26. The backend with `use_lockfile = true` worked on the first
init and on every apply.
Scope: one state bucket, one provider version.
Maps to: the shared instructions.

**A validate gate needs its own state directory.**

Evidence: 2026-08-26. `tofu init -backend=false` still demands the backend
credential in a checkout that applied the stack. So the validate gate needs its
own `TF_DATA_DIR`.
Scope: one checkout that already applied the stack.
Maps to: the shared instructions.
