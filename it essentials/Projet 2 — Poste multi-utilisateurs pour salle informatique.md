---
title: Projet 2 — Poste multi-utilisateurs pour salle informatique
tags: [it essentials]

---

# Projet 2 — Poste multi-utilisateurs pour salle informatique

## 📋 Contexte

Un centre de formation veut équiper une salle informatique partagée par plusieurs étudiants qui se relaient dans la journée, chacun avec son propre espace protégé.

## 🎯 Objectifs pédagogiques

-  Créer plusieurs comptes utilisateurs avec permissions différenciées
-  Configurer des dossiers personnels protégés
-  Mettre en place une politique de verrouillage de session
-  Définir une politique de mot de passe minimale
-  Séparer le compte de maintenance des comptes utilisateurs

---

## 🛠️ Marche à suivre

### Étape 1 — Installer le système d'exploitation

- Créer une nouvelle VM sous VMware Workstation
![Capture d'écran win11(1)](https://hackmd.io/_uploads/rkv2hUHYMx.png)
![Capture d'écran win11(2)](https://hackmd.io/_uploads/HJyp2USYfl.png)
![Capture d'écran win11(3)](https://hackmd.io/_uploads/rkdea8rYMx.png)
![Capture d'écran win11(4)](https://hackmd.io/_uploads/B1b02UHKMg.png)
- Allouer les ressources nécessaires (RAM, disque, CPU)
![Capture d'écran win11(5)](https://hackmd.io/_uploads/r1mlaIBFMl.png)
![Capture d'écran win11(6)](https://hackmd.io/_uploads/SJibaUSFfx.png)
![Capture d'écran win11(7)](https://hackmd.io/_uploads/H1yf6LrYMx.png)
![Capture d'écran win11(8)](https://hackmd.io/_uploads/B1MfpUSYfl.png)
- Lancer l'installation du système d'exploitation choisi
- Effectuer les réglages de base (langue, fuseau horaire, réseau)

### Étape 2 — Créer un compte administrateur dédié à la maintenance

- Créer un compte avec droits administrateur, distinct de tout compte d'usage courant
- Nommer le compte de façon explicite (ex. `PC01_WIN11`)
![Capture d'écran win11-compte01](https://hackmd.io/_uploads/Skj7OPLtGx.png)


- Définir un mot de passe fort pour ce compte
- Vérifier que ce compte n'est pas utilisé pour les tâches quotidiennes des étudiants

### Étape 3 — Créer au moins 3 comptes utilisateurs standards

- Créer 3 comptes (ou plus) avec droits **standards** (non-administrateurs)
- Nommer chaque compte de façon claire (ex. `PC02_WIN11`, `PC03_WIN11`, `PC04_WIN11`)
![Capture d'écran 2026-09-15 084539](https://hackmd.io/_uploads/ByhiYP8Yzl.png)

- Attribuer un mot de passe initial à chaque compte
![Capture d'écran win11-compte2](https://hackmd.io/_uploads/ryNdYw8Kzx.png)


### Étape 4 — Configurer les permissions pour cloisonner les fichiers personnels

- Vérifier que chaque compte dispose de son propre dossier personnel (profil utilisateur)
![Capture d'écran 2026-09-15 085756](https://hackmd.io/_uploads/r16q3v8YMl.png)
- Configurer les permissions NTFS/ACL (ou équivalentes) pour qu'un utilisateur ne puisse pas accéder aux fichiers d'un autre
![Capture d'écran 2026-09-15 091458](https://hackmd.io/_uploads/rkcqlu8KMe.png)
![Capture d'écran 2026-09-15 091517](https://hackmd.io/_uploads/Sywne_IYGx.png)
### Étape 5 — Mettre en place le verrouillage automatique après inactivité

- Configurer l'écran de verrouillage automatique après une période d'inactivité (ex. 5-10 minutes)
![Capture d'écran 2026-09-15 095543](https://hackmd.io/_uploads/HJOGqu8YGe.png)

- Vérifier que le déverrouillage nécessite le mot de passe du compte

### Étape 6 — Définir une politique de mot de passe

- Configurer une politique de mot de passe minimale (longueur, complexité)
- Appliquer cette politique à l'ensemble des comptes standards
![Capture d'écran 2026-09-15 101222](https://hackmd.io/_uploads/rk3gROIYze.png)


### Étape 7 — Tester le cloisonnement avec chaque compte

- Se connecter successivement à chaque compte utilisateur
- Créer un fichier test dans chaque profil
![Capture d'écran 2026-09-15 101941](https://hackmd.io/_uploads/B1Hn1YUFfe.png)
- Vérifier qu'un compte ne peut pas consulter/modifier les fichiers d'un autre compte
![Capture d'écran 2026-09-15 102105](https://hackmd.io/_uploads/rJu8gt8YGx.png)
![Capture d'écran 2026-09-15 102218](https://hackmd.io/_uploads/BJTUetIKzx.png)
- Tester l'accès refusé et noter le comportement observé
![Capture d'écran 2026-09-15 103057](https://hackmd.io/_uploads/S1YUfY8YGe.png)

---