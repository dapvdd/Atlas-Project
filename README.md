# ATLAS PROJECT

> Infrastructure & Network Engineering Lab

Atlas is a hands-on infrastructure and networking laboratory built around a virtualized Ubuntu server environment. The project documents the engineering process day by day: topology changes, configuration, experiments, troubleshooting, and lessons learned.

## Progress

| Day | Status | Focus |
|---|---|---|
| Day 01–09 | 📝 To be migrated | Previous engineering logs and documentation |
| Day 10 | ✅ Completed | Docker fundamentals, images, containers, Nginx, port publishing |
| Day 11 | ✅ Completed | Container persistence/restart behavior and Docker service recovery |
| Day 12 | 🚧 In progress | Docker bridge networking, container connectivity, NAT/masquerading |

## Repository Structure

```text
Atlas-Project/
├── README.md
├── Engineering-Log/
│   └── Day-XX.md
├── Documentation/
│   └── Day-XX.md
├── Topology/
└── Config/
```

## Engineering Log

The engineering log records what was actually done during each lab session, including commands, observations, troubleshooting, decisions, and conclusions.

## Documentation

Documentation contains the cleaned-up technical reference for the resulting configuration and architecture. It is intentionally separated from the raw engineering log.

## Current Lab

**Day 12 — Docker Networking**

Current environment includes:

- Ubuntu server `SRV-UBU-01`
- `enp0s3` — LAB-LAN (`192.168.10.2/28`)
- `enp0s8` — Host-Only / management (`192.168.56.103/24`)
- Docker bridge `docker0` — `172.17.0.1/16`
- `atlas-nginx` container — `172.17.0.2`

Verified during Day 12:

- Host → container connectivity
- Container → host connectivity
- Container → Internet connectivity
- Docker bridge routing
- Docker `MASQUERADE` rule for `172.17.0.0/16`
