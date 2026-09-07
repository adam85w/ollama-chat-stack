# Ollama Chat Stack
 
A minimal Docker Compose setup for running a local LLM with [Ollama](https://ollama.com), a simple web chat UI, and Prometheus-compatible metrics.
 
## Overview
 
This stack runs a local language model (`llama3.2:1b` by default) served by Ollama, automatically pulls the model on first startup, exposes a lightweight browser-based chat interface, and exports usage metrics for monitoring.
 
## Services
 
### ollama
The Ollama inference server itself. Hosts the model, serves the REST API, and stores downloaded models in a persistent volume.
- **Port:** `51434` → container port `11434` (Ollama API)
- Resource limits: 2GB RAM (hard), 1GB RAM (soft reservation), 2 CPU cores

### ollama-model-pull
A one-shot helper container that waits for `ollama` to become ready, then pulls the configured model (`llama3.2:1b`). Exits after the download completes; no exposed ports.
 
### ollama-ui
A single static HTML page (served by Nginx) providing a minimal browser-based chat interface for talking to the model. Connects directly to the Ollama API from the browser.
- **Port:** `51480` → container port `80` (web UI)

### ollama-exporter
A Prometheus exporter that polls the Ollama API and exposes metrics (requests, loaded models, resource usage) in Prometheus format.
- **Port:** `51500` → container port `8000` (`/metrics`, `/health`)

## Ports summary
 
| Service           | Host port | Purpose                      |
|-------------------|-----------|------------------------------|
| ollama            | 51434     | Ollama                       |
| ollama-ui         | 51480     | Chat web UI                  |
| ollama-exporter   | 51500     | Prometheus metrics endpoint  |

 
## Usage
 
```bash
podman-compose up
```
 
Wait for the `ollama-model-pull` container to finish downloading the model, then open:
 
```
http://localhost:51480

