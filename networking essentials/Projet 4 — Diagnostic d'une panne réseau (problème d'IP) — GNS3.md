---
title: Projet 4 — Diagnostic d'une panne réseau (problème d'IP) — GNS3
tags: [networking essentials]

---

# Projet 4 — Diagnostic d'une panne réseau (problème d'IP)


## 1. Contexte et objectifs

Un utilisateur signale qu'il **n'accède plus au réseau**. Le formateur simule une panne (câble, IP, DNS, switch). Ici, la panne traitée est de type **problème d'adressage IP**.

**Objectifs :**

- Appliquer une méthodologie de diagnostic **en couches (modèle OSI)**
- Identifier la **cause racine**
- Résoudre la panne et **documenter** l'intervention

**Livrables :**

-  Réseau rétabli et fonctionnel
-  Rapport d'intervention détaillant la démarche
-  Preuves des tests (`ipconfig`, `ping`, `tracert`)

---

## 2. Maquette GNS3

### 2.1 Topologie

![Capture d'écran 2026-10-15 091054](https://hackmd.io/_uploads/SJcky-RiMl.png)


| Équipement | Rôle | Type GNS3 |
|---|---|---|
| PC-USER | Poste de l'utilisateur en panne | VPCS (ou VM Windows/Ubuntu) |
| SW1 | Switch | Ethernet switch GNS3 (ou IOSvL2) |
| R1 | Passerelle par défaut | Routeur Cisco (c7200 / IOSv) |

### 2.2 Plan d'adressage de référence (état sain)

| Équipement | Adresse IP | Masque | Passerelle | DNS |
|---|---|---|---|---|
| R1 (LAN, `g0/0`) | 192.168.10.1 | 255.255.255.0 | — | — |
| PC-USER | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 | 192.168.10.2 |

---

## 3. Préparation

1. Créer le projet GNS3 : `Projet4-Diagnostic-IP`
2. Placer et câbler les équipements selon la topologie
3. Configurer l'état sain (voir §3.1), puis **valider** (§3.2)
4. Faire une **capture d'écran de l'état sain** (preuve « avant »)
5. Enregistrer le projet, puis **remettre la main au formateur** pour qu'il injecte la panne

### 3.1 Configuration de l'état sain

**VPCS :**

```
PC-USER> ip 192.168.10.10/24 192.168.10.1
PC-USER> save
```

**Routeur R1 :**

```
R1> enable
R1# configure terminal
R1(config)# interface g0/0
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# end
R1# write memory
```

### 3.2 Validation de l'état sain

```
PC-USER> ping 192.168.10.1
PC-USER> ping 192.168.10.11
PC-USER> ping 192.168.10.2
```

![Capture d'écran 2026-10-15 090909](https://hackmd.io/_uploads/Hk7p6gAozl.png)
![Capture d'écran 2026-10-15 090918](https://hackmd.io/_uploads/BJ-YkbRjGx.png)


---

## 4. Réception du scénario de panne

Le formateur simule la panne. Notez dans le rapport :

- **Date/heure** de réception du ticket
- **Symptôme rapporté** par l'utilisateur (mot pour mot)
- **Poste concerné**
- **Urgence / impact**


---

## 5. Méthodologie de diagnostic (modèle OSI)

On procède **de bas en haut** : on ne passe à la couche suivante que si la précédente est validée.

:::info
**Règle d'or :** une seule modification à la fois, et on note chaque test et chaque résultat.
:::

### Étape 1 — Couche 1 : Physique

**Question : le lien est-il actif ?**

- Dans GNS3 : vérifier la couleur des points sur les liens (vert = actif, rouge = coupé)
- Vérifier que les équipements sont **démarrés** (bouton ▶)
- Vérifier les interfaces du routeur / switch :

```
R1# show ip interface brief
SW1# show interfaces status
```

| Résultat | Interprétation |
|---|---|
| `up / up` | Couche 1-2 OK → passer à l'étape 2 |
| `administratively down` | Interface désactivée → `no shutdown` |
| `down / down` | Câble débranché / équipement éteint |


### Étape 2 — Couche 3 : Réseau (IP) — _cœur de ce projet_

**3.1 Vérifier la configuration IP du poste**

| Système | Commande |
|---|---|
| VPCS | `show ip` |
| Windows | `ipconfig /all` |
| Linux | `ip a` et `ip route` |


**3.2 Causes IP fréquentes**

| Symptôme observé | Cause probable |
|---|---|
| IP en `169.254.x.x` (APIPA) | Pas de réponse DHCP |
| IP dans un autre sous-réseau | Mauvaise IP saisie |
| Masque incorrect (ex. `255.255.255.252`) | Poste « isolé » du reste du réseau |
| Passerelle absente ou fausse | LAN OK mais pas de sortie vers l'extérieur |
| Même IP que l'autre poste | **Conflit d'adresse IP** |

**3.3 Tests de connectivité**  :

```
ping 192.168.10.1         → la passerelle
```

| Test | Résultat | Conclusion |
|---|---|---|
| Réussit au 3, échoue au 4 | Passerelle fausse ou R1 injoignable | Vérifier passerelle + R1 |
| Réussit au 4, échoue au 5 | Problème routage / NAT / WAN | Vérifier R1 |
| Réussit au 5 | Couche 3 OK → étape 4 |

![Capture d'écran 2026-10-15 093419](https://hackmd.io/_uploads/Sk55MZ0oMe.png)
![Capture d'écran 2026-10-15 093440](https://hackmd.io/_uploads/HJT5G-CjMe.png)

---

## 6. Identification de la cause racine

Compléter après le diagnostic :

| Rubrique | Description |
|---|---|
| **Couche OSI concernée** | _ex. Couche 3 — Réseau_ |
| **Cause racine** | _ex. Masque de sous-réseau erroné sur PC-USER (255.255.255.252 au lieu de 255.255.255.0)_ |
| **Pourquoi ce symptôme ?** | _ex. Le poste considère que 192.168.10.1 est hors de son réseau, donc aucune communication_ |
| **Comment l'avoir confirmée ?** | _ex. `show ip` + comparaison avec le plan d'adressage + ping du poste témoin_ |



---

## 7. Résolution

Comparaisont avec le plan d'addresage de base 
![Capture d'écran 2026-10-15 091054](https://hackmd.io/_uploads/SJcky-RiMl.png)
![Capture d'écran 2026-10-15 093324](https://hackmd.io/_uploads/SyOyEWRizl.png)
![Capture d'écran 2026-10-15 093419](https://hackmd.io/_uploads/SJvbVbRsze.png)
ip router ok mais ip pc fausse

### 7.1 Correction

**VPCS :**

```
PC-USER> ip 192.168.10.11/24 192.168.10.1
PC-USER> save
```

### 7.2 Vérification après correction

Refaire **tous les tests de l'étape 5.3.4** :

```
ping 192.168.10.1
ping 192.168.10.11
```
![Capture d'écran 2026-10-15 093914](https://hackmd.io/_uploads/r18vX-CiGe.png)

---

## 8. Preuves à collecter

| # | Preuve | Moment | Commande |
|---|---|---|---|
| 1 | Topologie GNS3 | Avant | Capture de l'espace de travail |
| 2 | Config IP saine | Avant | `show ip` / `ipconfig /all` |
| 3 | Config IP en panne | Pendant | `show ip` / `ipconfig /all` |
| 4 | Ping en échec | Pendant | `ping` vers passerelle |
| 5 | Traceroute en panne | Pendant | `tracert` / `trace` |
| 6 | Config IP corrigée | Après | `show ip` / `ipconfig /all` |
| 7 | Pings réussis | Après | `ping` passerelle |
| 8 | Traceroute réussi | Après | `tracert` / `trace` |


---

## 9. Rapport d'intervention (modèle)


### 9.1 Symptôme rapporté

_Description donnée par l'utilisateur._

### 9.2 Démarche de diagnostic

| Étape | Couche OSI | Test effectué | Résultat | Conclusion |
|---|---|---|---|---|
| 1 | Physique | Vérification des liens / interfaces | ok| |
| 2 | Réseau | `show ip` / `ipconfig /all` |not reachable | |
| 3 | Réseau | ping passerelle |not reachable | |
| 4 | Réseau | tracert |not reachable | |

### 9.3 Cause racine

_Voir §6._

### 9.4 Action corrective

_Commandes / modifications exactes réalisées (§7.1)._

### 9.5 Tests de validation

_Résultats après correction, avec captures (§7.2)._

### 9.6 Recommandations (prévention)

- Documenter et tenir à jour le **plan d'adressage**
- Utiliser le **DHCP** avec réservations pour limiter les erreurs de saisie manuelle
- Réserver une plage d'adresses fixes pour serveurs et équipements
- Mettre en place une **supervision** (alertes de conflit d'IP, interfaces down)
- Conserver une **fiche de diagnostic** type pour accélérer les interventions

---

## 10. Aide-mémoire des commandes

| Action | VPCS | Windows | Cisco |
|---|---|---|---|
| Voir la config IP | `show ip` | `ipconfig /all` | `show ip interface brief` |
| Tester la connectivité | `ping <ip>` | `ping <ip>` | `ping <ip>` |
| Tracer la route | `trace <ip>` | `tracert <ip>` | `traceroute <ip>` |
| Tester le DNS | `ping <nom>` | `nslookup <nom>` | — |
| Table ARP | `arp` | `arp -a` | `show ip arp` |
| Table de routage | — | `route print` | `show ip route` |
| Table MAC | — | — | `show mac address-table` |
| Sauvegarder | `save` | — | `write memory` |

---