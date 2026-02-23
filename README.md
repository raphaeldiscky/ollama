# ollama

Standalone Docker Compose deployment for [Ollama](https://ollama.com). Runs as a shared service that other projects connect to via the `ollama-net` Docker network.

## Prerequisites

- Docker & Docker Compose
- [Task](https://taskfile.dev) (optional, for convenience commands)
- NVIDIA GPU + [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) (for GPU acceleration)

## Quick start

```bash
# Create the shared network (one-time)
task network:create

# Copy and configure environment
cp .env.example .env

# Start with GPU
task up:gpu

# Or start with CPU only
task up:cpu
```

## Recommended models

| Model | Size | VRAM | Notes |
|-------|------|------|-------|
| `llama3.2` | 3B | 2GB | Compact general-purpose (default) |
| `llama3.1:8b` | 8B | 5GB | Strong general-purpose |
| `qwen3:8b` | 8B | 5GB | Top benchmark scores 2025 |
| `gemma3:4b` | 4B | 3GB | Google, fast inference |
| `mistral:7b` | 7B | 4GB | Fast, good for grammar |
| `phi4-mini` | 3.8B | 2GB | Microsoft, compact |

Set in `.env` or pull directly:

```bash
task model:pull -- qwen3:8b
```

## Profiles

| Profile      | Command              | Description          |
| ------------ | -------------------- | -------------------- |
| `cpu`        | `task up:cpu`        | CPU-only inference   |
| `gpu-nvidia` | `task up:gpu`        | NVIDIA GPU with CUDA |
| `gpu-amd`    | `task up -- gpu-amd` | AMD GPU with ROCm    |

## Model management

```bash
task model:list              # List downloaded models
task model:pull -- qwen3:8b  # Pull a specific model
task model:test              # Send a test prompt
```

## Connecting from other projects

Other Docker Compose projects can reach Ollama by joining the `ollama-net` external network:

```yaml
# In another project's docker-compose.yml
networks:
  ollama-net:
    external: true

services:
  my-app:
    networks: ["default", "ollama-net"]
    environment:
      - OLLAMA_HOST=ollama:11434
```

For host-level access (e.g. CLI tools), Ollama is available at `http://localhost:11434`.

## All tasks

```bash
task --list
```
