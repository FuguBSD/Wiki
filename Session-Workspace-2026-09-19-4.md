# Session Workspace 2026-09-19 4

Session: 29b0c67a-ff8b-59cf-b25c-3acd2cf76166
Project: Workspace
Opened: 2026-09-19T16:06:01Z

## Observations

Claim: a coherence defect can pass every gate of every repository, because no gate reads a sibling. The FuguSeed 004 procedure page fuguseed(7) ordered "Burn or shred every other paper of the procedure", and FuguPass spec/keys.md defines a plate as "a metal plate, or the paper drawing of the FuguSeed procedure". The step destroyed the recovery root of the sibling.
Evidence: FuguSeed branch feat/004-fuguseed-words, commit 234f920, host make check green. The standalone coherence reviewer found it, and commit 587e595 fixed it.
The same step also contradicted fuguseed-qr(1) line 94, "Keep the paper as you keep the words", in its own repository. A destruction instruction needs an exception list for each object that a consumer keeps.
Claim: a fix round writes a new falsehood into the text that it corrects. Commit a5b60a2 replaced a false cross-repository claim in OVW-VOCABULARY-4 with "The device scans the SeedQR with a camera ... (D-13)". D-13 states no camera, and its rationale admits any device that accepts 12 BIP39 words.
Evidence: FuguSeed spec/DECISIONS.md line 21, and spec/overview.md before commit 29e78f8.
The remedy was a removal, not a replacement. A vocabulary rule defines a term, and the capability belongs to the unit that owns it.

Closed: 2026-09-19T19:55:42Z
