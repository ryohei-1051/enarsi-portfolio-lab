# Verification Notes

This file logs each baseline milestone (v0.x) with what was validated and where the supporting artifacts are stored.

---

## v0.1-dmvpn (DMVPN Phase 2 / single-hub)
- Topology: HQ1 (Hub) + DC1/BR1/BR2 (Spokes); underlay on a shared Ethernet segment (NBMA)
- Result:
  - NHRP registrations confirmed on HQ1
  - Tunnel reachability verified (Spokes → HQ1 Tunnel0: 10.10.10.1)
- Evidence: `evidence/baseline/v0.1-dmvpn/`
- Configs: `configs/baseline/v0.1-dmvpn/`
- Key checks:
  - HQ1: `show dmvpn`, `show ip nhrp`, `show int tunnel0`
  - Spokes: `show ip nhrp`, `ping 10.10.10.1`
- Notes:
  - ISP router is present in the topology but not required for v0.1
- Next: v0.2 add OSPF over DMVPN (hub-spoke adjacencies)

---

## v0.2-ospf (OSPF over DMVPN Phase 2)
- Result:
  - OSPF neighbors FULL between HQ1 and all spokes over Tunnel0
  - Loopback routes exchanged via OSPF (10.255.x.x/32)
  - Reachability validated with ping/traceroute (including spoke-to-spoke via HQ1 as expected for Phase 2)
- Evidence: `evidence/baseline/v0.2-ospf/`
- Configs: `configs/baseline/v0.2-ospf/`
- Key checks:
  - `show ip ospf neighbor`
  - `show ip ospf interface tunnel0`
  - `show ip route ospf`
  - `ping/traceroute` between loopbacks (e.g., BR1 → DC1/BR2)
- Note:
  - ip nhrp map multicast dynamic may not appear in show run; verify with show running-config all.
- Next: v0.3 add EIGRP over DMVPN (or compare OSPF vs EIGRP behavior)

---

## v0.3-eigrp (EIGRP over DMVPN Phase 2)
- Result:
  - EIGRP neighbors established between HQ1 and all spokes over Tunnel0
  - Loopback routes exchanged via EIGRP (10.255.x.x/32)
  - Reachability validated with ping/traceroute (spoke-to-spoke via HQ1 as expected for Phase 2)
- Evidence: `evidence/baseline/v0.3-eigrp/`
- Configs: `configs/baseline/v0.3-eigrp/`
- Key checks:
  - `show ip eigrp neighbors`
  - `show ip route eigrp`
  - `show ip protocols`
  - `ping/traceroute` between loopbacks (e.g., BR1 → DC1/BR2)
- Next: INC (EIGRP neighbor not forming / route filtering / split-horizon)

---

## v0.4-bgp (eBGP + inbound filtering + selective redistribution)
- Result:
  - eBGP established between HQ1 (AS65001) and ISP (AS65002) over 192.0.1.0/24
  - ISP advertised two external prefixes; HQ1 accepted only the allowed prefix via inbound policy
  - Allowed prefix redistributed into EIGRP and reached spokes; blocked prefix not reachable as intended
- Evidence: `evidence/baseline/v0.4-bgp/`
- Configs: `configs/baseline/v0.4-bgp/`
- Key checks:
  - HQ1: `show ip bgp summary`, `show ip bgp`, `show ip bgp neighbors <ISP> received-routes`
  - HQ1: `show ip eigrp topology 203.0.113.1/32` (BGP→EIGRP injection)
  - Spokes: `show ip route <allowed/blocked>`, `ping/traceroute` to allowed vs blocked prefix
- Notes:
  - `received-routes` shows both prefixes due to inbound soft reconfiguration; bestpath/RIB reflects the inbound filter.
- Next: v0.5 dual-hub / failover and convergence measurement

---

## v0.5a-dual-hub (Dual Hub + failover over DMVPN)
- Result:
  - HQ2 enabled as a secondary DMVPN hub; spokes registered to both hubs (NHRP)
  - EIGRP neighbors established to HQ1 and HQ2 over Tunnel0
  - Failover test: HQ1 Tunnel0 shutdown → reachability restored with HQ2 as the control-plane path
  - DMVPN Phase 2 shortcut formed for data-plane (direct spoke-to-spoke forwarding after NHRP resolution)
- Evidence: `evidence/baseline/v0.5a-dual-hub/`
- Configs: `configs/baseline/v0.5a-dual-hub/`
- Key checks:
  - `show ip eigrp neighbors` (HQ1 down, HQ2 remains)
  - `show ip route <remote loopback>` (route sourced via HQ2)
  - `traceroute` + `ping repeat` (reachability before/after failover)
  - `show ip nhrp` / `show dmvpn` (NHRP registration + shortcut evidence)
- Convergence (indicator only):
  - Convergence time was recorded as a rough indicator only; results varied between runs in the GNS3 environment.
  - Validation focuses on path switch + restored reachability after failover.
- Next: v0.5b external failover (BGP on HQ2) or convergence tuning (SLA/track)

## v0.5b-external-failover (BGP on HQ2 + external reachability failover)
- Result:
  - HQ2 established eBGP with ISP and applied inbound policy (allowed prefix accepted; blocked prefix rejected)
  - Allowed prefix redistributed into EIGRP from HQ2 and reachable from spokes
  - External reachability to the allowed prefix remained available during HQ1 shutdown (failover validated)
- Evidence: `evidence/baseline/v0.5b-external-failover/`
- Configs: `configs/baseline/v0.5b-external-failover/`
- Key checks:
  - HQ2: `show ip bgp summary`, `show ip bgp`, `received-routes`, `show ip eigrp topology 203.0.113.1/32`
  - BR1: allowed ping success + blocked ping failure, `show ip route` (allowed present / blocked absent)
- Convergence (indicator only):
  - Timing may vary in GNS3; validation focuses on policy correctness + reachability during failover.
- Next: repository review + (optional) SLA/track and convergence tuning

## v0.6-sla-track (IP SLA + track based failover control on BR1)
- Result:
  - BR1 monitors HQ1 reachability using IP SLA (1s frequency) and track
  - Primary default route via HQ1 is installed only when track is up
  - When HQ1 becomes unreachable, BR1 fails over to backup default via HQ2 automatically
  - Upon recovery, BR1 returns to the primary path
- Evidence: `evidence/baseline/v0.6-sla-track/`
- Configs: `configs/baseline/v0.6-sla-track/`
- Key checks:
  - `show ip sla statistics 10`, `show track 10`
  - `show ip route 0.0.0.0` (next-hop changes HQ1 → HQ2 → HQ1)
  - `ping/traceroute 203.0.113.1` (reachability during failover)
- Notes:
  - Convergence timing is treated as an indicator only (GNS3 variability); validation focuses on deterministic control logic.
