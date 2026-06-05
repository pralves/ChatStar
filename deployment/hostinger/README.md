# Hostinger Docker Deploy

Use this deployment from a fresh VPS with Docker and Docker Compose installed.

## DNS

Point `chat.eletrostarsoft.com.br` to the VPS public IP before starting Caddy:

```text
A chat.eletrostarsoft.com.br -> 187.77.245.58
```

## Deploy

```bash
git clone https://github.com/pralves/ChatStar.git /opt/chatstar
cd /opt/chatstar
git checkout develop

cp deployment/hostinger/.env.example deployment/hostinger/.env
nano deployment/hostinger/.env

docker compose -f deployment/hostinger/docker-compose.yaml --env-file deployment/hostinger/.env build
docker compose -f deployment/hostinger/docker-compose.yaml --env-file deployment/hostinger/.env up -d postgres redis
docker compose -f deployment/hostinger/docker-compose.yaml --env-file deployment/hostinger/.env run --rm prepare
docker compose -f deployment/hostinger/docker-compose.yaml --env-file deployment/hostinger/.env up -d rails sidekiq caddy
```

## Validate

```bash
docker compose -f deployment/hostinger/docker-compose.yaml --env-file deployment/hostinger/.env ps
curl -fsS https://chat.eletrostarsoft.com.br/api
```

The expected API health response includes:

```json
{"queue_services":"ok","data_services":"ok"}
```

## Database Access

The Postgres container is not exposed publicly. On the VPS it is available at:

```text
Host: 127.0.0.1
Port: 5432
Database: chatwoot
User: postgres
Password: POSTGRES_PASSWORD from deployment/hostinger/.env
```

From another machine, use an SSH tunnel instead of opening port `5432` publicly.
