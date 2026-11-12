---
title: Projet 5 — Déploiement standardisé d'un poste
tags: [it essentials]

---

# Projet 5 — Déploiement standardisé d'un poste
## 1. Contexte

Une entreprise doit équiper **10 nouveaux postes identiques**. L'objectif de ce projet est de préparer une **méthode standardisée de déploiement rapide**, afin d'éviter de réinstaller manuellement chaque machine une par une.

---

## 2. Objectifs pédagogiques

- Comprendre le principe d'une image système de référence ou d'une installation automatisée.
- Préparer un poste de référence conforme à un cahier des charges.
- Créer une image ou un script de déploiement.
- Documenter la procédure pour qu'elle soit reproductible.

---

## 3. Cahier des charges du poste standard
| Élément | Spécification |
|---|---|
| Système d'exploitation |Windows|
| Édition / version |11 PRO |
| Nom d'ordinateur (convention) |PC01_WIN11 |
| Compte(s) utilisateur |PC01_WIN11 |
| Logiciels à installer| Chrome,LibreOffice|
| Pilotes requis |8RAM,2Cores|
| Paramètres réseau |NAT|

---

## 4. Marche à suivre

### Étape 1 — Définir le cahier des charges du poste standard

Lister les besoins de l'entreprise (usage des postes, logiciels métier, contraintes réseau).
### Étape 2 — Installer et configurer le poste de référence

1. Créer une nouvelle machine virtuelle dans VMware Workstation qui servira de **poste de référence**.
2. Installer le système d'exploitation choisi selon le cahier des charges.
3. Installer tous les logiciels, pilotes et mises à jour nécessaires.
4. Appliquer les personnalisations et paramètres définis (réseau, sécurité, comptes).
5. Vérifier que le poste correspond exactement au cahier des charges avant de poursuivre.

### Étape 3 — Créer un script de déploiement automatisé
**Script d'installation automatisée**
Rédiger un script (PowerShell) qui automatise :
   - l'installation des logiciels,
   - la configuration des paramètres,
![Capture d'écran 2026-09-17 150711](https://hackmd.io/_uploads/S1sXLwttfx.png)
### Étape 4 — Tester le déploiement sur une seconde machine

1. Créer une seconde machine virtuelle vierge dans VMware Workstation.
2. Déployer le script d'installation automatisée sur cette seconde machine.
![Capture d'écran 2026-09-17 152714](https://hackmd.io/_uploads/SkVpcDKFfx.png)
4. Vérifier que le résultat est conforme au cahier des charges (mêmes logiciels, mêmes paramètres, bon fonctionnement).
5. Documenter (captures d'écran) chaque étape du test comme preuve.
![Capture d'écran 2026-09-17 153109](https://hackmd.io/_uploads/S1m2svFtMx.png)
![Capture d'écran 2026-09-17 154209](https://hackmd.io/_uploads/rkmBCvFKze.png)
### Étape 5 — Comparer le temps d'installation manuelle vs automatisée

1. Chronométrer le temps nécessaire pour une **installation manuelle complète** d'un poste.
2. Chronométrer le temps nécessaire pour le **déploiement automatisé**  réalisé à l'étape 4.
3. Construire un tableau comparatif des deux temps.

| Méthode | Temps mesuré |
|---|---|
| Installation manuelle | 1h30|
| Déploiement automatisé | 30min|

4. Calculer le gain de temps et estimer le gain total pour les 10 postes de l'entreprise.

on gagne une heure par poste donc un total de 10h gagner pour les dix postes

---