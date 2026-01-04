# WMN Analyzer – Dockerized MQTT Analytics Service

This repository contains the source code and Docker build files for **wmn-analyzer**, an MQTT-based analytics service used in the Wireless & Mobile Networks (WMN) project.

The analyzer runs on a server or fog node and processes raw network metrics produced by edge devices. It computes a wireless quality score, generates alerts for degraded conditions, and republishes the results over MQTT for dashboards and downstream services.

The **primary distribution method is Docker Hub**. This repository exists to document, version, and reproduce the Docker image build.

---

## What the Analyzer Does

For each incoming metrics message, the analyzer computes:

* **Wireless experience score (0–100)** based on RSSI (when available), latency, jitter, and packet loss
* **Alerts** for weak signal, high latency, and packet loss
* **Mobility / handover heuristic** that flags sudden RSSI drops within a short time window

All processing is lightweight and stateless, suitable for fog or home server deployment.

---

## MQTT Topics

### Subscribed (input)

```
wmn/metrics/#
```

### Published (output)

```
wmn/analysis/<DEVICE_ID>
```

---

## Docker Images

Docker Hub repositories:

* **WMN Analyzer (this repository)**
  [https://hub.docker.com/r/irfanuruchi/wmn-analyzer](https://hub.docker.com/r/irfanuruchi/wmn-analyzer)

Related images in the same project:

* **WMN Collector (edge metrics)**
  [https://hub.docker.com/r/irfanuruchi/wmn-collector](https://hub.docker.com/r/irfanuruchi/wmn-collector)

* **WMN Explainer (explanation service)**
  *(to be added)*

---

## Running the Analyzer

### Fog / Home Server (recommended)

```bash
docker pull irfanuruchi/wmn-analyzer:latest

docker run -it --name wmn-analyzer \
  --restart unless-stopped \
  -v wmn_analyzer_config:/config \
  irfanuruchi/wmn-analyzer:latest
```

On first run, the container prompts for MQTT broker connection details and stores them in:

```
/config/config.env
```

This file is persisted using the `wmn_analyzer_config` Docker volume.

### Logs

```bash
docker logs -f wmn-analyzer
```

Expected output:

```
[mqtt] on_connect rc=0 connected=True
[mqtt] subscribed wmn/metrics/#
[pub] wmn/analysis/<device> score=... alerts=...
```

---

## Repository Contents

* `analyzer.py` – MQTT subscribe/publish logic and message handling
* `scorer.py` – scoring, alert rules, and mobility heuristics
* `entrypoint.sh` – first-run configuration script
* `Dockerfile` – container build definition
* `requirements.txt` – Python dependencies

Secrets and runtime configuration are **not** committed to this repository.

---

## Related GitHub Repositories

This repository is part of a multi-component system. Related GitHub repositories include:

* **wmn-collector**
  Edge-side metrics collection

* **wmn-analyzer** (this repository)
  Fog-layer analytics and scoring service

* **wmn-explainer** *(to be added)*
  Fog-layer explanation service

* **wmn-edge-system** *(head repository, to be added)*
  Top-level repository that documents the full system architecture and links all components together

---

## Other GitHub Resources

The following repositories may be linked here as the project expands:

* Local MQTT broker configuration and bridging
* Grafana dashboard JSON exports
* Evaluation scripts and experiments
* Documentation and reports

This section is intentionally left open to support future extensions without restructuring the repository.
