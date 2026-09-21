# Session Workspace 2026-09-21 2

Session: 746bcad8-f93b-4ab7-8689-46b5bf96ffa5
Project: Workspace
Opened: 2026-09-21T05:17:53Z

## Observations

Claim: the upstream `blind_pin_server` runs on the macOS host as a FuguPass
harness counterparty, and it answers `GET /` with 200.

Evidence: a clone of Blockstream/blind_pin_server under `scratch/`, a venv on
Python 3.13, and a start from a fresh working directory.

The recipe. Build the venv with `uv venv --python 3.13`, and install the
requirements with `uv pip install --require-hashes -r requirements.txt`. Python
3.14 fails: the pinned wallycore 1.5.3 publishes no cp314 wheel, and the source
build needs autotools that the host does not hold. Generate the key with
`PYTHONPATH=<parent of the checkout> python -m
blind_pin_server.generateserverkey`. Start the server with `python -m flask
--app blind_pin_server.flaskserver run --host 0.0.0.0 --port 8096`.

The working directory of the server holds `server_private_key.key` at mode 0600
and an empty `pins` directory. `pindb.py` takes the record store from the `pins`
directory of the working directory, so a fresh working directory gives a fresh
store (FuguPass TEST-MASK-6).

The server speaks plain HTTP. FuguPass `http_post()` accepts the scheme http, so
a client in the OpenBSD guest can reach the host server at the QEMU gateway
address (FuguPass TEST-HARNESS-7).
