---
title: Projet 3 — Routeur/pare-feu pour une petite entreprise (GNS3)
tags: [networking essentials]

---

# Projet 3 — Mise en place d'un routeur/pare-feu pour une petite entreprise

---

## 1. Contexte et objectifs

Une petite entreprise veut connecter son réseau local (LAN) à Internet de façon sécurisée avec un pare-feu basique.

- Configurer un routeur pour la connexion Internet (WAN/LAN)
- Activer le NAT (PAT) pour l'accès Internet
- Mettre en place des règles de pare-feu simples (ACL)
- Tester l'accès autorisé et le blocage du trafic non autorisé

---

## 2. Topologie et plan d'adressage

```
[Internet] -- [Nœud NAT GNS3] -- G0/0 [R-FW] G1/0  -- E0 [SW1] E1 -- PC1
                                                           E2
                                                           |
                                                          PC2
```

| Équipement | Interface | Adresse IP | Passerelle | Rôle |
|---|---|---|---|---|
| R-FW | G0/0 (WAN) | DHCP (réseau du nœud NAT, 192.168.122.0/24) | — | Sortie Internet |
| R-FW | G1/0 (LAN) | 192.168.10.1/24 | — | Passerelle du LAN |
| PC1 (VPCS) | e0 | 192.168.10.10/24 | 192.168.10.1 | Poste client |
| PC2 (VPCS) | e0 | 192.168.10.11/24 | 192.168.10.1 | Poste client |
| DNS | — | 8.8.8.8 | — | Résolution de noms |


---

## 3. Préparation du projet GNS3

1. Créer un nouveau projet : `Projet3_Routeur_Pare-feu`
2. Placer les nœuds :
   - 1 × **NAT** (nœud intégré GNS3) → simule Internet
   - 1 × **Routeur Cisco** renommé `R-FW`
   - 1 × **Switch Ethernet** `SW1`
   - 2 × **VPCS** `PC1` et `PC2`
3. Câbler :
   - NAT ↔ `R-FW` G0/0
   - `R-FW` G1/0 ↔ `SW1`
   - `SW1` ↔ `PC1` et `PC2`
4. Démarrer tous les équipements (▶)

