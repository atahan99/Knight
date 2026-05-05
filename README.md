# Ansible home media server / NAS

An Ansible playbook that sets up an Ubuntu-based home media server and NAS with security hardening, auto-updates, e-mail notifications for S.M.A.R.T., Docker-based apps, and optional WireGuard.

It assumes a fresh Ubuntu Server 22.04 install, a non-root user with sudo, and SSH key access (or a one-time password via environment variable for bootstrap).

## Project lineage

- **Original project:** First commits in this repository date to **2022-08-23** (initial “Add files via upload”–style import of the home-server Ansible work).
- **Cleaned release:** **2026-05-05** — security and hygiene pass: removed committed secrets and host-specific inventory, moved sensitive values to environment variables, fixed broken SSH port logic and Traefik configuration, pinned and corrected Traefik/Docker settings, and refreshed this README.

If this playbook or any fork of it was ever public with real credentials, **rotate those credentials** at the provider (they should not live in git).

## Possible next steps

| Area | Idea |
|------|------|
| Supply chain | Pin Galaxy roles in `requirements.yml` (avoid floating `master` for roles like `stuvusit.smartd` / `chriswayg.msmtp-mailer` where possible). |
| Quality | Add `ansible-lint` and fix high-signal issues (e.g. replace deprecated `apt_key` where roles still use it). |
| CI | GitHub Actions: install Galaxy roles/collections and run `ANSIBLE_NAS_PASSWORD=dummy ansible-playbook run.yml --syntax-check`. |
| Docs | Optional `SECURITY.md` for how to report issues. |
| OS | When you care about it, validate against **Ubuntu 24.04** and adjust `group_vars` codename / `version` as needed. |

## Special thanks

- David Stephens for [Ansible NAS](https://github.com/davestephens/ansible-nas).
- Wolfgang for [infra](https://github.com/notthebee/infra).
- TylerAlterio for the [mergerfs](https://github.com/tyalt1/mediaserver/tree/master/roles/mergerfs) role.
- Jake Howard and Alex Kretzschmar for the [snapraid](https://github.com/RealOrangeOne/ansible-role-snapraid) role.

## Services (container roles in this repo)

**Media:** Plex, Jellyfin, Radarr, Sonarr, Jackett, Deluge, Bazarr, Calibre, Openbooks, YouTube-dl material.

**Apps:** Heimdall, Dashy, Portainer, Traefik (optional), Watchtower, WireGuard (optional).

**System:** Cockpit, Samba (when NAS features are enabled), node_exporter, smartd, hd-idle, Neovim.

PiHole is mentioned in some docs but is not applied by the current `run.yml` (no PiHole role in this tree).

## Other features

- Optional MergerFS with Snapraid (commented in `run.yml` / `group_vars`)
- Samba shares when `enable_nas_stuff` / `enable_samba` are true

## Prerequisites

- Ansible 2.14+ recommended
- Python 3 on the control node

## Usage

Install Ansible (macOS example):

```bash
brew install ansible
```

Install Galaxy roles and collections:

```bash
ansible-galaxy install -r requirements.yml
ansible-galaxy collection install -r collections/requirements.yml
```

Inventory: copy the example and edit for your host. By default [`ansible.cfg`](ansible.cfg) uses `hosts.example` so a clone passes `--syntax-check` without a private file. For real runs, copy and point Ansible at your own inventory:

```bash
cp hosts.example hosts
# edit hosts, then either:
ansible-playbook run.yml -i hosts -K
# or set INVENTORY = hosts in ansible.cfg
```

Run the playbook (sudo password prompt only needed until passwordless sudo is configured):

```bash
ansible-playbook run.yml -K
```

## Environment variables

| Variable | Purpose |
|----------|---------|
| `ANSIBLE_NAS_PASSWORD` | **Required** for the `system` role: Linux login password for `username` (hashed on the target). Prefer Ansible Vault for production. |
| `GITHUB_TOKEN` | Optional: higher GitHub API rate limits for `community.general.github_release` (Neovim from source, process-exporter, hd-idle). Without it, Neovim installs from distro packages. |
| `VPN_PASSWORD` | Optional: used where `vpn_password` is referenced in vars. |
| `ANSIBLE_SSH_PASSWORD` | Optional one-time bootstrap if SSH keys are not on the target yet (not stored in repo). |
| `WIREGUARD_SERVER_URL` | Public hostname or IP for the WireGuard container `SERVERURL` (defaults to placeholder in `group_vars` if unset). |
| `VERCEL_API_TOKEN` | Required **only if** `traefik_enabled: true`: DNS challenge with Vercel. |
| `TRAEFIK_DASHBOARD_BASICAUTH` | Required **only if** Traefik role runs: one htpasswd line; double each `$` for YAML (see Traefik docs). |
| `TRAEFIK_ACME_EMAIL` | Optional: Let's Encrypt registration email (defaults in role if unset). |

Override `traefik_public_domain` and related defaults in `group_vars` or extra-vars when enabling Traefik.

## Enabling optional components

Set variables in `group_vars/all/vars.yml` or host vars (for example `plex_enabled: true`, `traefik_enabled: true`). See `docs/` for per-app notes.
