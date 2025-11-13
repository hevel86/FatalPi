# Repository Guidelines

## Project Structure & Module Organization
This repo anchors three self-contained stacks: `traefik/`, `portainer/`, and `minidlna/`. Each folder holds a single `docker-compose.yml` plus any stack-specific assets you may add later. Shared documentation and scripts belong at the repo root; service-specific secrets or bind mounts should live beside their compose file using `.env` files ignored by Git.

## Build, Test, and Development Commands
- `docker compose -f traefik/docker-compose.yml up -d`: starts the sample whoami service behind Traefik for smoke testing.
- `docker compose -f portainer/docker-compose.yml up -d`: deploys the Portainer agent; pair with your main Portainer server via port 9001.
- `docker compose -f minidlna/docker-compose.yml up -d`: launches the MiniDLNA stack using the declared NFS volumes; confirm host exports before running.
Use `docker compose down` with the same `-f` flag to tear down stacks, and `docker compose logs -f <service>` for live diagnostics.

## Coding Style & Naming Conventions
Write compose files in YAML v3, two-space indents, keys in lower_snake_case that match Docker defaults, and keep service names aligned with their `container_name`. Environment variables stay uppercase with descriptive prefixes (`MINIDLNA_*`), and volumes should specify explicit `source`/`target` blocks for clarity. If you add helper scripts, prefer POSIX shell with `set -euo pipefail` and kebab-case filenames.

## Testing Guidelines
There are no automated tests, so validation is command-driven. Run `docker compose -f <path>/docker-compose.yml config --quiet` before committing to ensure the manifest parses. After launching, check `docker compose -f <path>/docker-compose.yml ps` and `docker compose -f <path>/docker-compose.yml logs -f <service>` to confirm healthy containers. Document any manual verification steps (for example DLNA library scans or Portainer agent pairing) in your pull request.

## Commit & Pull Request Guidelines
Git history is currently empty; adopt short, imperative commit subjects prefixed with the touched stack, e.g., `minidlna: add tv volume`. Describe why the change is needed in the body and call out external dependencies (new ports, mounts, credentials). Pull requests should include: summary of changes, impact per stack, validation commands, and screenshots or log excerpts when UI-facing services (such as Portainer) are affected. Link to tracking issues or tickets if available.

## Security & Configuration Tips
Never hardcode credentials in compose files—load them via `.env` or Docker secrets and add those files to `.gitignore`. Review bind mounts carefully; Portainer’s `/var/run/docker.sock` and host root mounts grant wide access, so restrict usage in development environments. MiniDLNA relies on NFS paths; verify they remain read-only in production and document any firewall changes required for host network mode.
