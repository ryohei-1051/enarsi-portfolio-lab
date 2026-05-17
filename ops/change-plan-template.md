# Change Plan Template

## Change ID
- ID: (e.g., CHG-2026-05-17-01)
- Baseline target: (e.g., v0.5-dual-hub)
- Author: (name)
- Date/Time: (YYYY-MM-DD HH:MM)

## Summary
- What is changing (one sentence):
- Why (one sentence):
- Success criteria (bullet list):

## Scope
### In scope
- Devices:
- Interfaces / protocols:
- Repo paths to update:
  - Configs: `configs/baseline/<version>/`
  - Evidence: `evidence/baseline/<version>/`
  - Notes: `docs/verification-notes.md`
  - Snapshot: `docs/versions/<version>/`
  - Release: `<tag>`

### Out of scope
- (explicitly state what you are not touching)

## Risk Assessment
- Impact if change fails:
- Blast radius:
- User-visible symptoms (if any):
- Risk level: Low / Medium / High
- Mitigations:
  - (e.g., take baseline evidence first, keep rollback ready)

## Pre-change Checks (Pre-check)
Run and save outputs under:
- `evidence/baseline/<version>/pre-check/`

Minimum:
- `show ip int brief`
- `show dmvpn`
- `show ip nhrp`
- `show ip eigrp neighbors` (or OSPF/BGP depending on version)
- `show ip route` (and protocol-specific route table)
- `ping` / `traceroute` to key destinations

## Implementation Plan (Steps)
> Write steps that can be executed line-by-line. Keep steps minimal.

1. Step 1:
   - Commands:
   - Expected intermediate result:

2. Step 2:
   - Commands:
   - Expected intermediate result:

3. Step 3:
   - Commands:
   - Expected intermediate result:

## Post-change Verification (Post-check)
Run and save outputs under:
- `evidence/baseline/<version>/post-check/`

Minimum:
- Neighbor state (EIGRP/OSPF/BGP as applicable)
- Expected routes installed
- End-to-end reachability
- (If failover-related) path changes confirmed by traceroute

## Rollback Plan (If needed)
- Trigger conditions for rollback:
- Rollback steps:
  1.
  2.
- Rollback verification:
  - Key checks to confirm restored baseline

## Validation Notes
- Observed behavior (brief):
- Deviations from expected:
- Follow-up actions:
