# IP Plan (Snapshot) — v0.5b-external-failover

This snapshot reflects baseline v0.5b (External reachability failover: eBGP on HQ2 + inbound filtering + selective redistribution into EIGRP).

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

## 2-1) HQ1–ISP Link (BGP Transport) — v0.5a

| Device | IF | IPv4 Address | Prefix | Notes |
|---|---|---:|---:|---|
| HQ1 | Gi0/1 | 192.0.1.1 | /30 | eBGP neighbor transport |
| ISP | Gi0/1 | 192.0.1.2 | /30 | eBGP neighbor transport |

Network: `192.0.1.0/30`

## 2-2) HQ2–ISP Link (BGP Transport) — v0.5b

| Device | IF | IPv4 Address | Prefix | Notes |
|---|---|---:|---:|---|
| HQ2 | Gi0/1 | 192.0.3.1 | /30 | eBGP transport to ISP |
| ISP | Gi0/2 | 192.0.3.2 | /30 | eBGP transport to HQ2 |

Network: `192.0.3.0/30`

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

## 5) DMVPN Parameters
| Item | Value |
|---|---|
| DMVPN Phase | Phase 2 |
| NHRP network-id | 10 |
| NHRP authentication key | 12345 |
| Hub tunnel IP | 10.10.10.1 |
| Hub tunnel IP | 10.10.10.2 |
| Hub NBMA IP | 192.0.2.1 |
| Hub NBMA IP | 192.0.2.2 |

---

## 6) Routing Parameters (v0.5b)
### eBGP
| Item | Value |
|---|---|
| HQ1 AS | 65001 |
| HQ2 AS | 65001 |
| ISP AS | 65002 |
| HQ1–ISP neighbor | HQ1(192.0.1.1) ↔ ISP(192.0.1.2) |
| HQ2–ISP neighbor | HQ2(192.0.3.1) ↔ ISP(192.0.3.2) |
| Policy | inbound filter on HQ1/HQ2 (accept 203.0.113.1/32 only) |

### EIGRP redistribution
- HQ1 and HQ2 redistribute the allowed BGP prefix into EIGRP using `route-map BGP-TO-EIGRP` with explicit metric.

### ISP return paths (for ping reply)
- `10.10.10.0/24` → next-hop `192.0.3.1` (HQ2)
- `10.255.0.0/16` → next-hop `192.0.3.1` (HQ2)
