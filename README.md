# ENARSI Portfolio Lab: DMVPN Routing & Troubleshooting (GNS3)

# Status: Week 1 skeleton complete | Baseline v0.1 (DMVPN Phase 2) complete | Baseline v0.2 (OSPF over DMVPN) complete | Next: v0.3 (EIGRP over DMVPN)

# ENARSI Portfolio Lab: DMVPN Routing & Troubleshooting

This repository is a networking portfolio built around **CCNP ENARSI-level routing & troubleshooting**.
The lab is built in **GNS3 (primary)** with supporting **Packet Tracer** artifacts where helpful.

---

## 0) TL;DR (One-minute overview)

**What you’ll find here**
- A **reproducible DMVPN Phase 2** hub-and-spoke foundation (HQ1 hub + DC1/BR1/BR2 spokes)
- Baseline milestones with **versioned configs + evidence** (show outputs / ping / traceroute)
- A growing set of **incident runbooks** with a consistent format:  
  **Symptom → Investigation (commands + evidence) → Root cause → Fix → Verification → Prevention**

**Why it matters**
- The focus is not just “making it work,” but proving operational skills:  
  **design → build → verify → break intentionally → troubleshoot → prevent recurrence**

### Baselines (Versioned milestones)
| Baseline | Goal | Configs | Evidence | Notes |
|----|----|----|----|----|
| v0.1-dmvpn  | DMVPN Phase 2 up; NHRP registration; tunnel reachability | `configs/baseline/v0.1-dmvpn/` | `evidence/baseline/v0.1-dmvpn/` | DMVPN foundation |
| v0.2-ospf   | OSPF neighbors FULL over Tunnel0; loopbacks exchanged; reachability verified | `configs/baseline/v0.2-ospf/` | `evidence/baseline/v0.2-ospf/` | OSPF over DMVPN |

### Quick links
- Start here (design): [docs/routing-design.md](docs/routing-design.md)
- IP plan: [docs/ip-plan.md](docs/ip-plan.md)
- Baseline verification checklist: [docs/verification-checklist.md](docs/verification-checklist.md)
- Recommended show commands: [docs/command-set.md](docs/command-set.md)
- Verification notes (baseline log): [docs/verification-notes.md](docs/verification-notes.md)
- Incident index: [incidents/README.md](incidents/README.md)
- Ops artifacts: [ops/README.md](ops/README.md)

---

## 1) Learning Goals (ENARSI Mapping)
- **DMVPN foundation:** mGRE + NHRP (Phase 2), repeatable overlay operations
- **Routing protocols:** OSPF (current) / BGP (planned)
- **Path control:** route-map / prefix-list / community / local-preference / MED (planned)
- **Troubleshooting:** disciplined use of show/debug, documented workflows with evidence
- **Operations:** change plan / rollback / verification

---

## 2) Lab Overview

### 2.1 Topology
- Diagram: `docs/topology.png`
- Roles:
  - **HQ1** (Hub)
  - **DC1 / BR1 / BR2** (Spokes)
  - **ISP** (Optional / future)

### 2.2 IP Plan
- Addressing table: `docs/ip-plan.md`
- Underlay (NBMA): shared Ethernet segment
- Overlay (Tunnel0): DMVPN /24
- Loopbacks (/32) for routing validation

### 2.3 Design Summary
- Underlay: shared L2 segment for NBMA reachability
- Overlay: DMVPN Phase 2 (hub-and-spoke baseline)
- Dynamic routing over overlay: OSPF (v0.2), additional protocols planned

---

## 3) Build Guide (How to Reproduce)

### 3.1 Tools
- **GNS3 (primary):** baseline builds and incident reproduction/troubleshooting
- **Packet Tracer (supporting):** simplified demonstrations / visual explanations

### 3.2 Step-by-step (Baselines)
- v0.1: DMVPN Phase 2 (NHRP + tunnel reachability)
- v0.2: OSPF over DMVPN (neighbors FULL + route exchange + reachability)
- Save artifacts each time:
  - Configs → `configs/baseline/<version>/`
  - Evidence → `evidence/baseline/<version>/`

---

## 4) Verification (Proof It Works)
- Checklist: `docs/verification-checklist.md`
- Show command set: `docs/command-set.md`
- Evidence folders: `evidence/baseline/`

---

## 5) Troubleshooting Playbook (How I Troubleshoot)
- Symptom → hypothesis → test → narrow down → fix → verify → prevent
- Standard commands:
  - `show ip int brief`
  - `show dmvpn` / `show ip nhrp`
  - `show ip ospf neighbor` / `show ip ospf interface tunnel0`
  - `show ip route` / `show ip route ospf`
  - `ping` / `traceroute`
  - `show run | section ...`

---

## 6) Incidents (Failure Scenarios)
- Index: `incidents/README.md`
- Each incident includes:
  - Symptom / Expected vs Actual
  - Investigation steps + evidence
  - Root cause / Fix / Verification
  - Prevention / hardening

---

## 7) Change Management Artifacts
- Change plan: `ops/change-plan-template.md`
- Rollback: `ops/rollback.md`
- Post-change verification: `ops/post-change-checklist.md`

---

## 8) Roadmap (Next Enhancements)
- v0.3: **EIGRP over DMVPN** (compare OSPF vs EIGRP behavior)
- Add BGP (eBGP/iBGP), redistribution, and policy control
- Add dual-hub / failover (Hub2 enablement), timers, and convergence tests
- (Optional) Monitoring/logging view (SIEM-style)

---

## 9) License / Disclaimer
Educational use only. No production secrets or proprietary images are included.
