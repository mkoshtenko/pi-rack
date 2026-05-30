# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Infrastructure-as-code for a small Raspberry Pi homelab. There is no application code — only Ansible playbooks and Docker Compose files used to provision two Pi hosts:

- **`ns`** (host `kleo`, `192.168.1.2`) — DNS stack: Traefik → Pi-Hole → Unbound, plus Portainer and Watchtower.
- **`mon`** (host `millie`, `192.168.1.16`) — Monitoring stack: Zabbix server + web + Postgres 16, plus Watchtower.

Designed for LAN-only use; configs do not include public-internet hardening.

## Working with the playbooks

All `ansible-playbook` commands are run from the repo root and require a local `devices.ini` inventory (copy from `devices.ini.example`):

```
cp devices.ini.example devices.ini      # edit IPs / ansible_user
ansible all -m ping                      # sanity check connectivity
```

Bootstrap order for a fresh host (`-l ns` or `-l mon` limits to one group):

```
ansible-playbook -i devices.ini -l <group> ansible/rpi-os-lite-basic-playbook.yml
ansible-playbook -i devices.ini -l <group> ansible/rpi-os-rack-display-setup-playbook.yml   # only for Uctronics rack hardware
ansible-playbook -i devices.ini -l <group> ansible/docker-playbook.yml
ansible-playbook -i devices.ini    ansible/ns-playbook.yml   # or mon-playbook.yml
```

`bullseye-zabbix-agent2-playbook.yml` is the only playbook intended to run against *every* host (so the `mon` Zabbix server can scrape them). Despite the filename it currently uses the Zabbix Bullseye repo even when run against Bookworm — see the note in `mon/README.md`.

## How `ns-playbook.yml` and `mon-playbook.yml` actually deploy

These two playbooks are the glue between this repo and the running stacks. They:

1. Create `~/docker/{shared,app,logs,app/<service>...}` on the remote host.
2. `copy:` the per-host secrets (`ns/.env`, `ns/.htpasswd`, `ns/a-records.conf`, `mon/.env`) **plus** the matching `docker/docker-compose-<group>.yml` (renamed to `docker-compose.yml` on the host) **plus** Traefik rule files and `unbound.conf`.
3. Run `docker_compose` against `~/docker` to bring the stack up.

Implications when editing:
- Compose files in `docker/` are never executed locally — they only run after the playbook ships them to a Pi. Test changes by re-running the corresponding playbook.
- Any new file the compose stack needs must also be added to the `copy:` loop in the relevant playbook, or it won't reach the host.
- Anything in `.gitignore` (`.env`, `.htpasswd`, `devices.ini`, `a-records.conf`) must exist locally before the playbook runs — create them from the matching `.example`.
- `ns-playbook.yml` additionally patches `~/docker/app/pihole/pihole-FTL.conf` (`PRIVACYLEVEL`, `BLOCK_ICLOUD_PR`) and restarts the stack only when those values changed.

## Stack quirks worth knowing

- **Pi-Hole container must NOT have `security_opt: no-new-privileges:true`** — otherwise the admin UI can't save settings (documented in `ns/README.md`).
- **Unbound** has a static IP `192.168.90.123` on the `traefik-net` subnet so Pi-Hole upstream stats stay stable.
- **Pi-Hole** has `FTLCONF_LOCAL_IPV4=192.168.1.2` hard-coded in `docker/docker-compose-ns.yml` — change it if the `ns` host IP changes.
- **Zabbix images** in `docker/docker-compose-mon.yml` pin `platform: linux/arm64` and Postgres uses `arm64v8/postgres:16-alpine`; the stack will not run on x86 without edits.
- **Watchtower** only updates containers carrying `com.centurylinklabs.watchtower.enable=true`. Any new service that should auto-update needs this label. Update windows are Saturday 09:00 (ns) and 08:00 (mon).
- The `postgres_dir` for Zabbix is created with `become: yes` in `mon-playbook.yml` because Postgres chowns it to its container UID — don't change that to `become: no`.

## Secrets

Every file listed in `.gitignore` at any level (`.env`, `.htpasswd`, `devices.ini`, `a-records.conf`) is a secret. Never commit them, never inline real values into examples. There is currently no ansible-vault usage; the `.env` files are shipped as plain `0600` files.
