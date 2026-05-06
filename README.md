# infra-roles-public

The Ansible roles I run on my 3-node Proxmox cluster. Three of them, anyway. The full role library is private; this is the slice that's safe to publish.

`node_exporter` for Prometheus metrics, `adguard` for DNS blocking, `traefik` for reverse proxy. The versions I actually run, pinned to the upstream releases I tested against.

> Companion repos: [`sjviklabs/infra-docs`](https://github.com/sjviklabs/infra-docs) for runbooks and the change log, [`sjviklabs/noc-planning`](https://github.com/sjviklabs/noc-planning) for the GSD phase plans.

## Roles

| Role | Purpose | Installs |
|------|---------|---------|
| [`node_exporter`](roles/node_exporter/) | Prometheus metrics agent | v1.7.0 from GitHub releases |
| [`adguard`](roles/adguard/) | Network-level DNS blocker | AdGuard Home v0.107.73 |
| [`traefik`](roles/traefik/) | Reverse proxy + Tailscale subnet router | Traefik v3.3.4, mkcert v1.4.4 |

## Design choices

**Version-gated installs.** Each role checks the installed binary version before downloading. Re-running is idempotent: nothing re-downloads if the version already matches. I got tired of waiting for installers to redo work they didn't need to.

**No config templating for live-state services.** Traefik routing rules and AdGuard DNS lists are managed at runtime, in the UI or by hand. The roles deploy the binary and the systemd unit, then assert the config directory exists. That's it. Ansible owns the lifecycle, the operator owns the config. Trying to template both fights you forever.

**Minimal privilege.** Dedicated system users with `nologin` shell. `CAP_NET_BIND_SERVICE` only where it's needed (Traefik and AdGuard, which want port 53 and 80/443).

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

## What this isn't

A general-purpose role library. These three roles do exactly what I need them to do on my hardware. If you run a different distro or a different Proxmox version they'll probably still work, but I haven't tested it and I won't promise.

## License

MIT. If a role saves you an afternoon, that's the point.
