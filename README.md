# Promtail Docker Logs to Loki

Runs a single Promtail instance that auto-discovers all Docker containers on the host and ships their logs to Loki. Each container is labeled by name — no per-service configuration needed.

## Requirements

- Docker + Docker Compose
- Loki endpoint (e.g. `http://loki.example.com:3100/loki/api/v1/push`)

## Quick Start

1. Create your `.env` file:

   ```bash
   cp .env.example .env
   ```

2. Edit `.env`:

   ```env
   LOKI_URL=http://your-loki-server:3100/loki/api/v1/push
   HOSTNAME=my-docker-host
   ```

3. Start Promtail:

   ```bash
   docker compose up -d
   ```

4. Check logs:

   ```bash
   docker compose logs -f promtail
   ```

## Configuration

| Variable | Description |
|----------|-------------|
| `LOKI_URL` | Loki push API endpoint |
| `HOSTNAME` | Label added to all logs as `host` |

## Query Logs in Grafana

```logql
# All logs from a specific container
{container="itv-msq-v2"}

# All logs from a host
{host="my-docker-host"}

# Filter by container and level
{container="itv-msq-v2"} | json | level="ERROR"
```

## How It Works

- Uses **Docker service discovery** via `/var/run/docker.sock` to auto-detect all running containers
- Each container gets a `container` label with its name (e.g. `itv-msq-v2`, `itv-auth`)
- No config changes needed when adding or removing services — Promtail discovers them automatically
- Position state persisted in `promtail-positions` volume (survives restarts)
