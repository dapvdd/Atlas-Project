# Atlas Project

> Infrastructure & Network Engineering Lab

**Mission:**  
Build an Enterprise Homelab from Scratch

Atlas is a hands-on infrastructure and networking laboratory built around a virtualized Ubuntu server environment. The project documents the engineering process day by day: topology changes, configuration, experiments, troubleshooting, decisions, and lessons learned.

## Repository Structure

```text
Atlas-Project/
├── README.md
├── Documentation/
│   ├── Engineering Log/
│   ├── Screenshots/
│   ├── Topology/
│   └── Software Inventory.txt
├── ISO/
└── Tools/
```

`ISO/` and `Tools/` are kept locally and excluded from Git tracking.

## Engineering Log

The engineering log records the actual development process of Atlas, including:

- Commands executed
- Configuration changes
- Observations
- Troubleshooting
- Experiments
- Decisions
- Lessons learned

Each day is documented chronologically in `Documentation/Engineering Log/`.

## Documentation

Technical documentation, screenshots, topology diagrams, and supporting references are stored under `Documentation/`.

The Engineering Log focuses on the development process, while the supporting documentation provides a reference for the resulting configuration and architecture.

## Current Lab

**Day 12 — Docker Networking**

Current environment:

- Ubuntu Server: `SRV-UBU-01`
- `enp0s3`: `192.168.10.2/28`
- `enp0s8`: `192.168.56.103/24`
- Docker bridge: `docker0`
- Docker gateway: `172.17.0.1/16`
- `atlas-nginx`: `172.17.0.2`

### Verified

- Host → Container connectivity
- Container → Host connectivity
- Container → Internet connectivity
- Docker bridge routing
- Docker `MASQUERADE` for `172.17.0.0/16`
