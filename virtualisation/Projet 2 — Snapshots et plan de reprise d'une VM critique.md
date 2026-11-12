---
title: Projet 2 — Snapshots et plan de reprise d'une VM critique
tags: [virtualisation]

---

# Projet 2 — Snapshots et plan de reprise d'une VM critique


## 1. Contexte et objectifs

Le serveur applicatif virtualisé d'une entreprise doit pouvoir être restauré rapidement en cas de mauvaise mise à jour ou de panne.

**Objectifs :**

-  Comprendre le mécanisme des snapshots
-  Mettre en place une politique de snapshots réguliers
-  Simuler un incident et restaurer la VM
-  Documenter un plan de reprise simple

---

## 2. Principe d'un snapshot (à comprendre avant de commencer)

Un snapshot fige l'**état d'une VM** à un instant T :

- **Disque** : à la création, le disque d'origine (`.vmdk`) passe en lecture seule. Toutes les écritures suivantes vont dans un fichier delta (`-000001.vmdk`).
- **Mémoire (RAM)** : optionnellement sauvegardée (`.vmem` / `.vmsn`) pour reprendre la VM exactement là où elle était.
- **Configuration** : fichier `.vmsd` (liste des snapshots) et `.vmsn` (état).

**Retour arrière (Revert)** : le fichier delta est abandonné, la VM retrouve l'état figé.

---

## 3. Étape 1 — Créer la VM de test avec une application simple

### 3.1 Création de la VM

1. VMware Workstation → **File → New Virtual Machine → Typical**
2. ISO : Ubuntu Server 22.04/24.04 (ou Windows Server selon votre choix)
3. Ressources suggérées :

| Paramètre | Valeur |
|-----------|--------|
| Nom | `SRV-APP01` |
| vCPU | 2 |
| RAM | 2 Go |
| Disque | 20 Go (thin) |
| Réseau | NAT ou Bridged |

4. Installer l'OS, créer un utilisateur, noter l'adresse IP : `ip a`

### 3.2 Installer une application simple (exemple Apache + page web)

```bash
sudo apt update
sudo apt install -y apache2
sudo systemctl enable --now apache2
```

Créer une page et un fichier de données « critiques » :

```bash
echo "<h1>Application SRV-APP01 - version 1</h1>" | sudo tee /var/www/html/index.html
sudo mkdir -p /srv/donnees
echo "Donnees critiques - client A,B,C" | sudo tee /srv/donnees/clients.txt
```

### 3.3 Vérification

```bash
systemctl status apache2
curl http://localhost
cat /srv/donnees/clients.txt
```

