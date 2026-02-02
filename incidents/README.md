# Incidents Index (ENARSI-style)

Each incident follows this format:
**Symptom → Expected vs Actual → Investigation (commands + evidence) → Root cause → Fix → Verification → Prevention**

---

## INC-01: OSPF adjacency not FULL (MTU / area mismatch / auth)
- **Symptom:** Neighbor stuck in EXSTART/EXCHANGE or flaps (DOWN/INIT)
- **Goal:** Differentiate MTU mismatch vs area mismatch vs authentication issues

## INC-02: Specific prefixes missing (LSA filtering / summarization / distribute-list)
- **Symptom:** Only some routes are missing while others are fine
- **Goal:** Trace where the route disappears (originate → advertise → receive → install)

## INC-03: BGP not Established (TCP/179 / ACL / TTL / neighbor config)
- **Symptom:** Neighbor stays in Idle/Active
- **Goal:** Validate L3 reachability, TCP/179, update-source, ebgp-multihop, and ACLs

## INC-04: BGP up but no routes (next-hop / policy / filtering)
- **Symptom:** Sessions are Established but routes are not received/advertised as expected
- **Goal:** Check next-hop-self, route-maps, prefix-lists, communities, and inbound/outbound filters

## INC-05: Redistribution loop / instability (OSPF↔BGP without tagging)
- **Symptom:** Routes oscillate or feed back between domains
- **Goal:** Use tagging + filtering and limit redistribution points to prevent loops

## INC-06: Path selection not as intended (LocalPref / MED / AS-path prepending)
- **Symptom:** Traffic prefers the wrong link or backup becomes primary
- **Goal:** Apply BGP bestpath rules correctly and choose the right attribute for the job

## INC-07: One-way traffic / asymmetric routing
- **Symptom:** Ping works only one direction; apps fail intermittently
- **Goal:** Compare forward/return paths and correct asymmetric routing

## INC-08: Default route missing or leaking (default-originate / OSPF default)
- **Symptom:** Branch can’t reach external networks, or default spreads too far
- **Goal:** Control default origination conditions and propagation scope

## INC-09: Blackhole due to summarization (summary + Null0 / missing exceptions)
- **Symptom:** Certain subnets fail after summarization; traffic is “absorbed”
- **Goal:** Avoid summary blackholes by ensuring specifics or exceptions exist

## INC-10: Slow convergence after failover (IP SLA/track/timers)
- **Symptom:** Failover takes too long or partial connectivity remains broken
- **Goal:** Improve detection → routing reaction → convergence, and verify timer behavior
