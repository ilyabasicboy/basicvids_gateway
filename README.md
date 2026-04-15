# BasicVids Gateway

Shared nginx entrypoint for BasicVids microservices.

## Start

Create the shared network once:

```bash
docker network create basicvids
```

Start the service containers, then start the gateway:

```bash
cd ../basicvids_auth
docker compose up -d

cd ../basicvids_storage
docker compose up -d

cd ../basicvids_gateway
docker compose up -d
```

The gateway listens on:

```text
http://localhost:8080
```

## Routes

| Route | Service |
| ----- | ------- |
| `/api/v1/auth/*` | `basicvids_auth:8000` |
| `/api/v1/users/*` | `basicvids_auth:8000` |
| `/api/v1/videos/*` | `basicvids_storage:8000` |
| `/auth/health` | `basicvids_auth:8000/health` |
| `/storage/health` | `basicvids_storage:8000/health` |

## Adding A Microservice

Attach the microservice container to the external `basicvids` network:

```yaml
networks:
  basicvids:
    external: true
    name: basicvids
```

Then add an upstream and route in `nginx.conf`, for example:

```nginx
upstream comments_app {
    server basicvids_comments:8000;
    keepalive 32;
}

location /api/v1/comments/ {
    proxy_pass http://comments_app;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```
