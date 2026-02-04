# INC-01: OSPF adjacency not FULL (MTU mismatch)

## Summary
- **Impact:** OSPF adjacency between BR1 and HQ1 does not reach **FULL**, causing missing OSPF-learned routes and degraded reachability.
- **Symptom:** OSPF neighbor stuck in **EXSTART/EXCHANGE** (or flaps DOWN/INIT).
- **Root cause:** **MTU mismatch** on the BR1↔HQ1 transit interface.
- **Fix:** Align MTU on both ends (workaround: `ip ospf mtu-ignore`, but not recommended as a first choice).
- **Prevention:** Add MTU checks to change/verification checklists and standardize interface MTU settings.

---

## Lab Context
- **Platform:** GNS3 (primary), Packet Tracer (supporting)
- **Protocols:** OSPF (focus)
- **Devices:** BR1 ↔ HQ1 (minimum required)

> Topology/design references:
- Routing design: `docs/routing-design.md`
- Baseline verification: `docs/verification-checklist.md`
- Command set: `docs/command-set.md`

---

## Expected vs Actual

### Expected
- `show ip ospf neighbor` shows BR1↔HQ1 as **FULL**
- BR1 learns expected OSPF routes (e.g., HQ/DC loopbacks)

### Actual
- Neighbor remains in **EXSTART/EXCHANGE** (or repeatedly resets)
- Expected OSPF routes are missing or unstable

---

## Evidence (Files)
- **Before (healthy baseline):** `evidence/incidents/INC-01/before.txt`
- **Symptom (broken state):** `evidence/incidents/INC-01/symptom.txt`
- **Investigation (analysis):** `evidence/incidents/INC-01/investigation.txt`
- **Fix + Verify:** `evidence/incidents/INC-01/fix-verify.txt`

Configs:
- HQ1 before: `configs/incidents/INC-01/HQ1-before.txt`
- HQ1 after:  `configs/incidents/INC-01/HQ1-after.txt`

---

## Fault Injection (How I broke it)
### Change (intentional misconfig)
Change MTU **only on HQ1** transit interface to create mismatch.

**HQ1**

