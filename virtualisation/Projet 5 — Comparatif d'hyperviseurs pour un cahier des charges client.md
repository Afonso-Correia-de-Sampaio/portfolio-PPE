---
title: Projet 5 — Comparatif d'hyperviseurs pour un cahier des charges client
tags: [virtualisation]

---

### Projet 5 — Comparatif d'hyperviseurs pour un cahier des charges client
---

## 1. Contexte et objectif

Un client hésite entre plusieurs solutions de virtualisation (**Proxmox VE, Hyper-V, VMware, VirtualBox**). Il faut lui remettre une **recommandation argumentée** fondée sur des tests concrets.

### Stratégie retenue

| Hyperviseur | Travail | Justification |
|-------------|---------|---------------|
| **Proxmox VE** | ✅ Installé et testé | Open source, gratuit, basé sur KVM |
| **VMware ESXi** | ✅ Installé et testé | Référence du marché, écosystème vSphere |
| **Hyper-V** | 📖 Analysé (test optionnel) | Natif Windows Server |
| **VirtualBox** | 📖 Analysé | Orienté poste de travail, pas serveur |


---

## 2. Prérequis

### 2.1 Machine hôte

-  CPU avec virtualisation matérielle (**Intel VT-x/EPT** ou **AMD-V/RVI**) activée dans le BIOS/UEFI
-  **≥ 24 Go de RAM** conseillés (16 Go au minimum)
-  **≥ 150 Go** d'espace disque libre (SSD conseillé)
-  VMware Workstation installé et à jour

### 2.2 ISO à télécharger

-  Proxmox VE (site officiel proxmox.com)
-  VMware ESXi (portail Broadcom, compte requis)
-  Ubuntu Server LTS (VM de test, identique sur les deux hyperviseurs)
-  *(optionnel)* Windows Server en évaluation pour Hyper-V

### 2.3 Conflit Hyper-V / VMware Workstation (hôte Windows)

Si Hyper-V, la plateforme d'hyperviseur Windows ou Device Guard sont actifs sur l'hôte, Workstation passe en mode dégradé et le nested devient instable.

```powershell
# Vérifier (PowerShell administrateur)
systeminfo | findstr /i "hyperviseur hypervisor"

# Désactiver puis redémarrer
bcdedit /set hypervisorlaunchtype off
```


---

## 3. Définir les critères de comparaison

Figer les critères et leur **pondération** avant les tests. Exemple à adapter au scénario du formateur :

| Critère | Description | Poids (exemple) |
|---------|-------------|-----------------|
| 💰 Coût | Achat, abonnement, support | 20 % |
| 📜 Licences | Modèle, restrictions, risque contractuel | 10 % |
| 🛠️ Facilité d'administration | Interface, courbe d'apprentissage, automatisation | 20 % |
| ⚡ Performances | CPU, RAM, disque, réseau (mesures) | 20 % |
| 🌐 Écosystème | Sauvegarde, API, communauté, intégrateurs | 15 % |
| 🔐 Fonctions entreprise | HA, migration à chaud, snapshots, stockage partagé | 15 % |

### Cas d'usage identique (même test partout)

1. Créer **1 VM Ubuntu Server** : 2 vCPU, 2 Go RAM, 20 Go disque
2. Installer **Nginx**
3. Prendre un **snapshot** puis le restaurer
4. Mesurer CPU, RAM, disque, réseau avec les mêmes outils
5. Noter le **temps** et la **difficulté** de chaque opération

---

## 4. Préparer les hyperviseurs dans VMware Workstation

### 4.1 Réseau

