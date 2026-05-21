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
```

(Optional) Reset adjacency quickly:
- `clear ip ospf process`
Evidence to capture (save as `02-fault-injection.txt`)
- HQ1: `show run interface tunnel0`
- HQ1: `show ip ospf interface tunnel0`

## Symptom (What I observed)
Evidence to capture (save as `03-symptom.txt`)

HQ1 / BR1
- `show ip ospf neighbor`
- `show ip ospf interface tunnel0`
- `show ip route ospf`
- `show logging | include OSPF|MTU|MISMATCH`


## Investigation (How I diagnosed it)
Step 1: Confirm neighbor state
- `show ip ospf neighbor`
  - If **EXSTART/EXCHANGE** → suspect **MTU mismatch** (DBD exchange)
  - If **INIT/2-WAY** or neighbor not forming → check **area/auth/timers/network type**
Step 2: Compare OSPF interface parameters (MTU/area/timers/auth)

HQ1 / BR1
- `show ip ospf interface tunnel0`
- `show interface tunnel0 | include MTU`
- `show run interface tunnel0`

Finding
- Tunnel0 MTU differs between HQ1 and BR1 → OSPF adjacency cannot complete DBD exchange.

## Root Cause
MTU mismatch on Tunnel0 between HQ1 and BR1.

## Fix (What I changed)
Preferred fix: Align MTU

HQ1
```cisco
conf t
interface tunnel0
 no ip mtu 1400
 ! or: ip mtu 1476 (match the other side)
end
```
(Optional) Reset adjacency:
```cisco
clear ip ospf process
```
Evidence to capture (save as `05-fix-verify.txt`)

HQ1 / BR1

- `show run interface tunnel0`
- `show ip ospf neighbor`
- `show ip route ospf`
- `ping <remote loopback>` / `traceroute <remote loopback>`

## Verification (Pass/Fail)
Pass if:
- Neighbors reach FULL
- Expected OSPF routes return (show ip route ospf)
- End-to-end reachability restored

## Prevention / Hardening
- Add MTU checks to pre/post verification:
  - `show ip ospf interface tunnel0` (MTU/area/timers/auth)
  - `show interface tunnel0 | include MTU`
- Standardize Tunnel0 MTU values in baseline templates
- Document rollback (ops/rollback.md) and verify after changes (ops/post-change-checklist.md)

## Optional Variants (future expansion)
- Area mismatch: mismatch OSPF area on one side → neighbor fails to form
- Auth mismatch: enable auth on one side only → neighbor fails (logs typically indicate auth mismatch)

(These can be added as INC-01b/INC-01c, or separate incidents if you prefer.)
