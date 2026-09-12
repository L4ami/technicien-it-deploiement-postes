# CDC3 — Interconnexion réseau et partage de fichiers

**Auteur :** Paul Giocanti · **Date :** 11.09.2026
**Module :** IT Essentials — Geneva Institute of Technology

[← Retour au sommaire](README.md)

---

## 3.0 Contexte et choix techniques

Les deux postes déployés au CDC2 sont des machines virtuelles VMware Workstation hébergées sur le même poste physique. Les deux cartes réseau virtuelles ont été placées sur le **même réseau virtuel VMnet8 (mode NAT)**, condition indispensable pour que les machines se voient : deux VM sur des VMnet différents ne peuvent pas communiquer, même avec un adressage cohérent.

L'adressage retenu est le **DHCP fourni par VMware**, explicitement autorisé par le cahier des charges. Les deux machines obtiennent une adresse dans le même sous-réseau, ce qui satisfait l'exigence de segment commun sans configuration statique inutile.

![Carte réseau de la VM Ubuntu en NAT](https://hackmd.io/_uploads/rJDnFD-FGg.png)
*Carte réseau de la VM Ubuntu en NAT*

![Carte réseau de la VM Windows en NAT](https://hackmd.io/_uploads/BkD3tDbtMx.png)
*Carte réseau de la VM Windows en NAT*

## 3.1 Schéma réseau

```
                  Réseau virtuel VMware VMnet8 (NAT)
                         192.168.190.0/24
                                |
      +-------------------------+-------------------------+
      |                                                   |
+-------------------+                          +---------------------+
|     PC01-PG       |                          |       pc02-pg       |
|  Windows 10 Pro   | <-------- ping --------> | Ubuntu 26.04.1 LTS  |
|  192.168.190.135  | <----- SMB / Samba ----> |   192.168.190.134   |
|  Ethernet0        |                          |   ens33             |
+-------------------+                          +---------------------+
   Partage : Partage_PG                           Partage : partage_pg

              Passerelle commune : 192.168.190.2
```

## 3.2 Configuration réseau des deux postes

| Paramètre | PC01-PG (Windows 10 Pro) | pc02-pg (Ubuntu) |
|---|---|---|
| Interface | Ethernet0 | ens33 |
| Méthode d'attribution | DHCP (serveur VMware 192.168.190.254) | DHCP |
| Adresse IPv4 | **192.168.190.135** | **192.168.190.134** |
| Masque de sous-réseau | 255.255.255.0 (/24) | 255.255.255.0 (/24) |
| Passerelle par défaut | 192.168.190.2 | 192.168.190.2 |
| Serveur DNS | 192.168.190.2 | 192.168.190.2 |
| Réseau virtuel | VMnet8 (NAT) | VMnet8 (NAT) |
| Segment | 192.168.190.0/24 — **identique** | 192.168.190.0/24 — **identique** |

Les deux machines appartiennent bien au même segment : les trois premiers octets sont identiques et le masque /24 place les deux adresses dans le même réseau logique. Aucun routage n'est donc nécessaire entre elles.

![Sortie de ipconfig /all sur PC01-PG](https://hackmd.io/_uploads/BJd2KP-YGg.png)
*Sortie de `ipconfig /all` sur PC01-PG*

![Sortie de ip a sur pc02-pg](https://hackmd.io/_uploads/ByuntPZtfx.png)
*Sortie de `ip a` sur pc02-pg*

## 3.3 Tests de connectivité bidirectionnels

| Test | Commande | Résultat obtenu |
|---|---|---|
| Windows → Ubuntu | `ping 192.168.190.134` | 4 paquets envoyés, 4 reçus, **0 % de perte**, temps < 1 ms, TTL 64 |
| Ubuntu → Windows | `ping -c 4 192.168.190.135` | 4 paquets transmis, 4 reçus, **0 % de perte**, temps moyen 0,967 ms, TTL 128 |

![Ping Windows vers Ubuntu](https://hackmd.io/_uploads/BkO2Kw-tze.png)
![Ping Ubuntu vers Windows](https://hackmd.io/_uploads/HJyAtDWFfl.png)

**Incident rencontré et résolu.** Le premier test Ubuntu → Windows ne recevait aucune réponse alors que le sens inverse fonctionnait. Diagnostic : le pare-feu Windows bloque par défaut les requêtes d'écho ICMPv4 entrantes. La règle intégrée correspondante a été activée, **sans désactiver le pare-feu** :

```powershell
Enable-NetFirewallRule -Name FPS-ICMP4-ERQ-In
```

Le ping est passé immédiatement après. Le choix d'activer une règle ciblée plutôt que de désactiver le pare-feu est délibéré : il préserve le niveau de sécurité du poste.

## 3.4 Partage de fichiers croisé (SMB / Samba)

### Côté Ubuntu — serveur Samba

```bash
sudo apt install samba cifs-utils -y
sudo mkdir -p /srv/partage_pg
sudo chown lami:lami /srv/partage_pg
sudo chmod 770 /srv/partage_pg
```

Bloc ajouté en fin de `/etc/samba/smb.conf` :

```ini
[partage_pg]
   path = /srv/partage_pg
   browseable = yes
   read only = no
   valid users = lami
```

```bash
sudo smbpasswd -a lami          # base de mots de passe Samba, distincte des comptes Linux
sudo systemctl restart smbd
sudo ufw allow from 192.168.190.0/24 to any app Samba
sudo ufw status verbose
```

Règles ufw obtenues — l'accès est ouvert **uniquement** au segment du TP :

| Port | Action | Origine |
|---|---|---|
| 137,138/udp (Samba) | ALLOW IN | 192.168.190.0/24 |
| 139,445/tcp (Samba) | ALLOW IN | 192.168.190.0/24 |

![Samba configuré](https://hackmd.io/_uploads/BykAtvbKzx.png)

### Côté Windows — partage SMB

```powershell
mkdir C:\Partage_PG
New-SmbShare -Name Partage_PG -Path C:\Partage_PG -FullAccess tech.pg
Enable-NetFirewallRule -Name FPS-SMB-In-TCP
```

### Résultat de l'accès croisé

| Sens | Chemin utilisé | Identifiants | Résultat |
|---|---|---|---|
| Windows → Ubuntu | `\\192.168.190.134\partage_pg` | pc02-pg\lami | Partage ouvert, **création d'un dossier `test_depuis_windows`** réussie → accès en écriture confirmé |
| Ubuntu → Windows | `smb://192.168.190.135/Partage_PG` | PC01-PG\tech.pg | Partage ouvert, **création d'un dossier `test_depuis_Ubuntu`** réussie → accès en écriture confirmé |

La vérification croisée finale a consisté à ouvrir `C:\Partage_PG` sur Windows et à y retrouver le dossier créé depuis Ubuntu : la preuve que l'écriture distante aboutit réellement sur le système de fichiers cible, et pas seulement en cache.

![Accès de Windows vers Ubuntu](https://hackmd.io/_uploads/ry1RtwZFze.png)
![Accès d'Ubuntu vers Windows](https://hackmd.io/_uploads/SJkAKvWYGe.png)
![Vérification croisée](https://hackmd.io/_uploads/B1yAKDbKMe.png)

## 3.5 Rapport de diagnostic

| # | Problème rencontré | Cause identifiée | Solution appliquée | Résultat |
|---|---|---|---|---|
| 1 | Ping Ubuntu → Windows sans réponse, alors que Windows → Ubuntu fonctionne | Pare-feu Windows : requêtes d'écho ICMPv4 entrantes bloquées par défaut | Activation de la règle intégrée `FPS-ICMP4-ERQ-In`, pare-feu maintenu actif sur les 3 profils | Ping OK, 0 % de perte |
| 2 | Identifiants refusés à l'ouverture du partage Samba depuis Windows | Windows préfixait automatiquement le nom d'utilisateur avec son propre nom de machine, et le mot de passe Samba comportait des caractères spéciaux saisis différemment selon les dispositions clavier | Saisie du compte sous la forme `pc02-pg\lami` et redéfinition d'un mot de passe Samba sans caractère spécial | Partage accessible |
| 3 | Le partage réseau n'était pas atteignable au premier essai | Profil réseau Windows classé en « DomainAuthenticated », règles de partage de fichiers inactives | Activation de la règle `FPS-SMB-In-TCP` | Partage visible et accessible |

**Méthode de diagnostic appliquée**, du plus simple au plus complexe :

1. Les deux machines sont-elles dans le même sous-réseau ? (`ipconfig /all` et `ip a`)
2. Les cartes réseau des deux VM sont-elles sur le même VMnet ?
3. Le ping vers la passerelle fonctionne-t-il ?
4. Un pare-feu bloque-t-il ? Si oui, ouvrir une règle ciblée — jamais désactiver le pare-feu.
5. Le service de partage tourne-t-il ? (`systemctl status smbd`)

---

# Fiche technicien récapitulative

| | |
|---|---|
| **Intervenant** | Paul Giocanti — Technicien IT |
| **Date d'intervention** | 11.09.2026, 08h30 – 16h30 |
| **Lieu** | Geneva Institute of Technology |
| **Mandat** | Conception de 3 configurations matérielles, déploiement de 2 postes (Windows 10 Pro + Ubuntu), interconnexion et partage de fichiers |
| **Environnement** | VMware Workstation, 2 machines virtuelles sur VMnet8 (NAT) |

## Travaux réalisés

| Bloc | Objet | État | Remarque |
|---|---|---|---|
| CDC1 | 3 configurations chiffrées et vérifiées | Terminé | Budgets respectés : −3,1 % / +3,3 % / −4,8 % |
| CDC2-A | Windows 10 Pro sur PC01-PG | Terminé | GPT/UEFI, compte local, FR-CH, optimisé, point de restauration créé |
| CDC2-B | Ubuntu sur pc02-pg | Terminé | Système à jour, paquets inutiles purgés, ufw actif |
| CDC3 | Interconnexion et partage croisé | Terminé | Ping bidirectionnel 0 % de perte, SMB croisé en écriture |

## Sécurité et bonnes pratiques respectées

- Interventions menées sur machines virtuelles ; pour toute manipulation matérielle : mise hors tension et débranchement préalables.
- Manipulation des composants par les bords, décharge électrostatique avant contact, aucun composant inséré en forçant.
- Comptes utilisateurs : compte local avec mot de passe fort côté Windows, compte non-root membre du groupe sudo côté Ubuntu.
- **Pare-feu maintenu actif sur les deux machines.** Tous les accès réseau nécessaires ont été ouverts par des règles ciblées et limitées au segment 192.168.190.0/24, jamais par désactivation du pare-feu.
- Chaque étape documentée par une capture horodatée.

## Points d'attention transmis au client

- **Fin de support de Windows 10 (14.10.2025).** Le poste ne reçoit plus de correctifs de sécurité. Migration vers Windows 11 sur matériel compatible TPM 2.0, ou souscription au programme ESU, à planifier.
- **Aucune sauvegarde ni tolérance de panne** n'est en place sur ces deux postes. Prévoir une sauvegarde des données du partage avant mise en production.
- Le partage SMB est restreint au segment 192.168.190.0/24. À restreindre davantage si le réseau accueille des postes invités.
- L'adressage est en DHCP. Pour un usage durable en production, réserver les baux ou passer en adressage statique afin que les chemins de partage restent stables.

---

[← CDC2 — Déploiement des systèmes](CDC2-deploiement-os.md) · [Retour au sommaire](README.md)
