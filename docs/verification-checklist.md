# Baseline Verification Checklist

This checklist defines pass/fail conditions for each baseline.
Use `docs/command-set.md` to capture evidence in a consistent order.

---

## v0.1 (DMVPN Phase 2)
- `show ip int brief` (target interfaces are **up/up**)
- `show interface tunnel0` (tunnel is **up/up**)
- `show dmvpn` (expected peers present)
- `show ip nhrp` (registrations/mappings present and correct)
- `ping 10.10.10.1` from spokes (overlay reachability)

---

## v0.2 (OSPF over DMVPN)
- `show ip int brief` (up/up)
- `show ip ospf neighbor` (neighbors **FULL**)
- `show ip ospf interface tunnel0` (area/timers/auth/MTU as expected)
- `show ip route ospf` (expected OSPF routes installed)
- `ping/traceroute` to remote loopbacks (reachability)
- (Optional / If DMVPN changed or issues suspected) `show dmvpn`, `show ip nhrp`

---

## v0.3 (EIGRP over DMVPN)
- `show ip int brief` (up/up)
- `show ip eigrp neighbors` (neighbors present / stable)
- `show ip route eigrp` (expected EIGRP routes installed)
- `show ip protocols` (networks/passive-interface as expected)
- `ping/traceroute` between loopbacks (reachability)
- (Hub) `show run interface tunnel0 | include split-horizon|next-hop-self` (spoke-to-spoke support)
- (Hub) `show running-config all | section interface Tunnel0` (confirm `ip nhrp map multicast dynamic`)
- (Optional / If DMVPN changed or issues suspected) `show dmvpn`, `show ip nhrp`

---

## v0.4-bgp (eBGP + inbound filter + selective redistribution into EIGRP)
### HQ1 (control-plane)
- `show ip bgp summary` (neighbor **Established**, PfxRcd as expected)
- `show ip bgp` (allowed prefix present; blocked prefix absent)
- `show ip bgp neighbors 192.0.1.2 received-routes` (proof of inbound policy when soft-reconfig enabled)
- `show ip eigrp topology 203.0.113.1/32` (BGP→EIGRP injection verified)

### Spoke (data-plane)
- `show ip route 203.0.113.1` (route present)
- `show ip route 198.51.100.1` (route absent)
- `ping 203.0.113.1` (success)
- `ping 198.51.100.1` (fail as intended)
- `traceroute 203.0.113.1` (expected path)

### ISP (supporting checks)
- `show ip bgp` (both prefixes advertised)
- Return path static routes point to `192.0.1.1` for `10.10.10.0/24` and `10.255.0.0/16`

---

## v0.5a-dual-hub (Dual Hub + failover over DMVPN Phase 2)
### Normal state
- Spokes have EIGRP neighbors to **HQ1 and HQ2**
  - `show ip eigrp neighbors`
- Baseline path preference shows HQ1 as primary (example by traceroute)
  - `traceroute 10.255.3.3`

### Failover test
- HQ1 Tunnel0 shutdown (document action)
- After failover:
  - `show ip eigrp neighbors` shows HQ1 down / HQ2 up
  - `show ip route 10.255.3.3` learned via HQ2 control-plane
  - `ping` to remote loopback succeeds
  - `traceroute` confirms path change / shortcut behavior
- (Optional / overlay confirmation) `show dmvpn`, `show ip nhrp`

### Convergence measurement
- Convergence time is recorded as an **indicator only**; results may vary in GNS3.
- Validation focuses on **path switch + restored reachability**.
