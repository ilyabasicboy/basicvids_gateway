# BasicVids Gateway

Shared nginx entrypoint for BasicVids microservices.

## Start

Create the shared network once:

```bash
docker network create basicvids
```

Start the service containers, then start the gateway:

```bash
cd ../basicvids_auth && docker compose up -d
cd ../basicvids_storage && docker compose up -d
cd ../basicvids_channels && docker compose up -d
cd ../basicvids_comments && docker compose up -d
cd ../basicvids_engagement && docker compose up -d
cd ../basicvids_history && docker compose up -d
cd ../basicvids_favorites && docker compose up -d
cd ../basicvids_gateway && docker compose up -d
```

The gateway listens on:

```text
http://localhost:8080
```

## Routes

| Route | Service |
| --- | --- |
| `/api/v1/auth/*` | `basicvids_auth:8000` |
| `/api/v1/users/*` | `basicvids_auth:8000` |
| `/api/v1/avatars/*` | `basicvids_auth:8000` |
| `/api/v1/videos/*` | `basicvids_storage:8000` |
| `/api/v1/categories/*` | `basicvids_storage:8000` |
| `/api/v1/comments/*` | `basicvids_comments:8000` |
| `/api/v1/engagement/*` | `basicvids_engagement:8000` |
| `/api/v1/history/*` | `basicvids_history:8000` |
| `/api/v1/favorites/*` | `basicvids_favorites:8000` |
| `/api/v1/channels/*` | `basicvids_channels:8000` |
| `/auth/health` | `basicvids_auth:8000/health` |
| `/storage/health` | `basicvids_storage:8000/health` |
| `/comments/health` | `basicvids_comments:8000/health` |
| `/engagement/health` | `basicvids_engagement:8000/health` |
| `/history/health` | `basicvids_history:8000/health` |
| `/favorites/health` | `basicvids_favorites:8000/health` |
| `/channels/health` | `basicvids_channels:8000/health` |
| `/health` | Gateway self healthcheck |

## Adding A Microservice

Attach the microservice container to the external `basicvids` network:

```yaml
networks:
  basicvids:
    external: true
    name: basicvids
```

Then add an upstream and route in `nginx.conf`.
