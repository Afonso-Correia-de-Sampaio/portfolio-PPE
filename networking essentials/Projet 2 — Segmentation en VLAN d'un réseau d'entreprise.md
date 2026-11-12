---
title: Projet 2 — Segmentation en VLAN d'un réseau d'entreprise
tags: [networking essentials]

---

# Projet 2 — Segmentation en VLAN d'un réseau d'entreprise

---

## 1. Contexte et objectifs

Une entreprise veut **séparer le trafic du service comptabilité** de celui des autres employés pour des raisons de sécurité.

**Objectifs pédagogiques**

- Comprendre le principe des VLAN.
- Configurer au moins 2 VLAN sur un switch manageable.
- Vérifier l'étanchéité entre VLAN.

---

## 2. Rappel théorique (VLAN)

Un **VLAN** (Virtual LAN) découpe logiquement un switch en plusieurs réseaux de niveau 2 indépendants.

- Chaque VLAN = **un domaine de broadcast distinct**.
- Une machine ne voit que les machines du même VLAN, même si elles sont branchées sur le même switch physique.
- Un port **access** appartient à un seul VLAN (trames non taguées, cas des PC).
- Un port **trunk** transporte plusieurs VLAN (trames taguées 802.1Q, entre switchs ou vers un routeur).
- Pour que deux VLAN communiquent, il faut un équipement de **niveau 3** (routeur ou switch L3). Sans lui, ils sont isolés.

---

## 3. Architecture cible

### 3.1 Schéma logique

