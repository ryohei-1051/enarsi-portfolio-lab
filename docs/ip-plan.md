# IP Plan

This lab uses a shared L2 underlay (NBMA) for DMVPN and a /24 overlay for Tunnel0.
Loopbacks are /32 and used for routing validation (v0.2+).

> Notes:
> - ISP is present in the topology but **not required for baseline v0.1/v0.2**.
> - Naming is standardized as: **HQ1 (Hub), DC1/BR1/BR2 (Spokes), ISP (optional)**.

---

## 1) Underlay (NBMA / DMVPN Outside) — Shared Ethernet Segment

| Device | Role | Outside IF | IPv4 Address | Prefix | Notes |
|---|---|---|---:|---:|---|
| HQ1 | Hub | Gi0/0 | 192.0.2.1 | /24 | DMVPN NBMA (Hub) |
| HQ2 | Hub | Gi0/0 | 192.0.2.2 | /24 | DMVPN NBMA (Hub) |
| DC1 | Spoke | Gi0/0 | 192.0.2.3 | /24 | DMVPN NBMA (Spoke) |
| BR1 | Spoke | Gi0/0 | 192.0.2.4 | /24 | DMVPN NBMA (Spoke) |
| BR2 | Spoke | Gi0/0 | 192.0.2.5 | /24 | DMVPN NBMA (Spoke) |

Network: `192.0.2.0/24` (documentation prefix)

---

## 2) HQ1–ISP/HQ2–ISP Link (BGP Transport)

| Device | IF | IPv4 Address | Prefix | Notes |
|---|---|---:|---:|---|
| HQ1 | Gi0/1 | 192.0.1.1 | /24 | eBGP neighbor transport |
| ISP | Gi0/1 | 192.0.1.2 | /24 | eBGP neighbor transport |
| HQ2 | Gi0/1 | 192.0.3.1 | /24 | eBGP neighbor transport |
| ISP | Gi0/1 | 192.0.3.2 | /24 | eBGP neighbor transport |

Network: `192.0.1.0/24`

---

## 3) Overlay (DMVPN Tunnel0)

| Device | Role | Tunnel IF | Tunnel IPv4 | Prefix |
|---|---|---|---:|---:|
| HQ1 | Hub | Tunnel0 | 10.10.10.1 | /24 |
| HQ2 | Hub | Tunnel0 | 10.10.10.2 | /24 |
| DC1 | Spoke | Tunnel0 | 10.10.10.3 | /24 |
| BR1 | Spoke | Tunnel0 | 10.10.10.4 | /24 |
| BR2 | Spoke | Tunnel0 | 10.10.10.5 | /24 |

Network: `10.10.10.0/24`

---

## 4) Loopbacks

Loopbacks are used to validate routing reachability and will be advertised via dynamic routing in later baselines (v0.2+).

### Internal validation loopbacks (v0.2+)
| Device | Loopback IF | IPv4 Address | Prefix |
|---|---|---:|---:|
| HQ1 | Lo0 | 10.255.1.1 | /32 |
| HQ2 | Lo0 | 10.255.2.2 | /32 |
| DC1 | Lo0 | 10.255.3.3 | /32 |
| BR1 | Lo0 | 10.255.4.4 | /32 |
| BR2 | Lo0 | 10.255.5.5 | /32 |

### External prefixes (ISP)
| Device | Loopback IF | IPv4 Address | Prefix | Notes |
|---|---|---:|---:|---|
| ISP | Lo0 | 203.0.113.1 | /32 | Allowed prefix |
| ISP | Lo1 | 198.51.100.1 | /32 | Blocked prefix |

---

## 5) Conventions / Where to Save Artifacts
- Baseline configs: `configs/baseline/<version>/`
- Baseline evidence (show outputs, ping, traceroute): `evidence/baseline/<version>/`
- Versioning:
  - `v0.1-dmvpn` : DMVPN connectivity (NHRP registration + tunnel reachability)
  - `v0.2-ospf`  : OSPF over DMVPN (neighbors FULL + route exchange)
  - `v0.3-eigrp`  : EIGRP over DMVPN (neighbors established + route exchange)
  - `v0.4-bgp`  : eBGP + inbound filter + selective redistribution into EIGRP
  - `v0.5a-dual-hub`  : Dual Hub + failover over DMVPN Phase 2
  - `v0.5b-external-failover`  : BGP on HQ2 + external reachability failover
