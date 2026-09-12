# CDC2 — Déploiement des systèmes d'exploitation

**Auteur :** Paul Giocanti · **Date :** 11.09.2026
**Module :** IT Essentials — Geneva Institute of Technology

[← Retour au sommaire](README.md)

---

## Contexte et conventions

Deux postes de travail ont été déployés sur VMware Workstation : un poste Windows 10 Pro et un poste Ubuntu, tous deux partitionnés en GPT sur firmware UEFI, en français suisse et sur le fuseau Europe/Zurich.

| Élément | PC01-PG | pc02-pg |
|---|---|---|
| Système | Windows 10 Pro 22H2 | Ubuntu 26.04.1 LTS |
| Nom de machine | `PC01-PG` | `pc02-pg` |
| Utilisateur | `tech.pg` — compte **local**, administrateur | `lami` — compte **non-root**, membre du groupe sudo |
| Adresse IP | 192.168.190.135 /24 (DHCP) | 192.168.190.134 /24 (DHCP) |
| Passerelle / DNS | 192.168.190.2 | 192.168.190.2 |
| Langue / clavier | Français (Suisse) — Suisse romand | Français (Suisse) |
| Fuseau horaire | Europe/Zurich | Europe/Zurich |
| Partitionnement | GPT / UEFI | GPT / UEFI |
| Ressources allouées | 4 vCPU · 8 Go RAM · 60 Go | 2 vCPU · 4 Go RAM |

**Écart de nommage documenté.** Le cahier des charges demande le format `PC01_XX`. L'underscore est refusé par Windows dans un nom d'ordinateur (lettres, chiffres et tirets uniquement) comme par Linux dans un nom d'hôte conforme au RFC 1123. Les postes ont donc été nommés `PC01-PG` et `pc02-pg` ; ils sont désignés `PC01_PG` / `PC02_PG` dans les fiches de poste.

---

# Partie A — Windows 10 Pro (PC01-PG)

## A.1 Installation

| # | Étape | Détail |
|---|---|---|
| 1 | Préparation de la VM | Firmware réglé sur **UEFI** avant le premier démarrage — sans ce réglage, Windows partitionne en MBR et l'exigence GPT/UEFI n'est pas remplie |
| 2 | Média d'installation | ISO Windows 10 Pro 22H2 monté sur le lecteur virtuel |
| 3 | Langue et clavier | Français (Suisse), clavier Suisse romand |
| 4 | Édition | Windows 10 **Pro**, installation « Personnalisé » |
| 5 | Partitionnement | Suppression de toutes les partitions existantes, installation sur espace non alloué — Windows crée automatiquement les 4 partitions du schéma GPT (Récupération, EFI, MSR, Principale) |
| 6 | Compte | Compte **local** `tech.pg` avec mot de passe fort, sans compte Microsoft |
| 7 | Confidentialité | Localisation, diagnostics, publicité ciblée et reconnaissance vocale désactivés |

