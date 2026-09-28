# 5g-network-agent

Turns a single Linux machine into a working private 5G network you can talk to: it stands up the network in software, watches it, and lets you ask about it in plain language.

Capstone group project (Projeto Integrador, PE38) for L.EIC at FEUP/FCUP (2025/26).

## What it does

Boots a full **5G standalone testbed** on one host — an Open5GS core, two base stations (gNBs) and two phones (UEs), with the radio simulated in software — then layers three things on top:

- **Telemetry** — a collector gathers per-UE and per-cell metrics from the running network and stores them durably (SQLite).
- **Observability** — a REST API and live dashboards over that data.
- **RANPilot, a local-LLM operator agent** — ask about the network in natural language ("which cell is UE 2 on?", "is anything degraded?") and it answers from the real telemetry, running against a local model so nothing leaves the machine.

Two radio backends are supported (OAI and srsRAN), selected with `RAN_BACKEND`.

## Stack

Python, Open5GS, OAI / srsRAN (ZMQ-simulated radio), SQLite, a REST API and dashboards, a local LLM, and Linux network namespaces supervised with transient systemd units. The SIM values in `config/` are the standard public OAI test credentials for a simulated network — there is no real SIM or carrier involved.

## How to run

Needs a Linux host with the 5G components installed (see `setup_oai_open5gs.sh`).

```bash
bash src/launch_stack.sh              # bring the whole stack up (OAI backend)
RAN_BACKEND=srsran bash src/launch_stack.sh   # or the srsRAN backend
bash src/launch_stack.sh --stop       # tear it down cleanly
```
