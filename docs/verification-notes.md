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
