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

## v0.5-dual-hub (Dual Hub + failover over DMVPN)
- Result:
  - HQ2 enabled as a secondary DMVPN hub; spokes registered to both hubs (NHRP)
  - EIGRP neighbors established to HQ1 and HQ2
  - Failover test: HQ1 Tunnel0 shutdown → spoke-to-spoke reachability restored via HQ2
  - Recovery: HQ1 restored and preference returned to primary
- Evidence: `evidence/baseline/v0.5-dual-hub/`
- Configs: `configs/baseline/v0.5-dual-hub/`
- Key checks:
  - `show dmvpn`, `show ip nhrp`, `show ip eigrp neighbors`
  - `traceroute` path change (HQ1 → HQ2)
  - `ping repeat` output as convergence evidence
- Next: v0.5b external failover (BGP on HQ2) or v0.6 convergence tuning (SLA/track)
