# 5g-network-agent

Turns a single Linux machine into a working private 5G network you can talk to: it stands up the network in software, watches it, and lets you ask about it in plain language.

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

## What I built

Capstone group project (Projeto Integrador, PE38) for L.EIC at FEUP/FCUP (2025/26). I was the lead contributor. My work:

- **Multi-gNB / multi-UE orchestration** — launching several base stations and phones and keeping their network namespaces and data paths in order.
- The **metrics and telemetry tooling**, the SQLite cache and the per-entity identity/validation that feed the dashboards.
- **Integrating the LLM agent with the live network**: single-pass entity extraction from the telemetry, the JSON contract the model answers from, and fixes to keep its answers grounded (including making it answer in Portuguese).
- **Supervising the stack** with transient systemd units so the core, radio, collector, API and dashboard start and stop reliably, and the end-to-end validation run.

## What I would do differently

Replace the shell-script supervision with a small process manager or containers so the stack is easier to reproduce off this exact host, and separate the agent's retrieval layer from the LLM so the same telemetry queries can be tested without a model loaded.