![Capture d'écran 2026-10-13 113750](https://hackmd.io/_uploads/r1gW2usofg.png)



### 3.2 Plan des VLAN

| VLAN ID | Nom | Réseau | Ports du switch |
|---|---|---|---|
| 10 | COMPTA | 192.168.10.0/24 | E1, E2 |
| 20 | EMPLOYES | 192.168.20.0/24 | E3, E4 |

### 3.3 Plan d'adressage

| Équipement | VLAN | Adresse IP | Masque | Port switch |
|---|---|---|---|---|
| PC-1 | 10 | 192.168.10.11 | 255.255.255.0 | E1 |
| PC-2 | 10 | 192.168.10.12 | 255.255.255.0 | E2 |
| PC-4 | 20 | 192.168.20.11 | 255.255.255.0 | E4 |
| PC-3 | 20 | 192.168.20.12 | 255.255.255.0 | E3 |

---

## 4. Préparation de GNS3

### 4.1 Matériel nécessaire

- 1 switch manageable : **Cisco IOSvL2** (ou IOU L2) importé dans GNS3
- 4 PC : nœuds **VPCS** (légers) ou VM/Docker si vous voulez des postes plus réalistes

> **Alternative** : le switch intégré « Ethernet switch » de GNS3 permet aussi de définir des ports *access* avec un VLAN (clic droit → *Configure*), mais il n'a pas de CLI. Un switch Cisco est préférable pour montrer une vraie configuration et les commandes `show`.

### 4.2 Création du projet

1. Ouvrir GNS3 → **File → New blank project** → nom : `Projet2_VLAN`.
2. Glisser **1 switch** (IOSvL2) et **4 VPCS** dans l'espace de travail.
3. Renommer les nœuds : `SW1`, `PC-1`, `PC-2`, `PC-3`, `PC-4`.
4. Câbler (outil « Add a link ») :

| PC | Interface PC | Switch | Port |
|---|---|---|---|
| PC-1 | e0 | SW1 | E1 |
| PC-2 | e0 | SW1 | E2 |
| PC-3 | e0 | SW1 | E3 |
| PC-4 | e0 | SW1 | E4 |

5. Démarrer tous les nœuds (bouton ▶) et ouvrir la console de chacun (double-clic).

---

## 5. Configuration du switch

Ouvrir la console de `SW1`.

### 5.1 Configuration de base

```cisco
enable
configure terminal
hostname SW1
no ip domain-lookup
banner motd # Acces reserve - Switch SW1 #
```

### 5.2 Création des VLAN

```cisco
vlan 10
 name COMPTA
vlan 20
 name EMPLOYES
exit
```

### 5.3 Assignation des ports aux VLAN

```cisco
interface range GigabitEthernet0/0 - 1
 description Postes comptabilite
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 exit

interface range GigabitEthernet0/2 - 3
 description Postes employes
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 exit
```

### 5.4 Sauvegarde

```cisco
write memory
```

---

![Capture d'écran 2026-10-13 114041](https://hackmd.io/_uploads/BJv3n_sizx.png)

## 6. Configuration des postes (VPCS)

Dans la console de chaque PC :

```text
PC-1> ip 192.168.10.11/24
PC-1> save

PC-2> ip 192.168.10.12/24
PC-2> save

PC-4> ip 192.168.20.11/24
PC-4> save

PC-3> ip 192.168.20.12/24
PC-3> save
```

Vérifier avec `show ip`.


![Capture d'écran 2026-10-13 114920](https://hackmd.io/_uploads/HkznR_jjzx.png)
![Capture d'écran 2026-10-13 114939](https://hackmd.io/_uploads/H1UA0_jsfg.png)
![Capture d'écran 2026-10-13 114946](https://hackmd.io/_uploads/HkkJkKisMx.png)
![Capture d'écran 2026-10-13 114952](https://hackmd.io/_uploads/SJmkkKjozl.png)

---

## 7. Vérification de la configuration

Sur `SW1` :

```cisco
show vlan brief
show interfaces status
show running-config | section interface
show mac address-table
```

**Résultat attendu de `show vlan brief`** :

```text
VLAN Name       Status    Ports
10   COMPTA     active    E1, E2
20   EMPLOYES   active    E3, E4
```

![Capture d'écran 2026-10-13 114041](https://hackmd.io/_uploads/r15daOjszx.png)


---

## 8. Tests de connectivité et preuve d'étanchéité

### 8.1 Plan de tests

| # | Test | Depuis | Vers | Résultat attendu |
|---|---|---|---|---|
| T1 | Connectivité dans VLAN 10 | PC-1 | PC-2 (192.168.10.12) | ✅ Réussi |
| T2 | Connectivité dans VLAN 20 | PC-4 | PC-3 (192.168.20.12) | ✅ Réussi |
| T3 | Comptabilité → Employés | PC-1 | PC-4 (192.168.20.11) | ❌ Échec |
| T4 | Employés → Comptabilité | PC-4 | PC-1 (192.168.10.11) | ❌ Échec |

### 8.2 Tests T1 à T4

```text
PC-1> ping 192.168.10.12     # T1 : doit répondre
PC-4>    ping 192.168.20.12     # T2 : doit répondre
PC-1> ping 192.168.20.11     # T3 : doit échouer
PC-4>    ping 192.168.10.11     # T4 : doit échouer
```

> Sur VPCS sans passerelle, un ping vers un autre sous-réseau échoue dès l'envoi (« no gateway found »). Ce résultat prouve seulement qu'il n'y a pas de routage, **pas** l'étanchéité du VLAN. D'où le test T5.

### 8.3 Test T5 — Preuve rigoureuse (niveau 2)

Objectif : éliminer l'argument « ça échoue seulement parce que les sous-réseaux sont différents ».

1. Reconfigurer **temporairement** `PC-4` dans le sous-réseau de la comptabilité :

```text
PC-4> ip 192.168.10.50/24
```

2. Depuis `PC-4` (VLAN 20), pinguer `PC-1` (VLAN 10), même sous-réseau :

```text
PC-4> ping 192.168.10.11
```

3. **Résultat attendu** : timeout, car la requête ARP (broadcast) ne franchit pas la frontière du VLAN.
4. Vérifier : `show arp` sur PC-4 ne contient pas l'adresse MAC de PC-1.
5. Remettre ensuite l'adresse d'origine : `ip 192.168.20.11/24`.

![Capture d'écran 2026-10-13 114621](https://hackmd.io/_uploads/HJslCujiMe.png)
![Capture d'écran 2026-10-13 114754](https://hackmd.io/_uploads/SJpIAuojzl.png)
![Capture d'écran 2026-10-13 114708](https://hackmd.io/_uploads/HJCmROiiMe.png)
![Capture d'écran 2026-10-13 114828](https://hackmd.io/_uploads/SJKO0OssGl.png)

---

## 9. Dépannage

| Symptôme | Cause probable | Solution |
|---|---|---|
| Le ping échoue dans un même VLAN | Mauvais VLAN sur le port, ou IP/masque erroné | `show vlan brief`, puis `show ip` sur le PC |
| Port en `down` | Nœud non démarré ou câble absent | Vérifier les liens (points verts) dans GNS3 |
| Port en `err-disabled` | Port sécurisé ou erreur détectée | `shutdown` puis `no shutdown` sur l'interface |
| VLAN absent de `show vlan brief` | Création oubliée | Créer le VLAN (§5.2) |
| Config perdue au redémarrage | `write memory` oublié | Refaire la config et sauvegarder |
| Premier ping en timeout | Résolution ARP en cours | Relancer le ping |

---
