# Post-change Checklist

## Save location
- Evidence path: `evidence/baseline/<version>/post-check/`
- Config path: `configs/baseline/<version>/`

## Interface / Underlay
- [ ] `show ip int brief` (expected interfaces up/up)
- [ ] Underlay reachability (as applicable):
  - [ ] `ping <peer NBMA/transport IP>`

## DMVPN / Overlay (if applicable)
- [ ] `show dmvpn` (expected peers present)
- [ ] `show ip nhrp` (expected registrations/mappings present)
- [ ] `show run interface tunnel0` (key DMVPN/NHRP lines present)
- [ ] (Hub) `show run all | section interface tunnel0` (confirm `ip nhrp map multicast dynamic`)

## Routing Protocol
### EIGRP
- [ ] `show ip eigrp neighbors` (expected neighbors present/stable)
- [ ] `show ip route eigrp` (expected routes installed)
- [ ] `show ip protocols` (networks/passive settings)

### OSPF (if used)
- [ ] `show ip ospf neighbor` (FULL)
- [ ] `show ip route ospf`

### BGP (if used)
- [ ] `show ip bgp summary` (Established)
- [ ] `show ip bgp` (expected prefixes only)
- [ ] `show ip route bgp`

## End-to-end reachability
- [ ] `ping <key loopback 1>`
- [ ] `ping <key loopback 2>`
- [ ] `traceroute <key loopback>` (expected path)

## Failover validation (if applicable)
- [ ] Baseline path confirmed (pre-failover traceroute)
- [ ] Failover action executed (document what was shut)
- [ ] Post-failover path confirmed (traceroute)
- [ ] Reachability restored (ping success)
- [ ] Convergence recorded as indicator only (if measured)

## Finalize
- [ ] Save running-config per device
- [ ] Update `docs/verification-notes.md`
- [ ] Save snapshot under `docs/versions/<version>/`
- [ ] Create Release `<tag>`