![Capture d'écran 2026-10-06 131137](https://hackmd.io/_uploads/H17qtUfoMg.png)


---

## 4. Étape 2 — Politique de snapshots et snapshot avant modification

### 4.1 Politique de snapshots proposée

| Type | Fréquence | Rétention | Nom (convention) |
|------|-----------|-----------|------------------|
| Avant intervention | À chaque mise à jour / changement | Jusqu'à validation (max 3 jours) | `AVANT-MAJ_AAAA-MM-JJ` |
| Régulier | Quotidien (hors production) | 7 derniers | `AUTO_AAAA-MM-JJ` |
| Référence | Après installation validée | Permanent | `BASE_v1` |

**Règles :** 2 à 3 snapshots maximum dans la chaîne, pas de snapshot « oublié », suppression après validation de la modification.

### 4.2 Prendre le snapshot de référence

1. Éteindre ou laisser la VM allumée (VM allumée + option *Snapshot the virtual machine's memory* pour un état complet)
2. **VM → Snapshot → Take Snapshot…**
3. Nom : `BASE_v1` — Description : `VM installée, Apache OK, données présentes`

### 4.3 Snapshot avant la modification volontaire

1. **VM → Snapshot → Take Snapshot…**
2. Nom : `AVANT-MAJ_2026-10-06` — Description : `Avant mise à jour système`
3. Ouvrir le **Snapshot Manager** (`Ctrl+M`) et vérifier l'arborescence
![Capture d'écran 2026-10-06 135853](https://hackmd.io/_uploads/ry6KfDGsMg.png)
### 4.4 (Optionnel) Automatiser les snapshots réguliers

Avec `vmrun` (Windows, depuis l'hôte) :

```bat
"C:\Program Files (x86)\VMware\VMware Workstation\vmrun.exe" -T ws snapshot "D:\VMs\SRV-APP01\SRV-APP01.vmx" "AUTO_2026-10-06"
```

À planifier avec le **Planificateur de tâches Windows** (quotidien).

---

## 5. Étape 3 — Modification volontaire puis simulation d'incident

### 5.1 Modification volontaire (la « mauvaise mise à jour »)

```bash
sudo apt update && sudo apt -y upgrade
echo "<h1>Application SRV-APP01 - version 2 (MAJ)</h1>" | sudo tee /var/www/html/index.html
```

### 5.2 Incident à simuler

Choisir **au moins un** scénario :

**Scénario A — Suppression de fichiers**

```bash
sudo rm -f /srv/donnees/clients.txt
sudo rm -rf /var/www/html/*
```

**Scénario B — Panne applicative**

```bash
sudo systemctl stop apache2
sudo mv /etc/apache2/apache2.conf /etc/apache2/apache2.conf.bak
sudo systemctl start apache2   # doit échouer
```

### 5.3 Constat de l'incident

```bash
systemctl status apache2
curl http://localhost
ls /srv/donnees
```
---

## 6. Étape 4 — Restauration à partir du snapshot

1. **Noter l'heure de début** (chronomètre prêt)
2. **VM → Snapshot → Revert to Snapshot** (ou Snapshot Manager → sélectionner `AVANT-MAJ_...` → **Go To**)
3. Confirmer (les changements non sauvegardés seront perdus)
4. Redémarrer la VM si nécessaire

---

## 7. Étape 5 — Mesure du temps de restauration

### 7.1 Méthode

| Repère | Moment à chronométrer |
|--------|-----------------------|
| **T0** | Clic sur *Revert to Snapshot* |
| **T1** | Fin du retour arrière (VM de nouveau disponible) |
| **T2** | Service validé (`curl` OK + fichier présent) |

**Temps de restauration = T2 − T0**

### 7.2 Vérification après restauration

```bash
systemctl status apache2
curl http://localhost       # doit afficher la version 1
cat /srv/donnees/clients.txt
```

![Capture d'écran 2026-10-06 140035](https://hackmd.io/_uploads/HyQxXPGiMl.png)


---

## 8. Étape 6 — Plan de reprise (1 page)

### 8.1 Définitions simplifiées

- **RTO** (Recovery Time Objective) : durée maximale d'indisponibilité acceptable.
- **RPO** (Recovery Point Objective) : quantité maximale de données qu'on accepte de perdre (= âge du dernier snapshot).

### 8.2 Modèle à compléter

# Plan de reprise — SRV-APP01

| Élément | Contenu |
|---------|---------|
| **Système concerné** | SRV-APP01 (serveur applicatif Apache) |
| **Criticité** | Élevée |
| **Risques couverts** | Mauvaise mise à jour, suppression de fichiers, panne applicative |
| **Risques non couverts** | Perte de l'hôte, corruption du disque physique, sinistre du site (nécessitent un backup externe) |
| **RPO** | ex. 24 h (snapshot quotidien) |
| **RTO** | ex. 15 min (valeur mesurée : … min) |
| **Politique de snapshots** | Voir §4.1 |
| **Responsable** | Nom de l'administrateur |

**Procédure de reprise :**

1. Constater l'incident et le déclarer
2. Identifier le dernier snapshot sain (Snapshot Manager)
3. Prévenir les utilisateurs (indisponibilité)
4. Éteindre la VM si nécessaire, puis **Revert to Snapshot**
5. Démarrer la VM, contrôler le service et les données
6. Informer les utilisateurs de la reprise
7. Analyser la cause, consigner dans le journal d'incident

**Limites et améliorations :** snapshot ≠ sauvegarde → ajouter un backup externe (copie du dossier de la VM, export OVF), tester la restauration périodiquement.

---