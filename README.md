# Épreuve Technicien IT — déploiement et interconnexion de postes de travail

Rendu complet de l'épreuve pratique du module IT Essentials, réalisée en une journée (08h30 – 16h30) dans le cadre du Bachelor en informatique du **Geneva Institute of Technology**.

**Auteur :** Paul Giocanti · **Date :** 11.09.2026

---

## Le mandat

Intervenir comme technicien IT sur trois volets :

1. **Concevoir trois configurations matérielles** chiffrées pour trois profils clients et trois budgets différents.
2. **Déployer deux postes de travail** — un Windows 10 Pro et un Ubuntu — en partitionnement GPT/UEFI, puis les configurer et les optimiser.
3. **Interconnecter les deux postes** sur un même segment réseau et mettre en place un partage de fichiers croisé entre Windows et Linux.

## Les livrables

| Document | Contenu |
|---|---|
| [CDC1 — Configurations matérielles](CDC1-configurations-hardware.md) | 3 configurations complètes (gamer, administration, étudiant), vérification de compatibilité composant par composant, calcul de la consommation et dimensionnement de l'alimentation, justification de chaque arbitrage |
| [CDC2 — Déploiement des systèmes](CDC2-deploiement-os.md) | Installation de Windows 10 Pro et d'Ubuntu en GPT/UEFI, configuration réseau, optimisation, durcissement, fiches de poste |
| [CDC3 — Interconnexion et partage](CDC3-interconnexion-reseau.md) | Schéma réseau, tests de connectivité bidirectionnels, partage SMB/Samba croisé, rapport de diagnostic de pannes, fiche technicien |

## L'environnement

Deux machines virtuelles VMware Workstation placées sur le même réseau virtuel (VMnet8, NAT) :

| | PC01-PG | pc02-pg |
|---|---|---|
| Système | Windows 10 Pro 22H2 | Ubuntu 26.04.1 LTS |
| Adresse IP | 192.168.190.135 /24 | 192.168.190.134 /24 |
| Partitionnement | GPT / UEFI | GPT / UEFI |
| Partage | `\\PC01-PG\Partage_PG` | `//pc02-pg/partage_pg` (Samba) |
| Pare-feu | Actif sur les 3 profils | ufw actif, entrant refusé par défaut |

## Compétences mises en œuvre

**Matériel** — architecture PC, compatibilité socket / chipset / mémoire, calcul de consommation et dimensionnement d'alimentation, chiffrage et arbitrage sous contrainte de budget.

**Systèmes** — installation de Windows 10 Pro et d'Ubuntu, partitionnement GPT/UEFI, gestion des comptes et des privilèges, PowerShell, Bash, optimisation et nettoyage, points de restauration.

**Réseau** — adressage IPv4 et sous-réseaux, DHCP, modes réseau NAT et Pont, diagnostic ICMP, pare-feu Windows Defender et ufw, partage de fichiers SMB / Samba entre Windows et Linux.

**Méthode** — diagnostic du plus simple au plus complexe, résolution de pannes documentée, écarts au cahier des charges assumés et justifiés, documentation technique avec captures horodatées.

## Trois problèmes rencontrés, et comment ils ont été résolus

Ce qui suit n'a pas fonctionné du premier coup — et c'est la partie la plus instructive du travail.

**Le disque invisible à l'installation de Windows.** L'installateur affichait « Aucun lecteur trouvé ». Le disque virtuel avait été créé sur un contrôleur NVMe, pour lequel l'image de Windows 10 ne contient pas de pilote. Disque recréé en SATA, dont le pilote est natif : détection immédiate.

**Le ping qui ne passe que dans un sens.** Windows joignait Ubuntu, mais l'inverse restait sans réponse. Le pare-feu Windows rejette par défaut les requêtes d'écho ICMPv4 entrantes. La règle intégrée correspondante a été activée — sans désactiver le pare-feu, qui est resté actif sur les trois profils.

**Les identifiants refusés sur le partage Samba.** Windows préfixait automatiquement le nom d'utilisateur avec son propre nom de machine. Le compte a été saisi sous la forme `pc02-pg\lami`, et le mot de passe Samba redéfini sans caractère spécial pour écarter les différences de disposition clavier.

---

## Point d'attention transmis au client

**Windows 10 a atteint sa fin de support le 14 octobre 2025.** Le poste ne reçoit plus de correctifs de sécurité : migration vers Windows 11 sur matériel compatible TPM 2.0, ou souscription au programme ESU, à planifier avant toute mise en production.

---

## Autres rendus

- [Module IT Essentials — les 7 travaux pratiques](https://github.com/L4ami/it-essentials-module1)
