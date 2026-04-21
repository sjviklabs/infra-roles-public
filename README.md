# infra-roles-public

A slice of production Ansible roles from the **SJVIK NOC** — a 3-node Proxmox HA homelab running 14+ LXC services. Roles here are extracted from the private [`sjviklabs/infra-ansible`](https://github.com/sjviklabs/infra-ansible) playbook, sanitized for public use.

> Companion repos: [`sjviklabs/infra-docs`](https://github.com/sjviklabs/infra-docs) (runbooks + change log), [`sjviklabs/noc-planning`](https://github.com/sjviklabs/noc-planning) (GSD phase plans).

## Roles

| Role | Purpose | Installs |
|------|---------|---------|
| [`node_exporter`](roles/node_exporter/) | Prometheus metrics agent | v1.7.0 from GitHub releases |
| [`adguard`](roles/adguard/) | Network-level DNS blocker | AdGuard Home v0.107.73 |
| [`traefik`](roles/traefik/) | Reverse proxy + Tailscale subnet router | Traefik v3.3.4, mkcert v1.4.4 |

## Design choices

**Version-gated installs** — each role checks the installed binary version before downloading. Re-running is idempotent: no re-downloads if the version already matches.

**No config templating for live-state services** — Traefik routing rules and AdGuard DNS lists are managed at runtime (via UI or manual edits). The roles deploy the binary and systemd unit, then assert that the config directory exists. This is intentional: Ansible converges the service lifecycle, not the operator-owned config.

**Minimal privilege** — dedicated system users with `nologin` shell; only `CAP_NET_BIND_SERVICE` granted where needed (Traefik, AdGuard).

## Usage

```yaml
# requirements.yml (if consuming via Galaxy in future)
# For now: clone this repo and point roles_path at it

# playbook.yml
- hosts: monitoring
  roles:
    - role: node_exporter
      vars:
        node_exporter_version: "1.8.0"  # override default

- hosts: dns
  roles:
    - role: adguard

- hosts: proxy
  roles:
    - role: traefik
```

Point `roles_path` at the `roles/` directory:
```ini
# ansible.cfg
[defaults]
roles_path = /path/to/infra-roles-public/roles
```

## Tested on

- Debian 12 (Bookworm) LXC containers on Proxmox 8.x
- Ansible 2.17+

## License

MIT
