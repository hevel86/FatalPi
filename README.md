# Docker Service Stacks

This repository hosts a trio of lightweight Docker Compose bundles that can be deployed independently on a Raspberry Pi (or any Docker host) to provide a reverse-proxy test target, a Portainer agent, and a MiniDLNA media server.

## Repository Layout
- `traefik/`: sample whoami service for validating Traefik routing.
- `portainer/`: Portainer agent container so remote Portainer instances can manage this node.
- `minidlna/`: MiniDLNA server wired to NFS-backed media shares.
- `AGENTS.md`: contributor guide covering workflow conventions.

Each directory currently contains a single `docker-compose.yml`; place stack-specific `.env` files, secrets, and bind mounts alongside those manifests.

## Prerequisites
1. Docker Engine 24+ and Docker Compose plugin (`docker compose`) installed on the host.
2. Network access to pull the public images (`traefik/whoami`, `portainer/agent`, `vladgh/minidlna`).
3. For MiniDLNA, reachable NFS exports that match the paths declared in `docker-compose.yml`.

## Usage
Launch any stack by pointing `docker compose` at the desired manifest:
```bash
docker compose -f traefik/docker-compose.yml up -d
docker compose -f portainer/docker-compose.yml up -d
docker compose -f minidlna/docker-compose.yml up -d
```
Shut down with the corresponding `docker compose -f <path> down`. Tail logs using `docker compose -f <path> logs -f <service>`, for example `docker compose -f minidlna/docker-compose.yml logs -f minidlna`.

## Configuration Notes
- Portainer mounts `/var/run/docker.sock`, `/var/lib/docker/volumes`, and `/` to expose host control; restrict access to trusted users and networks.
- MiniDLNA runs in host network mode; open the necessary multicast/UPnP ports on your firewall and keep the NFS mounts read-only as shown.
- The Traefik sample publishes port 8080 on the host; update the mapping or add TLS/middleware if exposing beyond localhost.

## Contributing
Follow the guidance in `AGENTS.md` for coding style, validation steps, and pull-request expectations. In short: keep compose files consistent (two-space indent, lowercase service keys), validate with `docker compose config --quiet`, document any manual verification you performed, and prefix commits with the stack they touch (e.g., `PORTAINER: explain agent volume`). Pull requests should summarize the change, list affected stacks, and include screenshots or logs for UI-visible changes.
