# Security Policy

## Reporting a vulnerability

Email **security@stevenjvik.tech** with a reproducer. Expect acknowledgement within 72 hours. Do not open a public issue.

## Scope

Reportable issues:
- Privilege escalation from role misuse (e.g. overly permissive file modes, capability grants beyond what is documented)
- Credential or secret exposure introduced by a role task

## Out of scope

- Configuration choices (DNS blocklist selection, Traefik routing) — those are operator decisions
- Upstream vulnerabilities in installed software (AdGuard, Traefik, node_exporter) — report to the respective projects

## Supported versions

Only the latest commit on `main` is supported.
