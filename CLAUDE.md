# infra-roles-public — Claude memory

## What this repo is

A curated slice of production Ansible roles from the SJVIK NOC — a 3-node Proxmox HA homelab running 14+ LXC services. Public, MIT-licensed.

This is a **public artifact repo** (Tier 1 standalone per REPO-STANDARD §7). Useful as a tool whether or not anyone reads any SJVIK Labs book. Backs the Proxmox book and the Claude Code DevOps book.

## What's in here

Three roles:
- `node_exporter` — Prometheus metrics agent (v1.7.0)
- `adguard` — DNS blocker (AdGuard Home v0.107.73)
- `traefik` — Reverse proxy (v3.3.4)

These three are the public slice. Full role library is private at `sjviklabs/infra-ansible`.

## Conventions

- **Idempotent:** every role can run repeatedly without breaking state
- **Version-pinned:** all upstream tools pinned in `defaults/main.yml`
- **Debian 12 / Proxmox 8** target — roles assume this stack
- **One role per LXC service** — don't merge multiple services into one role
- **Templates:** Jinja2, kept minimal
- **Conventional Commits** (`feat:`, `fix:`, `docs:`, `chore:`)

## Common commands

```bash
# Lint
ansible-lint roles/

# Syntax check (no execution)
ansible-playbook --syntax-check site.yml

# Dry run (see what would change)
ansible-playbook -i inventory --check --diff site.yml

# Apply
ansible-playbook -i inventory site.yml
```

## OPSEC note

This repo is public. Do not commit:
- Inventory files with real hostnames or IPs
- `vars/secrets.yml` or anything with credentials (use Ansible Vault locally)
- SSH keys or `ansible_ssh_pass` values
- Internal CIDR ranges that map to live SJVIK NOC topology

When adding a new role, scrub any production-specific defaults (real domain names, real LDAP servers, real SMTP relays) before pushing.

## Cross-repo

- Full Ansible IaC: private `sjviklabs/infra-ansible`
- NOC state docs: private `sjviklabs/infra-docs`
- Planning: private `sjviklabs/noc-planning` (also `~/.claude/`)
- The Claude Code DevOps book references roles from this repo

## Standard

Per [SJVIK Labs Repo Standard](https://github.com/sjviklabs/.github) §1 + §9:
- **Status:** Stable (v1.0.0)
- License: MIT
- Branch protection on `main`
- Dependabot + secret scanning + push protection enabled
