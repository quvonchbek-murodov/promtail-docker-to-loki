# Promtail Docker Logs to Loki

This project runs Promtail in Docker Compose and forwards Docker container logs to a Loki server.

## Files

- `docker-compose.yml`: Runs Promtail container and mounts Docker log sources.
- `promtail-config.yml`: Promtail scrape and pipeline configuration for Docker logs.

## Requirements

- Docker + Docker Compose
- Loki endpoint (for example: `http://loki.example.com:3100/loki/api/v1/push`)

## Quick Start

1. Create your `.env` file from the template:

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

4. Check Promtail logs:

   ```bash
   docker compose logs -f promtail
   ```

5. Stop the stack:

   ```bash
   docker compose down
   ```

## Configuration

The compose file supports these environment variables:

- `LOKI_URL`: Loki push API endpoint.
- `HOSTNAME`: Label added to logs (`host`).

The setup uses `.env` only:

```env
LOKI_URL=http://your-loki-server:3100/loki/api/v1/push
HOSTNAME=my-docker-host
```

Then run:

```bash
docker compose up -d
```

## Verify Logs in Loki

Use Grafana Explore with a query like:

```logql
{job="docker"}
```

Or filter by container label:

```logql
{job="docker", container="your-container-name"}
```

## Notes

- This setup reads Docker json-file logs from `/var/lib/docker/containers`.
- Promtail position state is stored in the `promtail-positions` volume.
- This setup uses file-based Docker log scraping and does not require Docker API discovery.
