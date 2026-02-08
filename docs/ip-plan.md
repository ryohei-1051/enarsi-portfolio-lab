# IP Plan

This lab uses a shared L2 underlay (NBMA) for DMVPN and a /24 overlay for Tunnel0.
Loopbacks are /32 and used for routing validation (v0.2+).

---

## 1) Underlay (NBMA / Outside) — Shared Ethernet Segment

| Device | Outside IF | IPv4 Address | Subnet Mask | Network | Notes |
|---|---|---:|---:|---:|---|
| HQ1 (Hub) | Gi0/0 | 192.0.2.1 | 255.255.255.0 | 192.0.2.0/24 | DMVPN NBMA (Hub) |
| ISP | Gi0/0 | 192.0.2.2 | 255.255.255.0 | 192.0.2.0/24 | (Optional / future) |
| DC1 (Spoke) | Gi0/0 | 192.0.2.3 | 255.255.255.0 | 192.0.2.0/24 | DMVPN Spoke |
| BR1 (Spoke) | Gi0/0 | 192.0.2.4 | 255.255.255.0 | 192.0.2.0/24 | DMVPN Spoke |
| BR2 (Spoke) | Gi0/0 | 192.0.2.5 | 255.255.255.0 | 192.0.2.0/24 | DMVPN Spoke |

---

## 2) Overlay (DMVPN Tunnel0)

| Device | Tunnel IF | Tunnel IPv4 | Subnet Mask | Network | Notes |
|---|---|---:|---:|---:|---|
| HQ1 (Hub) | Tunnel0 | 10.10.10.1 | 255.255.255.0 | 10.10.10.0/24 | DMVPN Hub tunnel IP |
| DC1 (Spoke) | Tunnel0 | 10.10.10.3 | 255.255.255.0 | 10.10.10.0/24 | DMVPN Spoke tunnel IP |
| BR1 (Spoke) | Tunnel0 | 10.10.10.4 | 255.255.255.0 | 10.10.10.0/24 | DMVPN Spoke tunnel IP |
| BR2 (Spoke) | Tunnel0 | 10.10.10.5 | 255.255.255.0 | 10.10.10.0/24 | DMVPN Spoke tunnel IP |

### DMVPN Parameters (v0.1)
| Item | Value |
|---|---|
| DMVPN Phase | Phase 2 |
| NHRP network-id | 10 |
| NHRP key/authentication | 12345 |
| Hub NHS (overlay) | 10.10.10.1 |
| Hub NBMA (underlay) | 192.0.2.1 |

---

## 3) Loopbacks (Validation prefixes)

Loopbacks are used to validate routing reachability and are advertised via dynamic routing in later baselines (v0.2+).

| Device | Loopback IF | IPv4 Address | Subnet Mask | Notes |
|---|---|---:|---:|---|
| HQ1 | Lo0 | 10.255.1.1 | 255.255.255.255 | OSPF router-id candidate |
| DC1 | Lo0 | 10.255.3.3 | 255.255.255.255 | Test destination |
| BR1 | Lo0 | 10.255.4.4 | 255.255.255.255 | Test destination |
| BR2 | Lo0 | 10.255.5.5 | 255.255.255.255 | Test destination |

---

## 4) Naming / Conventions
- Underlay addresses: `192.0.2.0/24` (documentation prefix)
- DMVPN overlay: `10.10.10.0/24`
- Loopbacks: `10.255.0.0/16` (per-device /32)
- Files:
  - Baseline configs: `configs/baseline/<version>/`
  - Evidence (show outputs): `evidence/baseline/<version>/`
