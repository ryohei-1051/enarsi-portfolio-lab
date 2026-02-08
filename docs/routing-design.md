# Routing Design (ENARSI Portfolio Lab)

## 1. Design goals
- **Primary goal:** Prove ENARSI-level routing skills with repeatable incidents and evidence.
- **Operational goal:** Make the lab easy to rebuild, verify, break intentionally, and recover.
- **Constraints:** No proprietary images; configs and evidence are stored as text.

## 2. Topology summary
- **Model:** Branch → HQ → DC with ISP/Transit (and optional DMZ/FW zones)
- **Device roles:**
  - **BR**: branch edge
  - **HQ**: enterprise hub
  - **DC**: data center edge/core
  - **ISP**: transit/peering simulation

> Diagram: `docs/topology.png`

## 3. Addressing plan
- **Loopbacks:** Used for router-id and stable endpoints for reachability tests
- **P2P links:** /30 or /31 (state what you chose)
- **Summarization boundaries:** (where summaries are applied and why)

> Full table: [docs/ip-plan.md](docs/ip-plan.md)

## 4. Protocol design

### 4.1 OSPF design
- **Process ID:** (e.g., 10)
- **Areas:** (e.g., Area 0 + one non-backbone area)
- **Router-ID:** Lo0
- **Network types:** (p2p / broadcast) and why
- **Authentication:** (none / simple / message-digest) and scope
- **Summarization:** (ABR summaries if used)
- **Default route:** (if OSPF learns or originates default)

**Verification (OSPF)**
- Neighbors: `show ip ospf neighbor` (FULL)
- Interfaces: `show ip ospf interface <if>`
- Database: `show ip ospf database` (optional)

### 4.2 BGP design
- **ASNs:** (list ASNs used for BR/HQ/DC/ISP)
- **Sessions:** (eBGP/iBGP, which devices peer)
- **Transport details:** (update-source / multihop if used)
- **Next-hop handling:** (e.g., next-hop-self where appropriate)
- **Route Reflector:** (yes/no, and why)

**Verification (BGP)**
- Session: `show ip bgp summary` (Established)
- Routes: `show ip bgp` / `show ip bgp neighbors <x> received-routes` (as applicable)

### 4.3 Redistribution strategy (OSPF ↔ BGP)
- **Where redistribution happens:** (which device(s))
- **What is redistributed:** (which prefixes, direction)
- **Controls:** route-map / prefix-list
- **Tagging:** (tag strategy to prevent loops)
- **Metrics:** (OSPF metric type, BGP attributes if applicable)

**Verification (Redistribution)**
- Expected routes appear with the right attributes/metrics
- No route feedback loops (stable routing table)

### 4.4 Policy-based path control
- **Goal:** (primary/backup path, traffic engineering, etc.)
- **Tools used:** local-preference / MED / AS-path prepending / communities / route-map
- **Expected outcome:** (which path is preferred and under what conditions)

**Verification (Policy)**
- Compare bestpath selection and actual forwarding path:
  - `show ip bgp <prefix>`
  - `traceroute <dest>`

## 5. Operational practices
### 5.1 Baseline verification
- Checklist: `docs/verification-checklist.md`
- Command set: `docs/command-set.md`

### 5.2 Change / rollback approach
- Change plan: `ops/change-plan-template.md`
- Rollback plan: `ops/rollback.md`
- Post-change verification: `ops/post-change-checklist.md`

## 6. Incident design principles
- **One incident = one primary failure mode**
- **Break scope is small:** 1–3 lines of misconfig
- **Evidence first:** symptom → investigation → fix/verify
- **Prevention note:** one concrete operational guardrail per incident

## 7. Roadmap
- (e.g.) Add RR / dual ISP / DMVPN / monitoring
- 
