---
title: Projet 5 — Conception réseau complète pour une PME multi-sites
tags: [networking essentials]

---

# Projet 5 — Conception réseau complète pour une PME multi-sites

---

## 1. Objectif

Relier **2 sites distants** d'une PME de façon **sécurisée** :

- plan d'adressage global (IP + VLAN)
- interconnexion des sites par un **VPN site-à-site (GRE + IPsec)**
- schéma d'architecture + rapport de justification

---

## 2. Besoins (hypothèses)

| | Siège | Succursale |
|---|---|---|
| Postes employés | 30 | 15 |
| Postes comptabilité | 10 | 5 |
| Serveurs (fichiers, etc.) | 3 | — |
| Accès Internet | oui | oui |

**Exigences :** sites qui communiquent, trafic inter-sites chiffré, comptabilité séparée du reste.

---

## 3. Plan d'adressage

### 3.1 Réseaux LAN

| Site | VLAN | Nom | Réseau | Passerelle |
|---|---|---|---|---|
| Siège | 10 | Employés | 10.1.10.0/24 | 10.1.10.1 |
| Siège | 20 | Comptabilité | 10.1.20.0/24 | 10.1.20.1 |
| Siège | 30 | Serveurs | 10.1.30.0/24 | 10.1.30.1 |
| Succursale | 10 | Employés | 10.2.10.0/24 | 10.2.10.1 |
| Succursale | 20 | Comptabilité | 10.2.20.0/24 | 10.2.20.1 |

### 3.2 Liens WAN / tunnel

| Lien | Réseau | Côté A | Côté B |
|---|---|---|---|
| R1 ↔ ISP | 203.0.113.0/30 | R1 : .1 | ISP : .2 |
| R2 ↔ ISP | 198.51.100.0/30 | R2 : .1 | ISP : .2 |
| Tunnel VPN | 10.255.0.0/30 | R1 : .1 | R2 : .2 |

### 3.3 Équipements (exemples)

| Équipement | IP | Passerelle |
|---|---|---|
| PC1-Siège (VLAN 10) | 10.1.10.10/24 | 10.1.10.1 |
| PC-Compta-Siège (VLAN 20) | 10.1.20.10/24 | 10.1.20.1 |
| SRV-Fichiers (VLAN 30) | 10.1.30.10/24 | 10.1.30.1 |
| PC1-Succursale (VLAN 10) | 10.2.10.10/24 | 10.2.10.1 |
| PC-Compta-Succursale (VLAN 20) | 10.2.20.10/24 | 10.2.20.1 |

---

## 4. Schéma d'architecture

```
 SIÈGE                                                      SUCCURSALE
 [PC1-V10] [PC-Compta-V20] [SRV-V30]                [PC1-V10] [PC-Compta-V20]
        \        |        /                                \        |
         [   SW1 (VLANs)  ]                                 [   SW2 (VLANs)  ]
                 | trunk                                            | trunk
              [ R1 ] ---- 203.0.113.0/30 ---- [ISP] ---- 198.51.100.0/30 ---- [ R2 ]
                 \________________ Tunnel GRE/IPsec 10.255.0.0/30 _______________/
```

