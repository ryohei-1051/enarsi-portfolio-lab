# enarsi-portfolio-lab
ENARSI-focused routing lab (OSPF/BGP) with incident runbooks, evidence, and reproducible configs (GNS3/Packet Tracer)

### 0. TL;DR (1分で分かる概要)
- What: （例）OSPF multi-area + BGP (eBGP/iBGP) + redistribution + policy control
- Why: （例）ENARSIスキルを “設計→実装→検証→障害対応→再発防止” で証明
- Result: （例）障害シナリオ10本 / Runbook / 設定・検証手順を整備

### 1. Learning Goals (ENARSI Mapping)
- Routing protocols: OSPF / BGP
- Path control: route-map / prefix-list / community / local-preference / MED
- Redistribution: OSPF↔BGP (tagging含む)
- Troubleshooting: show/debugの使い分け、切り分け手順化
- Operations: change plan / rollback / verification

### 2. Lab Overview
#### 2.1 Topology
- Diagram: `docs/topology.png`
- Device roles:
  - BR (Branch)
  - HQ (Headquarters)
  - DC (Data Center)
  - ISP (Transit)
  - (Optional) FW zone / DMZ

#### 2.2 IP Plan
- Addressing table: `docs/ip-plan.md`
- Loopback design
- Point-to-point links
- Summarization boundaries

#### 2.3 Routing Design Summary
- OSPF areas design (Area 0 / non-backbone)
- BGP design (AS numbers, iBGP/eBGP, RR有無)
- Redistribution strategy (where/what/filters/tags)
- Default route policy (who originates, who consumes)

### 3. Build Guide (How to Reproduce)
#### 3.1 Prerequisites
- Tools: (例) EVE-NG / CML / GNS3 / Packet Tracer (※使ったものを書く)
- Images / versions (可能な範囲で)
- Repository structure

#### 3.2 Step-by-step Configuration
- Step 1: Base config (interfaces, hostname, AAA, NTP)
- Step 2: OSPF configuration
- Step 3: BGP configuration
- Step 4: Redistribution
- Step 5: Policy control (route-map/prefix-list)
- Step 6: (Optional) IP SLA / tracking / failover
- Step 7: Save/backup configs

> Configs are stored in: `configs/`

### 4. Verification (Proof it Works)
#### 4.1 Baseline Verification Checklist
- Neighbor status (OSPF/BGP)
- Routing table expected entries
- End-to-end reachability (ping/traceroute)
- Path preference checks
- Convergence / failover checks

#### 4.2 Evidence
- Command outputs: `evidence/`
- Screenshots: `docs/screenshots/`
- Notes: `docs/verification-notes.md`

### 5. Troubleshooting Playbook (How I Troubleshoot)
#### 5.1 General Approach (Decision Tree)
- Symptom → Layer guess → Hypothesis → Test → Narrow down → Fix → Verify → Prevent

#### 5.2 Standard Commands (Cisco-style例)
- show ip route / cef / protocols
- show ip ospf neighbor / database
- show ip bgp summary / bgp / neighbors
- show run | section ...
- traceroute / ping extended
- debug（使うなら条件・注意点も書く）

### 6. Failure Scenarios (10 incidents)
- Index: `incidents/README.md`
- Each incident contains:
  - Symptom
  - Expected vs Actual
  - Investigation steps + outputs
  - Root cause
  - Fix
  - Verification
  - Prevention / hardening

### 7. Change Management Artifacts
- Change plan template: `ops/change-plan-template.md`
- Rollback plan: `ops/rollback.md`
- Post-change verification checklist: `ops/post-change-checklist.md`

### 8. Lessons Learned
- Top 5 learnings (routing / policy / ops)
- What I would improve next

### 9. Roadmap (Next Enhancements)
- Add RR / add DMVPN / add dual ISP / add FW zones / add monitoring
- Add Splunk/SIEM-style log view (optional)

### 10. Repository Structure
- `/docs`
- `/configs`
- `/evidence`
- `/incidents`
- `/ops`

### 11. License / Disclaimer
- Educational use only, no production secrets

# Baseline Verification Checklist（docs/verification-checklist.md）
 show ip int brief で対象IF up/up
 ping リンク対向IPが疎通
 show ip ospf neighbor が FULL
 show ip ospf interface <if>（area/auth/hello-dead/MTU）
 show ip route ospf で期待経路が載る
 traceroute で期待パス
 （任意）リンク断/復旧で収束確認（IP SLA/track等がある場合）

# 推奨 show コマンドセット（毎回この順で保存）
状態：show ip int brief
OSPF：show ip ospf neighbor / show ip ospf interface <if> / show ip ospf
経路：show ip route / show ip route ospf
転送：traceroute <dest> / ping <dest>
設定差分：show run interface <if> / show run | section router ospf
