# Recommended Show Command Set (Evidence Capture Order)

Purpose: capture consistent evidence in a fixed order so outputs are comparable across baselines.

---

## v0.1 (DMVPN Phase 2)
### HQ1 (Hub)
- `show ip int brief`
- `show interface tunnel0`
- `show dmvpn`
- `show ip nhrp`
- `show run interface tunnel0`
- (Hub, verify if needed) `show running-config all | section interface Tunnel0`

### Spokes (DC1 / BR1 / BR2)
- `show ip int brief`
- `show interface tunnel0`
- `show dmvpn`
- `show ip nhrp`
- `ping 10.10.10.1`

---

## v0.2 (OSPF over DMVPN)
- (HQ1 only, optional / run when overlay issues suspected) `show dmvpn`
- (HQ1 only, optional / run when overlay issues suspected) `show ip nhrp`
- `show ip int brief`
- `show ip ospf neighbor`
- `show ip ospf interface tunnel0`
- `show ip route ospf`
- `ping <remote loopback>`
- `traceroute <remote loopback>`
- `show run interface tunnel0`
- `show run | section router ospf`

---

## v0.3 (EIGRP over DMVPN)
- (HQ1 only, optional / run when overlay issues suspected) `show dmvpn`
- (HQ1 only, optional / run when overlay issues suspected) `show ip nhrp`
- `show ip int brief`
- `show ip eigrp neighbors`
- `show ip route eigrp`
- `show ip protocols`
- `ping <remote loopback>`
- `traceroute <remote loopback>`
- `show run interface tunnel0`
- `show run | section router eigrp`

---

## v0.4-bgp (eBGP + inbound filter + selective redistribution into EIGRP)
### HQ1
- `show ip int brief`
- `show ip bgp summary`
- `show ip bgp`
- `show ip bgp neighbors 192.0.1.2 received-routes`
- `show ip eigrp topology 203.0.113.1/32`
- `show ip route 203.0.113.1`
- `show run | section router bgp`
- `show run | section route-map`
- `show run | section ip prefix-list`
- `show run | section router eigrp`

### ISP
- `show ip int brief`
- `show ip bgp summary`
- `show ip bgp`
- `show ip route 203.0.113.1`
- `show ip route 198.51.100.1`
- `show run | section router bgp`
- `show run | include ^ip route 10.10.10.0|^ip route 10.255.0.0`

### Spoke (BR1 recommended)
- `show ip route 203.0.113.1`
- `show ip route 198.51.100.1`
- `ping 203.0.113.1`
- `ping 198.51.100.1`
- `traceroute 203.0.113.1`

---

## v0.5-dual-hub (Dual Hub + failover over DMVPN Phase 2)
### Normal state (before failover) — BR1 recommended
- `show ip eigrp neighbors`
- `traceroute 10.255.3.3` (or another remote loopback)

### Failover state (HQ1 Tunnel0 shutdown) — BR1 recommended
- `show ip eigrp neighbors` (HQ1 down, HQ2 remains)
- `show ip route 10.255.3.3` (route learned via HQ2 control-plane)
- `traceroute 10.255.3.3` (path evidence; Phase2 shortcut may show 1 hop)
- `ping 10.255.3.3 repeat 50 timeout 1` (reachability + convergence indicator)

### HQ1 (before/after shutdown)
- `show ip eigrp neighbors`
- `show dmvpn`
- `show ip nhrp`
- `show clock` (optional)
- `show run interface tunnel0` (bandwidth/delay preference)

### HQ2 (after failover)
- `show ip eigrp neighbors`
- `show ip route 10.255.3.3`
- `show dmvpn`
- `show ip nhrp`
- `show run interface tunnel0` (confirm hub settings + preference)

> Note: convergence time is treated as an indicator only (GNS3 timing variability).