![Capture d'écran 2026-10-16 090025](https://hackmd.io/_uploads/BycqiB1hfe.png)

---

## 5. Montage dans GNS3

### 5.1 Équipements à placer

| Qté | Équipement | Rôle |
|---|---|---|
| 3 | Routeur Cisco (IOSv / c7200) | R1, R2, ISP |
| 2 | Switch (IOSvL2 ou switch Ethernet GNS3) | SW1, SW2 |
| 5 | VPCS | PC1-Siège, PC-Compta-Siège, SRV, PC1-Succ., PC-Compta-Succ. |

### 5.2 Câblage

- SW1 ↔ R1 (trunk) · SW2 ↔ R2 (trunk)
- R1 ↔ ISP · R2 ↔ ISP
- PC1 → port VLAN 10 · PC-Compta → port VLAN 20 · SRV → port VLAN 30

> ⚠️ Adapter les noms d'interface (`g0/0`, `e0/1`…) à votre topologie.

---

## 6. Configuration

### 6.1 Switch SW1 (Siège)

```
enable
configure terminal
hostname SW1
vlan 10
 name EMPLOYES
vlan 20
 name COMPTA
vlan 30
 name SERVEURS
!
interface e0
 description Trunk vers R1
 switchport trunk encapsulation dot1q
 switchport mode trunk
interface e1
 switchport mode access
 switchport access vlan 10
interface e2
 switchport mode access
 switchport access vlan 20
interface e3
 switchport mode access
 switchport access vlan 30
end
write memory
```

### 6.2 Switch SW2 (Succursale)

Même principe : VLAN 10 et 20 seulement, trunk vers R2, un port access par VLAN.

### 6.3 Routeur R1 (Siège)

```
enable
configure terminal
hostname R1
interface g0/0
 no shutdown
interface g0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.1 255.255.255.0
interface g0/0.20
 encapsulation dot1Q 20
 ip address 10.1.20.1 255.255.255.0
interface g0/0.30
 encapsulation dot1Q 30
 ip address 10.1.30.1 255.255.255.0
interface g1/0
 description Lien vers ISP
 ip address 203.0.113.1 255.255.255.252
 no shutdown
ip route 0.0.0.0 0.0.0.0 203.0.113.2
end
write memory
```

### 6.4 Routeur ISP (Internet simulé)

```
enable
configure terminal
hostname ISP
interface g0/0
 ip address 203.0.113.2 255.255.255.252
 no shutdown
interface g1/0
 ip address 198.51.100.2 255.255.255.252
 no shutdown
end
write memory
```

> L'ISP n'a **aucune route** vers les LAN : c'est voulu, seul le tunnel les relie.

### 6.5 Routeur R2 (Succursale)

```
enable
configure terminal
hostname R2
interface g0/0
 no shutdown
interface g0/0.10
 encapsulation dot1Q 10
 ip address 10.2.10.1 255.255.255.0
interface g0/0.20
 encapsulation dot1Q 20
 ip address 10.2.20.1 255.255.255.0
interface g1/0
 description Lien vers ISP
 ip address 198.51.100.1 255.255.255.252
 no shutdown
ip route 0.0.0.0 0.0.0.0 198.51.100.2
end
write memory
```

### 6.6 PC (VPCS)

```
PC1-Siege>        ip 10.1.10.10/24 10.1.10.1
PC-Compta-Siege>  ip 10.1.20.10/24 10.1.20.1
SRV>              ip 10.1.30.10/24 10.1.30.1
PC1-Succ>         ip 10.2.10.10/24 10.2.10.1
PC-Compta-Succ>   ip 10.2.20.10/24 10.2.20.1
```

Penser à faire `save` sur chaque VPCS.

---

## 7. VPN site-à-site (GRE + IPsec)

### 7.1 Sur R1

```
configure terminal
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 2
 lifetime 3600
crypto isakmp key CleSecrete123 address 198.51.100.1
!
crypto ipsec transform-set TS esp-aes 256 esp-sha-hmac
 mode transport
crypto ipsec profile VPN-PROFILE
 set transform-set TS
!
interface Tunnel0
 ip address 10.255.0.1 255.255.255.252
 tunnel source g1/0
 tunnel destination 198.51.100.1
 tunnel protection ipsec profile VPN-PROFILE
!
ip route 10.2.0.0 255.255.0.0 10.255.0.2
end
write memory
```

### 7.2 Sur R2

```
configure terminal
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 2
 lifetime 3600
crypto isakmp key CleSecrete123 address 203.0.113.1
!
crypto ipsec transform-set TS esp-aes 256 esp-sha-hmac
 mode transport
crypto ipsec profile VPN-PROFILE
 set transform-set TS
!
interface Tunnel0
 ip address 10.255.0.2 255.255.255.252
 tunnel source g1/0
 tunnel destination 203.0.113.1
 tunnel protection ipsec profile VPN-PROFILE
!
ip route 10.1.0.0 255.255.0.0 10.255.0.1
end
write memory
```

---

## 8. Tests et preuves

| # | Test | Commande | Résultat attendu |
|---|---|---|---|
| T1 | Passerelle locale | `ping 10.1.10.1` depuis PC1-Siège | ✅ |
| T2 | Inter-VLAN (Siège) | PC1 → SRV (`ping 10.1.30.10`) | ✅ |
| T3 | Tunnel actif | `ping 10.255.0.2` depuis R1 | ✅ |
| T4 | Inter-sites | PC1-Siège → PC1-Succursale (`ping 10.2.10.10`) | ✅ |
| T5 | Phase 1 IKE | `show crypto isakmp sa` sur R1 | état `QM_IDLE` |
| T6 | Phase 2 IPsec | `show crypto ipsec sa` | compteurs encaps/decaps > 0 |


T1
![Capture d'écran 2026-10-16 095621](https://hackmd.io/_uploads/HkB3d8khzl.png)
T2
![Capture d'écran 2026-10-16 095731](https://hackmd.io/_uploads/ryFltIk2Me.png)
T3
![Capture d'écran 2026-10-16 100029](https://hackmd.io/_uploads/Sk0oFUynzx.png)
T4
![Capture d'écran 2026-10-16 095844](https://hackmd.io/_uploads/S1ZrKIJnfx.png)
T5
![Capture d'écran 2026-10-16 100115](https://hackmd.io/_uploads/Bkn0Y8Jhfg.png)
T6
![Capture d'écran 2026-10-16 100220](https://hackmd.io/_uploads/r13M5IyhMx.png)

**Cheat sheet de vérification :**

```
show ip interface brief
show vlan brief            (sur les switchs)
show interfaces trunk      (sur les switchs)
show ip route
show interfaces tunnel 0
```
![Capture d'écran 2026-10-16 100332](https://hackmd.io/_uploads/Bk7PcUJ3Gl.png)
![Capture d'écran 2026-10-16 100400](https://hackmd.io/_uploads/S1JYq8J3Me.png)
![Capture d'écran 2026-10-16 100431](https://hackmd.io/_uploads/Hk2qqL12zl.png)
![Capture d'écran 2026-10-16 100504](https://hackmd.io/_uploads/H1p29Iy2Me.png)

---

![Capture d'écran 2026-10-16 095325](https://hackmd.io/_uploads/H1oEuIknzl.png)

---