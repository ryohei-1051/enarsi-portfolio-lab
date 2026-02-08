# Status: Week 1 skeleton completed. Baseline (v0.1) complete.
# ENARSI Portfolio Lab: Branch-to-Enterprise Routing & Troubleshooting

## 0. TL;DR (One-minute overview)
This repository is a networking portfolio built around **CCNP ENARSI-level routing**.  
I use **GNS3 (primary)** and **Packet Tracer (supporting)** to design, implement, verify, and troubleshoot a branch-to-enterprise topology using **OSPF and BGP**.

**What you’ll find here**
- A reproducible lab that covers **OSPF (multi-area)** and **BGP (eBGP/iBGP)**, including **redistribution** and **policy-based path control**
- A growing set of **incident runbooks** (failure scenarios) with a consistent format:  
  **Symptom → Investigation (commands + evidence) → Root cause → Fix → Verification → Prevention**
- **Configs and evidence** (show outputs / verification notes) stored in a way that supports repeatable troubleshooting practice

**Why it matters**
- The focus is not just “making it work,” but proving operational skills:  
  **design → build → verify → break intentionally → troubleshoot → prevent recurrence**

### Deliverables (What this portfolio proves)
- **Incident documentation:** 10+ ENARSI-style routing incidents with evidence and prevention notes
- **Reproducible configs:** baseline and per-incident before/after configs (no proprietary images included)
- **Operational artifacts:** verification checklist, change plan, and rollback approach to mirror real operations

### Quick links
- Start here: [docs/routing-design.md](docs/routing-design.md)
- Baseline verification: [docs/verification-checklist.md](docs/verification-checklist.md)
- Recommended show commands: [docs/command-set.md](docs/command-set.md)
- Incident index: [incidents/README.md](incidents/README.md)
- Ops artifacts: [ops/README.md](ops/README.md)


---

## 1. Learning Goals (ENARSI Mapping)
- **Routing protocols:** OSPF / BGP  
- **Path control:** route-map / prefix-list / community / local-preference / MED  
- **Redistribution:** OSPF ↔ BGP (including route tagging)  
- **Troubleshooting:** disciplined use of show/debug, documented troubleshooting workflows  
- **Operations:** change plan / rollback / verification

---

## 2. Lab Overview

### 2.1 Topology
- Diagram: `docs/topology.png`
- Device roles:
  - **BR** (Branch)
  - **HQ** (Headquarters)
  - **DC** (Data Center)
  - **ISP** (Transit)
  - *(Optional)* FW zones / DMZ

### 2.2 IP Plan
- Addressing table: `docs/ip-plan.md`
- Loopback design
- Point-to-point links
- Summarization boundaries

### 2.3 Routing Design Summary
- OSPF area design (Area 0 / non-backbone areas)
- BGP design (ASNs, iBGP/eBGP, Route Reflector optional)
- Redistribution strategy (where/what/filters/tags)
- Default route policy (who originates, who consumes)

---

## 3. Build Guide (How to Reproduce)

### 3.1 Prerequisites
### Tools
- **GNS3 (primary):** used for the main lab and ENARSI-level incident reproduction/troubleshooting
- **Packet Tracer (supporting):** used for simplified demonstrations and visual explanations where helpful
- Router images / versions (as applicable)
- Repository structure overview

### 3.2 Step-by-step Configuration
- Step 1: Base config (interfaces, hostname, AAA, NTP)
- Step 2: OSPF configuration
- Step 3: BGP configuration
- Step 4: Redistribution
- Step 5: Policy control (route-map / prefix-list)
- Step 6: *(Optional)* IP SLA / tracking / failover
- Step 7: Save / backup configs

> Configs are stored in: `configs/`

---

## 4. Verification (Proof It Works)

### 4.1 Baseline Verification Checklist
- Neighbor status (OSPF/BGP)
- Expected routing table entries
- End-to-end reachability (ping/traceroute)
- Path preference validation
- Convergence / failover checks

### 4.2 Evidence
- Command outputs: `evidence/`
- Screenshots: `docs/screenshots/`
- Notes: `docs/verification-notes.md`

---

## 5. Troubleshooting Playbook (How I Troubleshoot)

### 5.1 General Approach (Decision Tree)
- Symptom → layer hypothesis → test → narrow down → fix → verify → prevent

### 5.2 Standard Commands (Cisco IOS-style examples)
- `show ip route` / `show ip cef` / `show ip protocols`
- `show ip ospf neighbor` / `show ip ospf database`
- `show ip bgp summary` / `show ip bgp` / `show ip bgp neighbors`
- `show run | section ...`
- `traceroute` / `ping` (including extended ping)
- `debug` (only when necessary; use conditions and note risks)

---

## 6. Incidents (Failure Scenarios)
- Index: `incidents/README.md`
- Each incident includes:
  - Symptom
  - Expected vs Actual
  - Investigation steps + outputs
  - Root cause
  - Fix
  - Verification
  - Prevention / hardening

---

## 7. Change Management Artifacts
- Change plan template: `ops/change-plan-template.md`
- Rollback plan: `ops/rollback.md`
- Post-change verification checklist: `ops/post-change-checklist.md`

---

## 8. Lessons Learned
- Top 5 learnings (routing / policy / operations)
- What I would improve next

---

## 9. Roadmap (Next Enhancements)
- Add Route Reflector, DMVPN, dual ISP, FW zones, monitoring
- Add Splunk/SIEM-style log view *(optional)*

---

## 10. Repository Structure
- `/docs`
- `/configs`
- `/evidence`
- `/incidents`
- `/ops`

---

## 11. License / Disclaimer
- Educational use only. No production secrets or proprietary images are included.

---

## Baseline verification
- Checklist: `docs/verification-checklist.md`
- Recommended show command set: `docs/command-set.md`
