---
title: Projet 4 — Dépannage et diagnostic d'un poste en panne
tags: [it essentials]

---

# Projet 4 — Dépannage et diagnostic d'un poste en panne
## 📋 Contexte

Le service support d'une entreprise reçoit un poste signalé en panne. Le formateur attribue un scénario de panne (démarrage lent, pilote manquant, service en échec...).

---

## 🎯 Objectifs pédagogiques

- Utiliser une méthodologie de diagnostic structurée.
- Exploiter les outils de diagnostic du système.
- Résoudre un problème matériel ou logiciel courant.
- Rédiger un rapport d'intervention clair.

---

## 🛠️ Marche à suivre

### Étape 1 — Réception du scénario de panne
- Rencontrer le formateur pour recevoir ou choisir un scénario de panne (réseau).
- Noter précisément les symptômes décrits par le "client".
### Étape 2 — Reproduction de la panne
-  Préparer un poste de test (VM VMware Workstation dédiée).
- Reproduire ou simuler la panne attribuée sur ce poste.
- Prendre une première capture d'écran de l'état défaillant du poste.
![Capture d'écran 2026-09-17 085733](https://hackmd.io/_uploads/SyTjkMtKMg.png)

### Étape 3 — Diagnostic à l'aide des outils système
- Ouvrir l'**Observateur d'événements** (`eventvwr.msc`) et relever les journaux pertinents (Système, Application).
![Capture d'écran 2026-09-16 132022](https://hackmd.io/_uploads/Sk45oldtze.png)
![Capture d'écran 2026-09-16 132056](https://hackmd.io/_uploads/SJdjjeOtfl.png)
- Vérifier le **Gestionnaire de périphériques** (`devmgmt.msc`) pour repérer d'éventuels pilotes manquants ou en erreur.
![Capture d'écran 2026-09-16 132223](https://hackmd.io/_uploads/ByH-2lOFzx.png)
- Contrôler les **Services** (`services.msc`) pour identifier un service arrêté ou en échec de démarrage.
![Capture d'écran 2026-09-16 132458](https://hackmd.io/_uploads/Syi53g_Kfg.png)
- Utiliser au besoin d'autres outils complémentaires : Gestionnaire des tâches, Analyseur de performances, `msconfig`, invite de commandes (`sfc /scannow`, `chkdsk`, etc.).
![Capture d'écran 2026-09-16 133104](https://hackmd.io/_uploads/H1MMRgOYGl.png)
![Capture d'écran 2026-09-17 090426](https://hackmd.io/_uploads/rkyMZztYGg.png)

### Étape 4 — Documentation des hypothèses
- Lister chaque hypothèse envisagée avant de la tester.
panne réseau, panne carte réseau
- Pour chaque hypothèse : décrire le test effectué, le résultat obtenu, et la conclusion (validée ou écartée).
connection internet, recherche sur navigater
- onserver une trace (capture d'écran) pour chaque test significatif, y compris les hypothèses écartées.
![Capture d'écran 2026-09-17 085733](https://hackmd.io/_uploads/SyTjkMtKMg.png)
![Capture d'écran 2026-09-17 090426](https://hackmd.io/_uploads/rkyMZztYGg.png)
### Étape 5 — Résolution du problème
- Appliquer la correction identifiée (réinstallation de pilote, relance de service, réparation système, etc.).
![Capture d'écran 2026-09-17 090744](https://hackmd.io/_uploads/SJNAbMtYfx.png)
![Capture d'écran 2026-09-17 090810](https://hackmd.io/_uploads/BkT1GMYFGg.png)
- Vérifier que le poste fonctionne à nouveau normalement.
![Capture d'écran 2026-09-17 091105](https://hackmd.io/_uploads/BkKcMMKKzx.png)
- Prendre une capture d'écran de l'état final fonctionnel.
![Capture d'écran 2026-09-17 091034](https://hackmd.io/_uploads/SJh_zzYFGl.png)
---