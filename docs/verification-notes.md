## v0.1-dmvpn (Phase 2 / single-hub)
- Topology: HQ1 (Hub) + DC1/BR1/BR2 (Spokes), underlay on a shared Ethernet segment
- Result: Spokes successfully registered to HQ1 via NHRP; tunnel reachability confirmed (Spoke → HQ1 Tunnel0)
- Evidence: `evidence/baseline/v0.1-dmvpn/`
- Configs: `configs/baseline/v0.1-dmvpn/`
- Key checks: `show dmvpn`, `show ip nhrp`, `show int tunnel0`, `ping <HQ1_Tunnel_IP>`
- Next: v0.2 add OSPF over DMVPN (hub-spoke adjacencies)
