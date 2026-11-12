---
title: Projet 1 — Poste de travail pour un cabinet comptable
tags: [it essentials]

---

---
title: Projet 1 — Poste de travail pour un cabinet comptable
---

# Projet 1 — Poste de travail pour un cabinet comptable

> **Cours de référence :** IT Essentials — Mettre en service un poste de travail ICT avec le système d'exploitation (ICT-187)



---

## 🧭 Contexte

Un petit cabinet comptable de 3 personnes vous engage pour mettre en service le poste de travail de sa nouvelle collaboratrice. Le poste doit être **prêt à l'emploi dès le lundi matin** :

- système d'exploitation installé
- logiciels métier disponibles
- imprimante partagée accessible
- poste sécurisé

---

## 🎯 Objectifs pédagogiques

-Installer un système d'exploitation (Windows 10/11 ou distribution Linux au choix du formateur) sur une machine physique ou virtuelle.
- Créer et configurer un compte utilisateur standard (non administrateur) avec mot de passe conforme à une politique de sécurité simple.
- Installer les logiciels bureautiques de base (suite Office ou équivalent libre, lecteur PDF, navigateur).
- Configurer l'accès à une imprimante partagée sur le réseau local.
- Appliquer les premières mesures de sécurité : mises à jour du système, antivirus, pare-feu activé.

---

## 🛠️ Marche à suivre

### Étape 1 — Installer le système d'exploitation
- Préparer le support d'installation (image ISO fournie).
![Capture d'écran win11(2)](https://hackmd.io/_uploads/rJxXDXBtMl.png)

- Démarrer la machine (VM) sur ce support.
![Capture d'écran win11-final](https://hackmd.io/_uploads/Sye0IXSKMg.png)
- Suivre l'assistant d'installation (partitionnement, langue, fuseau horaire, nom de la machine).


### Étape 2 — Créer les comptes utilisateurs
- Créer un **compte administrateur local** (utilisé uniquement pour la maintenance).
![Capture d'écran win11-compte01](https://hackmd.io/_uploads/rJEGBXSKGe.png)

- Créer un **compte utilisateur standard** pour la collaboratrice, avec un mot de passe conforme à une politique simple (longueur minimale, majuscule, chiffre, caractère spécial).
![Capture d'écran win11-compte](https://hackmd.io/_uploads/ryMHHQBKzx.png)
![Capture d'écran win11-compte2](https://hackmd.io/_uploads/r1iSH7HKze.png)

- Vérifier que le compte standard n'a **pas** de droits administrateur.
![Capture d'écran win11-compte02](https://hackmd.io/_uploads/Bk97BQSFzx.png)

![Capture d'écran win11-comptes](https://hackmd.io/_uploads/SJGhrmrKGx.png)


### Étape 3 — Configurer le réseau
- Définir une adresse IP.
![Capture d'écran win11-ip](https://hackmd.io/_uploads/SJtwDmrYGe.png)

- Renommer la machine selon la convention donnée (ex. `PC01_WIN11`).
![Capture d'écran 2026-09-14 131856](https://hackmd.io/_uploads/S1NVuIrtGe.png)

- Configurer le groupe de travail (Workgroup) ou le domaine si applicable.
![Capture d'écran win11-work](https://hackmd.io/_uploads/Hyj6y_HYze.png)

- Vérifier la connectivité (ping vers la passerelle ou un autre poste).
![Capture d'écran win11-ping](https://hackmd.io/_uploads/SJYOP7rKzx.png)


### Étape 4 — Installer les logiciels métier de base
- Installer la suite bureautique (Office ou équivalent libre type LibreOffice).
- Installer un lecteur PDF.
- Installer un navigateur web.
- Installer les éventuels logiciels métier complémentaires listés par le formateur.
![Capture d'écran win11-instal](https://hackmd.io/_uploads/SkpwZHSKMe.png)


### Étape 5 — Ajouter l'imprimante réseau partagée
- Ajouter une imprimante partagée sur le réseau local (réelle ou simulée).
![Capture d'écran 2026-09-14 112153](https://hackmd.io/_uploads/SyzanVrYzx.png)

- Définir cette imprimante comme imprimante par défaut si demandé.
![Capture d'écran 2026-09-14 112115](https://hackmd.io/_uploads/Hygs3ESKMe.png)

- Lancer un test d'impression et vérifier le résultat.
![Capture d'écran 2026-09-14 111932](https://hackmd.io/_uploads/SyzH2EBKfx.png)

### Étape 6 — Vérifier les mises à jour
- Ouvrir le module de mises à jour (Windows Update).
![Capture d'écran win11-maj](https://hackmd.io/_uploads/BJ3cj7BtGe.png)
- Lancer une recherche et installer les mises à jour disponibles.
![Capture d'écran win11-rmaj](https://hackmd.io/_uploads/r1VanXBtfl.png)

- Vérifier que les mises à jour automatiques sont activées.
![Capture d'écran 2026-09-14 130732](https://hackmd.io/_uploads/Bks9S8StMg.png)


### Étape 7 — Sécuriser le poste
- Activer/vérifier l'antivirus (Windows Defender ou équivalent).
![Capture d'écran win11-antivirus](https://hackmd.io/_uploads/S1EK9XBKGg.png)


- Activer le pare-feu.
![Capture d'écran win11-parefeu](https://hackmd.io/_uploads/BJwuKQBKzg.png)
![Capture d'écran win11-secu](https://hackmd.io/_uploads/ByCiEUBtMg.png)

---