![Capture d'écran 2026-10-14 085429](https://hackmd.io/_uploads/r1LEDshjMe.png)


---

## 4. Configuration du routeur

### 4.1 Interfaces WAN et LAN

```
enable
configure terminal
hostname R-FW
no ip domain-lookup

interface G0/0
 description WAN vers Internet
 ip address dhcp
 no shutdown
exit

interface G1/0
 description LAN entreprise
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit
```

**Vérification :**

```
show ip interface brief
```

G0/0 doit avoir une adresse reçue en DHCP, G0/1 doit afficher `192.168.10.1`, les deux en `up/up`.

![Capture d'écran 2026-10-14 090458](https://hackmd.io/_uploads/ByzhKs3sMl.png)


### 4.2 Route par défaut et DNS

```
ip route 0.0.0.0 0.0.0.0 G0/0 192.168.226.146
ip domain-lookup
ip name-server 8.8.8.8
```

:::warning
Si ton nœud NAT donne une autre passerelle, remplace `192.168.226.146` (visible avec `show dhcp lease` ou `show ip route`).
:::

**Test depuis le routeur :**

```
ping 8.8.8.8
```

### 4.3 NAT (PAT) pour l'accès Internet

```
interface G0/0
 ip nat outside
exit
interface G1/0
 ip nat inside
exit

access-list 1 permit 192.168.10.0 0.0.0.255
ip nat inside source list 1 interface G0/0 overload
```

**Vérification :**

```
show ip nat translations
show ip nat statistics
```

![Capture d'écran 2026-10-14 095037](https://hackmd.io/_uploads/rkyDN33jfg.png)




---

## 5. Configuration des postes du LAN

Sur **PC1** (console VPCS) :

```
ip 192.168.10.10 255.255.255.0 192.168.10.1
ip dns 8.8.8.8
save
```

Sur **PC2** :

```
ip 192.168.10.11 255.255.255.0 192.168.10.1
ip dns 8.8.8.8
save
```

---

## 6. Test d'accès Internet (avant pare-feu)

Depuis **PC1** :

```
ping 192.168.10.1
ping 8.8.8.8
ping google.com
```

Résultat attendu : réponses reçues (`84 bytes from ...`).

![Capture d'écran 2026-10-14 094858](https://hackmd.io/_uploads/rkClVh3oze.png)


---

## 7. Règles de pare-feu (ACL)

### 7.1 Règles à implémenter

| N° | Sens | Action | Protocole | Source | Destination | Justification |
|---|---|---|---|---|---|---|
| 10 | LAN → Internet | **Bloquer** | TCP/23 (Telnet) | LAN | Tout | Protocole non chiffré, interdit en entreprise |
| 20 | LAN → Internet | **Bloquer** | ICMP | LAN | 8.8.8.8 | Service/hôte ciblé bloqué (test visible) |
| 30 | LAN → Internet | Autoriser | IP | LAN | Tout | Accès Internet normal |
| 10 | Internet → LAN | Autoriser | TCP établi | Tout | LAN | Réponses aux connexions initiées par le LAN |
| 20 | Internet → LAN | Autoriser | ICMP echo-reply, DHCP | Tout | Tout | Retour des pings et du DHCP |
| 30 | Internet → LAN | **Bloquer** | IP | Tout | Tout | Aucune connexion entrante non sollicitée |

### 7.2 Configuration

```
! --- Filtrage du trafic sortant (LAN vers Internet) ---
ip access-list extended LAN-IN
 10 deny tcp any any eq 23 log
 20 deny icmp 192.168.10.0 0.0.0.255 host 8.8.8.8 log
 30 permit ip 192.168.10.0 0.0.0.255 any
exit

! --- Filtrage du trafic entrant (Internet vers LAN) ---
ip access-list extended WAN-IN
 10 permit tcp any any established
 20 permit icmp any any echo-reply
 30 permit udp any eq bootps any eq bootpc
 40 deny ip any any log
exit

interface G1/0
 ip access-group LAN-IN in
exit
interface G0/0
 ip access-group WAN-IN in
exit
```

**Vérification :**

```
show access-lists
show ip interface FastEthernet0/1 | include access list
```

![Capture d'écran 2026-10-14 095814](https://hackmd.io/_uploads/rkbE823iGl.png)


---

## 8. Tests après application du pare-feu

### 8.1 Accès autorisé

Depuis **PC1** :

```
ping 1.1.1.1
ping google.com
```

✅ Attendu : réponses reçues → le NAT et la règle `permit` fonctionnent.

![Capture d'écran 2026-10-14 100435](https://hackmd.io/_uploads/S1Xow3hjzl.png)


### 8.2 Trafic bloqué

Depuis **PC1** :

```
ping 8.8.8.8
```

❌ Attendu : échec, message du type *Communication administratively filtered* (ICMP type 3, code 13).

![Capture d'écran 2026-10-14 100511](https://hackmd.io/_uploads/HJFawn2oGe.png)


**Preuve côté routeur :**

```
show access-lists LAN-IN
```

Le compteur de la ligne 20 (`deny icmp ... host 8.8.8.8`) doit avoir augmenté (`matches`).

![Capture d'écran 2026-10-14 100711](https://hackmd.io/_uploads/Bk1Bd22ifg.png)

---

## 9. Tableau récapitulatif des tests

| # | Test | Depuis | Commande | Résultat attendu | Résultat obtenu | Preuve |
|---|---|---|---|---|---|---|
| T1 | Connectivité LAN | PC1 | `ping 192.168.10.1` | OK |OK | Capture 4 |
| T2 | NAT / Internet (IP) | PC1 | `ping 1.1.1.1` | OK |OK | Capture 6 |
| T3 | Résolution DNS | PC1 | `ping google.com` | OK |OK | Capture 6 |
| T4 | Table NAT | R-FW | `show ip nat translations` | Traductions présentes | Traductions présentes| Capture 3 |
| T5 | Blocage ICMP vers 8.8.8.8 | PC1 | `ping 8.8.8.8` | Bloqué |Bloqué | Capture 7 |
| T6 | Compteur ACL | R-FW | `show access-lists` | `matches` > 0 | 5 matches| Capture 8 |


---

## 10. Dépannage

| Symptôme | Cause probable | Solution |
|---|---|---|
| Fa0/0 sans IP | Nœud NAT non connecté ou routeur non redémarré | Vérifier le câble, `shutdown` puis `no shutdown` |
| Ping 8.8.8.8 échoue depuis le routeur | Route par défaut incorrecte | Corriger `ip route 0.0.0.0 ...` |
| PC1 joint le routeur mais pas Internet | NAT mal configuré | Vérifier `ip nat inside/outside` et `access-list 1` |
| `ping google.com` échoue mais `ping 1.1.1.1` marche | DNS manquant | `ip dns 8.8.8.8` sur le PC |
| Tout est bloqué après les ACL | `permit ip` oublié ou ACL WAN trop stricte | Vérifier l'ordre avec `show access-lists` |

---