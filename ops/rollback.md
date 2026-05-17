# Rollback Plan

## When to rollback
- Change causes loss of reachability beyond acceptable threshold
- Neighbor relationships fail to establish after X minutes
- Route table becomes unstable / unexpected redistribution occurs
- Failover behavior deviates from expected

## Rollback strategy
- Prefer minimal rollback that restores the previous working baseline
- Apply rollback in reverse order of change steps
- Verify after each rollback step

## Rollback steps (example structure)
1. Revert interface-level changes
   - Commands:
   - Expected result:

2. Revert routing protocol changes
   - Commands:
   - Expected result:

3. Revert policy changes (route-map/prefix-list/redistribution)
   - Commands:
   - Expected result:

## Rollback verification checklist
Save evidence under:
- `evidence/baseline/<version>/rollback/`

Minimum:
- `show ip int brief`
- `show dmvpn`
- `show ip nhrp`
- `show ip eigrp neighbors` / `show ip ospf neighbor` / `show ip bgp summary`
- `show ip route` (+ protocol-specific routes)
- `ping` / `traceroute` to key destinations

## Notes
- If rollback restores service but leaves partial drift, document it and schedule cleanup.
