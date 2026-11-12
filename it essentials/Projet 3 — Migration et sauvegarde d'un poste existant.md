---
title: Projet 3 — Migration et sauvegarde d'un poste existant
tags: [it essentials]

---

---
title: Projet 3 — Migration et sauvegarde d'un poste existant
---
# Projet 3 — Migration et sauvegarde d'un poste existant
**Cours de référence :** IT Essentials — Mettre en service un poste de travail ICT avec le système d'exploitation
## Contexte
Un poste vieillissant doit être réinstallé complètement, mais l'utilisateur souhaite retrouver ses documents et paramètres essentiels après l'opération.
## Objectifs pédagogiques
- Sauvegarder les données utilisateur avant réinstallation
- Réinstaller proprement le système
- Restaurer les données après installation
- Documenter une procédure de migration reproductible
---
## Marche à suivre
### Étape 1 — Inventaire des données à conserver
- Identifier l'utilisateur et le poste concerné (nom, OS actuel, rôle du poste)
- Lister les dossiers à sauvegarder (ex. `Documents`, `Bureau`, `Images`, `Téléchargements`, profil de messagerie, favoris navigateur)
![Capture d'écran 2026-09-15 112253](https://hackmd.io/_uploads/Bytee5LKMx.png)
- Lister les paramètres essentiels à conserver (papier peint, résolution, imprimante, connexions réseau/VPN, comptes de messagerie, favoris)
- Lister les logiciels installés à réinstaller (nom + version si possible)
![Capture d'écran 2026-09-15 112848](https://hackmd.io/_uploads/r1qJg58KMx.png)
---
### Étape 2 — Sauvegarde des données
- Choisir le support de sauvegarde (disque externe, clé USB, partage réseau, cloud)
- Copier l'ensemble des données identifiées à l'étape 1 vers le support choisi
![Capture d'écran 2026-09-16 084946](https://hackmd.io/_uploads/HJ8733DtMl.png)
- Exporter les paramètres spécifiques si nécessaire (favoris navigateur, profil de messagerie, pilotes d'imprimante)
- Nommer clairement le dossier de sauvegarde (ex. `Backup_NomPoste_AAAAMMJJ`)
![Capture d'écran 2026-09-16 084424](https://hackmd.io/_uploads/SkyJs3wFzl.png)
---
### Étape 3 — Vérification de l'intégrité de la sauvegarde
- Comparer le nombre de fichiers et la taille totale entre la source et la sauvegarde
- Ouvrir quelques fichiers au hasard depuis la sauvegarde pour vérifier qu'ils ne sont pas corrompus
---
### Étape 4 — Réinstallation propre du système d'exploitation
- Préparer le support d'installation (clé USB bootable ou ISO)
- Sauvegarder/noter la clé de licence si nécessaire
- Supprimer les anciennes partitions et créer un nouveau partitionnement propre
![Capture d'écran 2026-09-16 085353](https://hackmd.io/_uploads/rkOfThwKfl.png)
- Démarrer sur le support d'installation
![Capture d'écran win11(1)](https://hackmd.io/_uploads/BJ6R2nwFzl.png)
- Installer le système d'exploitation
![Capture d'écran win11(2)](https://hackmd.io/_uploads/r1ZhnhDKfl.png)
- Installer les pilotes nécessaires (carte réseau, carte graphique, chipset, etc.)
![Capture d'écran win11(6)](https://hackmd.io/_uploads/Syzs2hPFzl.png)
![Capture d'écran win11(7)](https://hackmd.io/_uploads/r1MH62vKfx.png)
![Capture d'écran win11(8)](https://hackmd.io/_uploads/HJTHTnwtzx.png)
![Capture d'écran 2026-09-16 150428](https://hackmd.io/_uploads/HyExEMuYzl.png)
- Effectuer les mises à jour Windows/OS
---
### Étape 5 — Réinstallation des logiciels nécessaires
- Réinstaller les logiciels identifiés dans l'inventaire (étape 1)
![Capture d'écran win11-instal](https://hackmd.io/_uploads/BJf_3nvYMx.png)
- Vérifier que chaque logiciel se lance correctement
- Mettre à jour les logiciels installés
---
### Étape 6 — Restauration des données et vérification
- Copier les données depuis le support de sauvegarde vers l'emplacement d'origine
- Vérifier que tous les fichiers attendus sont présents
![Capture d'écran 2026-09-16 084946](https://hackmd.io/_uploads/HJ8733DtMl.png)
- Réimporter les paramètres exportés (favoris, profil de messagerie, etc.)
- Vérifier l'intégrité des données restaurées
---
### Étape 7 — Reconfiguration des paramètres essentiels
- Reconfigurer le réseau (IP, Wi-Fi, VPN si nécessaire)
![Capture d'écran win11-ip](https://hackmd.io/_uploads/HkA8FhvYMl.png)
- Reconfigurer l'imprimante et les périphériques
![Capture d'écran win11-inprim](https://hackmd.io/_uploads/S1WFK3DFGe.png)
- Reconfigurer les comptes utilisateurs et permissions
![Capture d'écran win11-comptes](https://hackmd.io/_uploads/HkRoY3wtfe.png)
- Reconfigurer les paramètres d'affichage et de personnalisation
- Vérifier le bon fonctionnement général du poste
---