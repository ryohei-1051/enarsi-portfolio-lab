# INC-01: OSPF adjacency not FULL (MTU mismatch)

## Summary
- **Impact:** OSPF adjacency between BR1 and HQ1 does not reach **FULL**, causing missing OSPF-learned routes and degraded reachability.
- **Symptom:** OSPF neighbor stuck in **EXSTART/EXCHANGE** (or flaps).
- **Root cause:** **MTU mismatch** on Tunnel0 (OSPF DBD exchange cannot complete).
- **Fix:** Align MTU on both ends (workaround: `ip ospf mtu-ignore` is possible but not the first choice).
- **Prevention:** Add MTU checks to verification/change checklists; standardize MTU values.

---

## Lab Context
- **Baseline required:** `v0.2-ospf` (start from the OSPF baseline configs)
- **Platform:** GNS3 (primary), Packet Tracer (supporting)
- **Devices:** HQ1 ↔ BR1 (minimum)
- **Links:** 
  - Routing design: `docs/routing-design.md`
  - Baseline verification: `docs/verification-checklist.md`
  - Command set: `docs/command-set.md`

---

## Expected vs Actual

### Expected
- `show ip ospf neighbor` shows BR1↔HQ1 as **FULL**
- BR1 learns expected OSPF routes (e.g., HQ1 loopback)

### Actual
- Neighbor remains in **EXSTART/EXCHANGE**
- Expected OSPF routes are missing or unstable

---

## Evidence (Files)
Evidence:
- `evidence/incidents/INC-01/01-baseline.txt`
- `evidence/incidents/INC-01/02-fault-injection.txt`
- `evidence/incidents/INC-01/03-symptom.txt`
- `evidence/incidents/INC-01/04-investigation.txt`
- `evidence/incidents/INC-01/05-fix-verify.txt`

Configs (delta only; baseline configs live under `configs/baseline/v0.2-ospf/`):
- `configs/incidents/INC-01/HQ1-delta-before.txt`
- `configs/incidents/INC-01/HQ1-delta-after.txt`
- `configs/incidents/INC-01/BR1-delta-before.txt`
- `configs/incidents/INC-01/BR1-delta-after.txt`

---

## Fault Injection (How I broke it)

### Change (intentional misconfig)
Create an MTU mismatch by changing MTU **only on HQ1 Tunnel0**.

**HQ1**
```cisco
conf t
interface tunnel0
 ip mtu 1400
end