![Firmware UEFI activé dans les réglages de la VM](https://hackmd.io/_uploads/S1vf5wZKMg.png)
*Firmware UEFI activé dans les réglages de la machine virtuelle*

![Langue et clavier Français (Suisse)](https://hackmd.io/_uploads/HkvGqwZtGe.png)
*Langue et clavier Français (Suisse)*

![Édition Windows 10 Pro sélectionnée](https://hackmd.io/_uploads/S1Pz5vZKMe.png)
*Édition Windows 10 Pro sélectionnée*

![Disque de 60 Go en espace non alloué](https://hackmd.io/_uploads/H1vf9vZtGx.png)
*Disque de 60 Go en espace non alloué avant installation*

![Région Suisse](https://hackmd.io/_uploads/rywG9w-tzx.png)
*Région Suisse*

![Clavier Suisse romand](https://hackmd.io/_uploads/BkDM9PZtGg.png)
*Clavier Suisse romand*

![Options de confidentialité désactivées](https://hackmd.io/_uploads/rkPG9D-Yze.png)
*Options de confidentialité désactivées*

### Incident rencontré pendant l'installation

Au premier lancement, l'installateur Windows affichait *« Aucun lecteur trouvé — Sélectionner le pilote à installer »*. Diagnostic mené du plus simple au plus complexe : relance de l'analyse, vérification du média, puis inspection des réglages de la machine virtuelle. Le disque virtuel avait été créé sur un contrôleur **NVMe**, pour lequel l'image de Windows 10 ne contient pas de pilote intégré. Le disque a été recréé sur un contrôleur **SATA**, dont le pilote est natif dans Windows 10 : le disque a alors été détecté immédiatement.

Une seconde tentative avait par ailleurs démarré via « Easy Install » de VMware, qui automatise l'installation et court-circuite les écrans de langue, d'édition et de partitionnement. La machine virtuelle a été recréée en choisissant *« Je vais installer le système d'exploitation ultérieurement »*, afin de garder la main sur chaque étape et de pouvoir la documenter.

## A.2 Vérification du partitionnement

```powershell
Get-Disk
```

| Number | Friendly Name | HealthStatus | OperationalStatus | Total Size | Partition Style |
|---|---|---|---|---|---|
| 0 | VMware Virtual… | Healthy | Online | 60 GB | **GPT** |

L'exigence « partitionnement GPT/UEFI » est vérifiée par le système lui-même, ce qui constitue une preuve plus fiable qu'une capture de l'écran d'installation.

## A.3 Configuration réseau

```powershell
hostname
ipconfig /all
```

| Paramètre | Valeur relevée |
|---|---|
| Nom de l'hôte | PC01-PG |
| Carte | Ethernet0 — Intel(R) 82574L Gigabit Network Connection |
| DHCP activé | Oui |
| Adresse IPv4 | 192.168.190.135 (préféré) |
| Masque de sous-réseau | 255.255.255.0 |
| Passerelle par défaut | 192.168.190.2 |
| Serveur DHCP | 192.168.190.254 |
| Serveur DNS | 192.168.190.2 |

![Get-Date, Get-Disk, hostname et ipconfig sur une capture horodatée](https://hackmd.io/_uploads/H1QR9DZYfx.png)
*`Get-Date`, `Get-Disk`, `hostname` et `ipconfig /all` sur une seule capture horodatée*

## A.4 Mises à jour

Windows Update a été lancé. Le système signale que **l'appareil ne reçoit plus de mises à jour de sécurité** : Windows 10 a atteint sa fin de support le **14 octobre 2025**. Seuls les correctifs intégrés à l'image d'installation 22H2 sont donc présents, et la recherche de mises à jour ne peut aboutir.

**Recommandation transmise au client :** migrer vers Windows 11 sur un matériel compatible TPM 2.0, ou souscrire au programme ESU (Extended Security Updates) pour continuer à recevoir des correctifs. Maintenir un poste Windows 10 non corrigé en production constitue un risque de sécurité.

![Message de fin de support](https://hackmd.io/_uploads/S1sX5vZFzx.png)
*Message de fin de support*

![Historique des mises à jour installées](https://hackmd.io/_uploads/H1jmcv-Kfe.png)
*Historique des mises à jour installées*

## A.5 Optimisation du poste

| Action | Détail réalisé |
|---|---|
| Programmes au démarrage | Revue via le Gestionnaire des tâches. **Microsoft OneDrive désactivé** (impact au démarrage « Haut », service non utilisé sur ce poste). **VMware Tools Core Service conservé** : nécessaire au fonctionnement de la machine virtuelle. **Notifications Sécurité Windows conservées** : élément du dispositif de sécurité, à ne pas désactiver pour gagner quelques secondes de démarrage. |
| Plan d'alimentation | Mode **« Utilisation normale »** — extinction de l'écran après 15 minutes, mise en veille : **Jamais**. Adapté à un poste fixe qui doit rester joignable sur le réseau. |
| Pare-feu Windows | **Actif sur les 3 profils** : Domain = True, Private = True, Public = True |
| Microsoft Defender | `AntivirusEnabled = True`, `RealTimeProtectionEnabled = True` |
| Nettoyage de disque | `cleanmgr` avec l'option « Nettoyer les fichiers système » — **1,58 Go libérés** |
| Point de restauration | Protection système activée sur C:, point « Post-install TP 11.09.2026 » créé |

```powershell
Get-NetFirewallProfile | Select-Object Name, Enabled
Get-MpComputerStatus | Select-Object AntivirusEnabled, RealTimeProtectionEnabled
cleanmgr
Enable-ComputerRestore -Drive "C:\"
Checkpoint-Computer -Description "Post-install TP 11.09.2026" -RestorePointType "MODIFY_SETTINGS"
Get-ComputerRestorePoint
```

![Programmes au démarrage après optimisation](https://hackmd.io/_uploads/r1jXqv-Kfx.png)
*Programmes au démarrage après optimisation*

![Pare-feu, Defender et nettoyage](https://hackmd.io/_uploads/Bk1ccPZFGl.jpg)
*Pare-feu, Defender et nettoyage sur une seule capture*

![1,58 Go à libérer](https://hackmd.io/_uploads/HJ1c9PbFzx.jpg)
*1,58 Go à libérer*

![Point de restauration créé](https://hackmd.io/_uploads/SJk9cPbKGg.jpg)
*Point de restauration créé*

## A.6 Vérifications finales

| Commande | Ce qu'elle prouve | Résultat |
|---|---|---|
| `winver` | Édition et version du système | Windows 10 Pro 22H2 |
| `ipconfig /all` | Adressage complet du poste | 192.168.190.135 /24, passerelle .2, DNS .2 |
| Gestionnaire des tâches → Démarrage | Programmes au démarrage maîtrisés | OneDrive désactivé, VMware et sécurité conservés |
| `Get-Disk` | Schéma de partitionnement | GPT |

![winver](https://hackmd.io/_uploads/H11c9w-tGe.png)

---

# Partie B — Ubuntu (pc02-pg)

## B.0 Création de la machine virtuelle et installation

> **Note sur la provenance de cette machine virtuelle.** La machine Ubuntu n'a pas été recréée le jour de l'épreuve : elle a été réutilisée depuis le TP6 du module, réalisé le 09.09.2026, avec l'accord du formateur. Les captures de cette section datent donc du 09 et du 10.09.2026, ce qui est indiqué sous chacune d'elles. Tout ce qui suit à partir de la section B.1 — configuration, mise à jour, nettoyage des paquets, pare-feu, renommage et vérifications finales — a été refait et recapturé le 11.09.2026.
>
> Rendu complet du TP6 pour référence : https://github.com/L4ami/it-essentials-module1

### Paramètres de création de la machine virtuelle

| Paramètre | Valeur retenue | Pourquoi |
|---|---|---|
| Hyperviseur | VMware Workstation | Même environnement que le poste Windows, ce qui permet de les placer sur le même réseau virtuel |
| Image d'installation | `ubuntu-26.04.1-desktop-amd64.iso` | Version LTS, support long terme |
| Mémoire allouée | 8 Go | 4 Go suffisent pour Ubuntu Desktop, 8 Go donnent de la marge |
| Disque virtuel | 25 Go | Au-dessus du minimum recommandé pour une installation Desktop complète |
| Firmware | **UEFI** | Impose le partitionnement GPT, comme exigé par le cahier des charges |
| Mode réseau à la création | Pont (Bridged) | Choix du TP6, adapté à un test vers le réseau physique |
| **Mode réseau au 11.09.2026** | **NAT (VMnet8)** | **Modifié le jour de l'épreuve** pour placer les deux VM sur le même réseau virtuel — condition indispensable au CDC3 |

### Étapes d'installation

**Étape 1 — Démarrage sur le support d'installation**

![Écran Try or Install Ubuntu](https://hackmd.io/_uploads/H1-LV4yFGg.png)
*Capture du 09.09.2026 — TP6*

**Étape 2 — Choix du type d'installation** (installation « Normale »)

![Type d'installation](https://hackmd.io/_uploads/ByBRNNyFMl.png)
*Capture du 09.09.2026 — TP6*

| Champ | Valeur saisie au TP6 | Modifié le 11.09.2026 |
|---|---|---|
| Nom d'utilisateur (non-root) | `lami` | inchangé |
| Nom de la machine | `lami-VMware-Virtual-Platform` | **renommé en `pc02-pg`** (voir B.1) |
| Langue et clavier | Français (Suisse) | inchangé |
| Fuseau horaire | Europe/Zurich | inchangé |

**Étape 3 — Création du compte utilisateur non-root**

![Création du compte utilisateur](https://hackmd.io/_uploads/BJNZBNyYfx.png)
*Capture du 09.09.2026 — TP6*

> Sous Linux, on ne travaille jamais en **root** (le compte administrateur absolu) au quotidien. Le compte créé est un compte standard membre du groupe `sudo` : les commandes privilégiées passent par `sudo`, ce qui limite les dégâts d'une fausse manipulation et laisse une trace dans les journaux système.

**Étape 4 — Premier démarrage du bureau**

![Premier démarrage du bureau Ubuntu](https://hackmd.io/_uploads/SyrZ84kFzg.png)
*Capture du 09.09.2026 — TP6*

**Étape 5 — Installation des VMware Tools** (`open-vm-tools-desktop`)

![open-vm-tools installé](https://hackmd.io/_uploads/rkGD3dktze.png)
*Capture du 10.09.2026 — TP6*

**Étape 6 — Première mise à jour complète**

![Première mise à jour](https://hackmd.io/_uploads/ByQFh_1FGe.png)
*Capture du 10.09.2026 — TP6*

**Étape 7 — Relevé de l'adresse IP**

```bash
ip a
```

Interface active `ens33`, adresse relevée **192.168.1.109** — une adresse du réseau physique, cohérente avec le mode **Pont** utilisé au TP6. C'est précisément ce mode qui a dû être changé en **NAT** le 11.09.2026 : en mode Pont, la VM Ubuntu prenait une adresse sur le réseau physique tandis que la VM Windows était sur le réseau virtuel VMware — les deux machines ne pouvaient donc pas se voir.

![ip a au TP6](https://hackmd.io/_uploads/SyyfAuyYGx.png)
*Capture du 10.09.2026 — TP6*

---

## B.1 Configuration et vérifications du 11.09.2026

| Paramètre | Valeur |
|---|---|
| Distribution | Ubuntu 26.04.1 LTS |
| Nom de machine | `pc02-pg` |
| Utilisateur | `lami` — compte non-root, membre du groupe sudo |
| Langue et clavier | Français (Suisse) |
| Fuseau horaire | Europe/Zurich |
| Partitionnement | GPT / UEFI |
| Interface réseau | ens33 — 192.168.190.134 /24 (DHCP) |

**Renommage de la machine.** Le poste s'appelait initialement `lami-VMware-Virtual-Platform`, ce qui ne correspondait pas à la convention du mandat. Renommage effectué et propagé dans `/etc/hosts` :

```bash
sudo hostnamectl set-hostname pc02-pg
sudo sed -i 's/lami-VMware-Virtual-Platform/pc02-pg/g' /etc/hosts
```

![hostname pc02-pg](https://hackmd.io/_uploads/S1HiqPWKGx.png)

## B.2 Mise à jour du système

```bash
date && sudo apt update && sudo apt upgrade -y
```

![Système à jour](https://hackmd.io/_uploads/H1Ss5wWtMg.png)
*`date`, `lsb_release -a` et `hostnamectl` après mise à jour*

## B.3 Optimisation

| Action | Détail réalisé |
|---|---|
| Paquets inutiles | `sudo apt autoremove --purge -y` puis `sudo apt clean`. **Aucun paquet orphelin à supprimer** : le système était déjà propre. La vérification est documentée telle quelle — un résultat vide reste un résultat. |
| Services au démarrage | Revue de `systemctl list-unit-files --state=enabled`. Aucun service superflu : la machine n'héberge que les services système standard et, depuis le CDC3, le service `smbd` volontairement activé. |
| Pare-feu | `ufw` activé, politique par défaut **entrant refusé / sortant autorisé**, activation au démarrage confirmée |

```bash
date && sudo apt autoremove --purge -y && sudo apt clean
systemctl list-unit-files --state=enabled
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw enable
sudo ufw status verbose
```

Résultat de `ufw status verbose` :

```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
```

![autoremove](https://hackmd.io/_uploads/HJBsqDZYGl.png)
![Services au démarrage](https://hackmd.io/_uploads/ryBi9v-YMg.png)
![ufw actif](https://hackmd.io/_uploads/BkHjcP-YGx.png)

## B.4 Vérifications finales

| Commande | Ce qu'elle prouve | Résultat |
|---|---|---|
| `ip a` | Interface, adresse IP et masque | ens33 — 192.168.190.134/24 |
| `lsb_release -a` | Distribution et version exactes | Ubuntu 26.04.1 LTS |
| `systemctl list-unit-files --state=enabled` | Services activés au démarrage | Services système standard + smbd |
| `hostnamectl` | Nom de machine, noyau, architecture | pc02-pg |

![ip a](https://hackmd.io/_uploads/SyBo9wbFMx.png)

---

# Fiches de poste

## PC01_PG

| Champ | Valeur |
|---|---|
| Nom du poste | PC01_PG (nom machine : `PC01-PG`) |
| Système d'exploitation | Windows 10 Pro 22H2 |
| Type de machine | VM VMware Workstation — 4 vCPU, 8 Go RAM, 60 Go |
| Partitionnement | GPT (UEFI) — Récupération / EFI / MSR / Principale |
| Utilisateur local | tech.pg — administrateur local |
| Adresse IP / masque | 192.168.190.135 / 255.255.255.0 |
| Passerelle / DNS | 192.168.190.2 / 192.168.190.2 |
| Partage réseau | `\\PC01-PG\Partage_PG` (C:\Partage_PG) |
| Pare-feu / Antivirus | Pare-feu actif sur les 3 profils · Microsoft Defender actif, protection temps réel active |
| Point de restauration | « Post-install TP 11.09.2026 » |
| Date d'installation | 11.09.2026 |
| Technicien responsable | Paul Giocanti |

## PC02_PG

| Champ | Valeur |
|---|---|
| Nom du poste | PC02_PG (hostname Linux : `pc02-pg`) |
| Système d'exploitation | Ubuntu 26.04.1 LTS |
| Type de machine | VM VMware Workstation |
| Partitionnement | GPT (UEFI) |
| Utilisateur | lami — non-root, membre du groupe sudo |
| Adresse IP / masque | 192.168.190.134 / 255.255.255.0 |
| Passerelle / DNS | 192.168.190.2 / 192.168.190.2 |
| Interface | ens33 |
| Partage réseau | `//pc02-pg/partage_pg` (/srv/partage_pg, Samba) |
| Pare-feu | ufw actif — entrant refusé par défaut, Samba autorisé depuis 192.168.190.0/24 |
| Date d'installation | 11.09.2026 |
| Technicien responsable | Paul Giocanti |

---

[← CDC1 — Configurations matérielles](CDC1-configurations-hardware.md) · [CDC3 — Interconnexion et partage →](CDC3-interconnexion-reseau.md)