1. **Edit → Virtual Network Editor** (en administrateur)
2. Utiliser **VMnet8 (NAT)** pour l'accès Internet
3. Noter le sous-réseau et la passerelle
![Capture d'écran 2026-10-09 091039](https://hackmd.io/_uploads/B13uQf8jzg.png)

### 4.2 Créer la VM Proxmox VE (PVE-LAB01)

1. **File → New Virtual Machine → Custom (advanced)**
2. ISO : Proxmox VE
3. Système invité : **Linux → Debian 12.x 64-bit**
4. Nom : `PVE-LAB01`
5. Ressources :

| Paramètre | Valeur |
|-----------|--------|
| vCPU | 4 |
| RAM | 8 Go |
| Disque | 60 Go |
| Réseau | NAT (VMnet8) |


### 4.3 Créer la VM ESXi (ESX-LAB01)

1. **File → New Virtual Machine → Custom (advanced)**
2. Système invité : **VMware ESX → VMware ESXi 8** (ou 7 selon l'ISO)
3. Nom : `ESX-LAB01`
4. Ressources :

| Paramètre | Valeur |
|-----------|--------|
| vCPU | 4 |
| RAM | 12 Go (8 Go minimum) |
| Disque | 60 Go |
| Réseau | NAT (VMnet8) |



### 4.4 Installer Proxmox VE

1. Démarrer `PVE-LAB01` → **Install Proxmox VE (Graphical)**
2. Accepter la licence, choisir le disque
3. Pays **Switzerland**, fuseau `Europe/Zurich`, clavier suisse
4. Mot de passe root + e-mail
5. Réseau : IP statique dans le sous-réseau VMnet8, nom `pve-lab01.lab.local`
6. Installer, redémarrer
7. Accès : `https://<IP_PVE>:8006` (accepter le certificat auto-signé)
![Capture d'écran 2026-10-09 091751](https://hackmd.io/_uploads/r1ENHMUifx.png)


### 4.5 Installer ESXi

1. Démarrer `ESX-LAB01` → **Enter**
2. Accepter le CLUF (**F11**), choisir le disque, clavier suisse
3. Définir le mot de passe root
4. Après redémarrage : **F2 → Configure Management Network** (IP statique, DNS, hostname)
5. Accès : `https://<IP_ESXi>/ui`
6. Licence : mode évaluation ou licence gratuite selon l'offre Broadcom du moment (**à vérifier à la date du projet**)

![Capture d'écran 2026-10-09 091800](https://hackmd.io/_uploads/By54Sz8jMg.png)


---

## 5. Tests identiques sur chaque hyperviseur

### 5.1 Créer `VM-TEST-01`

| Paramètre | Valeur (identique partout) |
|-----------|----------------------------|
| OS | Ubuntu Server LTS |
| vCPU | 2 |
| RAM | 2 Go |
| Disque | 20 Go |

- **Proxmox** : téléverser l'ISO dans *local → ISO Images*, puis **Create VM** (QEMU Guest Agent coché, disque VirtIO SCSI, réseau VirtIO).
- **ESXi** : téléverser l'ISO dans le datastore, puis **Create / Register VM** (Linux, Ubuntu).

Outils invités dans la VM :

```bash
# Proxmox
sudo apt update && sudo apt install -y qemu-guest-agent
sudo systemctl enable --now qemu-guest-agent

# ESXi
sudo apt update && sudo apt install -y open-vm-tools
sudo systemctl enable --now open-vm-tools
```


### 5.2 Service de démonstration

```bash
sudo apt install -y nginx
sudo systemctl enable --now nginx
curl -I http://localhost
```

Tester depuis l'hôte : `http://<IP_VM>`.

### 5.3 Snapshot et restauration

1. `echo "avant snapshot" | sudo tee /var/www/html/test.txt`
2. Créer le snapshot `avant-test`
3. `sudo rm /var/www/html/test.txt`
4. Restaurer le snapshot
5. Vérifier que le fichier est revenu


![Capture d'écran 2026-10-09 092958](https://hackmd.io/_uploads/rJjWuMLsMx.png)
.

### 5.4 Mesures de performance

```bash
sudo apt install -y sysbench fio iperf3 htop
```

**CPU**
```bash
sysbench cpu --cpu-max-prime=20000 --threads=2 run
```

**Mémoire**
```bash
sysbench memory --memory-block-size=1M --memory-total-size=10G run
```

**Disque**
```bash
fio --name=seqwrite --rw=write --bs=1M --size=1G --direct=1 \
    --runtime=60 --time_based --group_reporting

fio --name=randread --rw=randread --bs=4k --size=1G --direct=1 \
    --runtime=60 --time_based --group_reporting
```

**Réseau** : serveur sur l'hôte `iperf3 -s`, client dans la VM :
```bash
iperf3 -c <IP_HOTE> -t 30
```

**Temps de démarrage** : du clic sur *Start* jusqu'à la page Nginx accessible.

---

## 6. Résultats et différences notables

### 6.1 Analyse documentaire (comparaison complète)

| Critère | Proxmox VE | VMware ESXi / vSphere | Hyper-V | VirtualBox |
|---------|-----------|-----------------------|---------|-----------|
| Type | Type 1 (KVM) | Type 1 | Type 1 | Type 2 |
| Licence | AGPL, open source | Commerciale (Broadcom) | Incluse avec Windows Server | GPL (Extension Pack sous licence propre) |
| Coût | Gratuit, support payant optionnel | Abonnement | Licences Windows Server | Gratuit |
| Administration | Web, CLI, API | Web, vCenter, PowerCLI | Hyper-V Manager, WAC, PowerShell | Interface graphique, VBoxManage |
| HA / cluster | Oui, natif | Oui (vSphere HA, vMotion) | Oui (cluster de basculement) | Non |
| Public cible | PME, labos, intégrateurs | Grandes entreprises | Environnements Windows | Poste de travail, tests |


---

## 7. Tableau comparatif final

Notes de 0 à 5, avec une phrase de justification par note (mesure, capture ou source).

| Critère | Poids | Proxmox VE | VMware | 
|---------|-------|-----------|------|
| Coût | 20 % | 5|3 | 
| Licences | 10 % |4 |3 | 
| Facilité d'administration | 20 % |3 |4 | 
| Performances | 20 % |4 |4 | 
| Écosystème | 15 % |3 |4 | 
| Fonctions entreprise | 15 % |4 |5 | 
| **Score pondéré (/5)** | 100 % |3.85/5 |3.85/5 |

`Score = Σ (note × poids)`

---