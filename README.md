# Docker Compose Reverse Proxy Demo (Nginx + whoami)

This project runs two containers with Docker Compose:

Nginx serves a static index page on port 80 and reverse proxies requests to a whoami container.

## What it does

Nginx listens on port 80.
The root path `/` serves an index.html page.
The path `/whoami/` proxies to the `whoami` service over the Docker network.
A healthcheck verifies Nginx is serving traffic.

## Project structure

compose-demo/
  docker-compose.yml
  nginx/
    default.conf
  site/
    index.html

## Prerequisites

Docker installed
Docker Compose installed

## How to run

From the project root:

docker-compose up -d

Check containers:

docker-compose ps

## Test locally on the EC2 instance

Root page:

curl -I http://localhost/

Whoami through Nginx:

curl -I http://localhost/whoami
curl -I http://localhost/whoami/

If you see a 301, that is expected because Nginx redirects /whoami to /whoami/ to enforce the trailing slash.

To follow redirects automatically:

curl -L http://localhost/whoami

## Test from your browser

http://<EC2_PUBLIC_IP>/
http://<EC2_PUBLIC_IP>/whoami/

## Healthcheck

The web service includes a healthcheck that hits:

http://localhost/

View health status:

docker inspect --format='{{json .State.Health}}' nginx-web | jq

If jq is not installed, run without jq:

docker inspect --format='{{.State.Health.Status}}' nginx-web

## Useful commands

Reload Nginx after config changes:

docker exec -it nginx-web nginx -t
docker exec -it nginx-web nginx -s reload

View logs:

docker logs nginx-web --tail 50
docker logs whoami --tail 50

Stop everything:

docker-compose down

## What this demonstrates

- Nginx acting as a reverse proxy
- Path-based routing using /whoami/
- Service discovery via Docker Compose DNS
- Health checks for container availability
- URL normalization and redirect handling

