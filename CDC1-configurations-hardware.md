# CDC1 — Configurations matérielles

**Auteur :** Paul Giocanti · **Date :** 11.09.2026
**Module :** IT Essentials — Geneva Institute of Technology

[← Retour au sommaire](README.md)

---

## Méthode de travail

Pour chacun des trois profils, la démarche suivie est la même :

1. **Choix du couple processeur / carte mère en premier** : c'est lui qui verrouille le socket et le type de mémoire, donc toute la suite de la configuration.
2. **Mémoire et stockage** dimensionnés selon l'usage réel du client, pas selon le maximum possible.
3. **Carte graphique** uniquement si l'usage la justifie.
4. **Alimentation** dimensionnée sur la consommation réelle additionnée, majorée de 30 % de marge.
5. **Boîtier** vérifié pour le format de carte mère et la longueur de la carte graphique.

**Source des prix.** Tous les prix ont été relevés le 11.09.2026 sur **toppreise.ch**, comparateur de prix suisse, en retenant l'offre la plus basse frais de port inclus.

> PCPartPicker, souvent utilisé pour ce type d'exercice, ne couvre pas la Suisse et affiche des prix en euros. Pour un devis destiné à un client suisse en francs, toppreise.ch est la référence pertinente.

---

## Profil 1 — PC Gamer · Budget CHF 1'500.–

**Besoin client :** jeux AAA récents en 1440p à 60+ FPS. Carte graphique dédiée obligatoire, refroidissement efficace, boîtier avec bon flux d'air, alimentation certifiée 80 PLUS Bronze au minimum.

| Catégorie | Modèle retenu | Caractéristiques clés | Prix CHF |
|---|---|---|---|
| Processeur | AMD Ryzen 5 7600 Boxed | AM5, 6 cœurs / 12 threads, TDP 65 W (PPT 88 W) | 156.10 |
| Ventirad | Thermalright Peerless Assassin 120 SE | Double tour, ~220 W dissipés, très silencieux | 60.90 |
| Carte mère | MSI B650 GAMING PLUS WIFI | ATX, socket AM5, DDR5, 2× M.2, PCIe x16 | 143.40 |
| Mémoire | Kingston FURY Beast RGB 32 Go (2×16) DDR5-5600 | Dual channel, profil EXPO | 239.00 |
| Carte graphique | ASUS PRIME RTX 5060 Ti 16 Go OC | 16 Go GDDR7, ~180 W, 1× PCIe 8 broches | 549.99 |
| Stockage | Samsung 990 EVO Plus 1 To NVMe M.2 | PCIe, lecture ~7'000 Mo/s | 156.91 |
| Alimentation | be quiet! Pure Power 13 M 650 W | **80 PLUS Gold**, semi-modulaire | 81.95 |
| Boîtier | Fractal Design Pop Air | Façade mesh, ATX, GPU jusqu'à 405 mm | 65.70 |
| | | **TOTAL** | **1'453.95** |

**Respect du budget :** 1'453.95 pour 1'500.– → **−3,1 %**, dans la tolérance de ±10 %.

### Vérification de compatibilité

| Point contrôlé | Vérification | OK |
|---|---|---|
| Socket | Ryzen 5 7600 = AM5 · MSI B650 = AM5 | ✔ |
| Type de mémoire | B650 = DDR5 exclusivement · kit choisi = DDR5 | ✔ |
| Fréquence mémoire | DDR5-5600 supportée par AM5 · profil EXPO à activer dans le BIOS, sinon la mémoire tourne à 4800 MHz | ✔ |
| Format | Carte mère ATX · boîtier Pop Air compatible ATX | ✔ |
| Longueur GPU | RTX 5060 Ti ≈ 250 mm · boîtier accepte 405 mm | ✔ |
| Connecteurs d'alimentation | GPU = 1× PCIe 8 broches · l'alimentation en fournit 2 | ✔ |
| Ventirad | 220 W dissipés pour un processeur à 88 W de PPT : large marge | ✔ |
| Stockage | Slot M.2 en PCIe (NVMe) · SSD = NVMe | ✔ |

### Dimensionnement de l'alimentation

| Poste | Consommation estimée |
|---|---|
| Processeur Ryzen 5 7600 (PPT maximum) | 88 W |
| Carte graphique RTX 5060 Ti 16 Go | 180 W |
| Carte mère + mémoire + SSD | 45 W |
| Ventilateurs et périphériques | 15 W |
| **Total en charge** | **≈ 330 W** |
| Marge de sécurité +30 % | ≈ 430 W |
| **Alimentation retenue** | **650 W** → charge d'environ 50 %, zone de meilleur rendement |

### Justification des choix

- **RTX 5060 Ti 16 Go :** c'est le composant qui détermine directement l'objectif du client. En 1440p avec textures élevées, 8 Go de mémoire vidéo deviennent le facteur limitant sur les jeux récents ; les 16 Go écartent ce goulot d'étranglement. Ce poste représente 38 % du budget, ce qui est cohérent pour une machine de jeu.
- **Ryzen 5 7600 :** 6 cœurs suffisent largement en jeu, et son TDP de 65 W permet de concentrer le budget sur la carte graphique plutôt que sur le refroidissement. La plateforme AM5 permettra de changer de processeur plus tard sans changer de carte mère.
- **Alimentation 80 PLUS Gold :** le cahier des charges impose du Bronze au minimum. Le passage en Gold coûte ici une vingtaine de francs et améliore rendement et stabilité — un arbitrage rentable sur la durée de vie de la machine.
- **Boîtier à façade mesh :** le client demande explicitement un bon flux d'air. Une façade pleine étoufferait la carte graphique et provoquerait du throttling thermique, donc une perte de FPS.
- **Arbitrage assumé :** SSD de 1 To plutôt que 2 To. Passer à 2 To ferait sortir la configuration du budget pour un gain non demandé. Un second SSD pourra être ajouté dans le slot M.2 libre.

---

## Profil 2 — PC Administration (bureautique) · Budget CHF 600.–

**Besoin client :** PME, poste de bureautique (suite Office, ERP web, multi-fenêtrage). Pas de jeu. Fiabilité et silence prioritaires, format compact accepté.

| Catégorie | Modèle retenu | Caractéristiques clés | Prix CHF |
|---|---|---|---|
| Processeur | Intel Core i5-12400 Boxed | LGA1700, 6 cœurs / 12 threads, **circuit graphique intégré UHD 730**, ventirad inclus | 164.85 |
| Carte mère | MSI PRO H610M-G DDR4 | Micro-ATX, LGA1700, DDR4, M.2, sorties vidéo intégrées | 45.15 |
| Mémoire | Kingston FURY Beast 16 Go (2×8) DDR4-3200 | Dual channel, 2 slots restants libres | 139.90 |
| Stockage | Samsung 990 EVO Plus 1 To NVMe M.2 | Aucune pièce mécanique, silencieux | 156.91 |
| Alimentation | be quiet! System Power 11 450 W | 80 PLUS Bronze, ventilateur peu sollicité | 47.55 |
| Boîtier | Fractal Design Pop Air | Accepte le micro-ATX, ventilation généreuse donc ventilateurs à bas régime | 65.70 |
| | | **TOTAL** | **620.06** |

**Respect du budget :** 620.06 pour 600.– → **+3,3 %**, dans la tolérance de ±10 %. Le dépassement est assumé et porte sur le SSD : un modèle d'entrée de gamme ferait économiser une trentaine de francs mais dégraderait la fiabilité, premier critère du client.

### Vérification de compatibilité

| Point contrôlé | Vérification | OK |
|---|---|---|
| Socket | i5-12400 = LGA1700 · H610M = LGA1700 | ✔ |
| Type de mémoire | Carte mère en version **DDR4** · kit DDR4-3200 | ✔ |
| Affichage | Sans carte graphique dédiée, le processeur doit disposer d'un circuit graphique intégré : le i5-12400 en a un. **Piège classique : la référence 12400F n'en a pas** et l'écran resterait noir. | ✔ |
| Format | Carte mère micro-ATX · boîtier compatible ATX / mATX / ITX | ✔ |
| Refroidissement | Ventirad Intel fourni, suffisant pour 65 W de TDP en usage bureautique | ✔ |
| Évolutivité | 2 slots DDR4 libres → extension possible à 32 Go | ✔ |

### Dimensionnement de l'alimentation

| Poste | Consommation estimée |
|---|---|
| Processeur i5-12400 (turbo maximum) | 117 W |
| Carte mère + mémoire + SSD | 40 W |
| Périphériques USB | 10 W |
| **Total en charge** | **≈ 170 W** |
| Marge de sécurité +30 % | ≈ 220 W |
| **Alimentation retenue** | **450 W** → charge d'environ 38 %, ventilateur peu sollicité donc très silencieux |

### Justification des choix

- **Processeur avec circuit graphique intégré :** c'est la contrainte structurante de ce profil. Le client n'a pas besoin de carte graphique dédiée, mais il faut impérativement un processeur capable d'afficher.
- **Carte mère à 45.– :** sur un poste bureautique, une carte mère haut de gamme n'apporte rien. Le chipset H610 fournit tout le nécessaire, et l'économie est réinvestie dans le SSD, qui a un impact direct sur le ressenti utilisateur.
- **SSD NVMe plutôt que disque dur :** c'est le composant qui change le plus la perception de rapidité, et l'absence de pièce mécanique réduit à la fois le bruit et le taux de panne — les deux priorités du client.
- **DDR4 assumée :** à ce budget et pour cet usage, la DDR5 n'apporterait aucun gain perceptible et coûterait plus cher en carte mère comme en mémoire.
- **Aucun composant superflu :** ni RGB, ni overclocking, ni carte d'extension. Moins de composants signifie moins de pannes potentielles.

---

## Profil 3 — PC Étudiant (informatique) · Budget CHF 900.–

**Besoin client :** étudiant en école d'informatique — virtualisation de plusieurs machines virtuelles, développement, bureautique, jeu occasionnel. Minimum 16 Go de mémoire extensible, SSD NVMe recommandé, processeur multi-cœurs adapté à la virtualisation.

| Catégorie | Modèle retenu | Caractéristiques clés | Prix CHF |
|---|---|---|---|
| Processeur | AMD Ryzen 5 7600 Boxed | AM5, 6 cœurs / **12 threads**, virtualisation AMD-V, circuit graphique Radeon intégré | 156.10 |
| Ventirad | Thermalright Peerless Assassin 120 SE | Maintient le silence en charge prolongée (compilation, VM) | 60.90 |
| Carte mère | MSI PRO B650M-P | Micro-ATX, AM5, **4 slots DDR5**, 2× M.2 | 130.30 |
| Mémoire | Kingston FURY Beast RGB 32 Go (2×16) DDR5-5600 | 2 slots libres → extensible à 64 Go | 239.00 |
| Stockage | Samsung 990 EVO Plus 1 To NVMe M.2 | 2ᵉ slot M.2 libre pour un disque dédié aux VM | 156.91 |
| Alimentation | be quiet! System Power 11 450 W | 80 PLUS Bronze | 47.55 |
| Boîtier | Fractal Design Pop Air | Accepte le micro-ATX, façade mesh | 65.70 |
| | | **TOTAL** | **856.46** |

**Respect du budget :** 856.46 pour 900.– → **−4,8 %**, dans la tolérance de ±10 %.

### Vérification de compatibilité

| Point contrôlé | Vérification | OK |
|---|---|---|
| Socket | Ryzen 5 7600 = AM5 · B650M-P = AM5 | ✔ |
| Type de mémoire | B650 = DDR5 · kit DDR5-5600 avec profil EXPO | ✔ |
| Mémoire extensible | 4 slots au total, 2 occupés → conforme à l'exigence « extensible » | ✔ |
| Affichage | Le Ryzen 5 7600 intègre un circuit graphique Radeon : aucune carte dédiée nécessaire | ✔ |
| Virtualisation | AMD-V / SVM présent, **à activer dans le BIOS** (désactivé par défaut sur la plupart des cartes mères) | ✔ |
| Format | Carte mère micro-ATX · boîtier compatible | ✔ |
| Stockage | 2 slots M.2 PCIe, 1 seul occupé | ✔ |

### Dimensionnement de l'alimentation

| Poste | Consommation estimée |
|---|---|
| Processeur Ryzen 5 7600 (PPT maximum) | 88 W |
| Carte mère + 32 Go de mémoire + SSD | 45 W |
| Ventilateurs | 10 W |
| **Total en charge** | **≈ 145 W** |
| Marge de sécurité +30 % | ≈ 190 W |
| **Alimentation retenue** | **450 W** → charge d'environ 32 %, marge suffisante pour ajouter une carte graphique plus tard |

### Justification des choix

- **32 Go de mémoire plutôt que 16 :** c'est le point critique de ce profil. Avec 16 Go, deux machines virtuelles plus le système hôte et un environnement de développement saturent la mémoire et provoquent du swap, ce qui effondre les performances. 32 Go permettent de faire tourner deux à trois VM confortablement.
- **Ryzen 5 7600 (6 cœurs / 12 threads) :** les 12 threads permettent d'attribuer 2 processeurs virtuels à plusieurs machines sans étouffer l'hôte, et la virtualisation matérielle AMD-V est présente.
- **Pas de carte graphique dédiée — choix assumé.** Le client mentionne un usage « jeu occasionnel », pas du jeu comme usage principal. Le circuit graphique intégré suffit pour du jeu léger en 1080p. Ajouter une carte d'entrée de gamme coûterait 200 à 250 francs pris sur la mémoire, c'est-à-dire sur la virtualisation, qui est le besoin réel. **L'alimentation et le boîtier ont été choisis pour permettre cet ajout plus tard**, sans rien remplacer.
- **Carte mère à 4 slots mémoire :** l'exigence « extensible » n'est réellement satisfaite qu'avec des slots libres. Avec 2 slots occupés sur 4, le client peut monter à 64 Go sans jeter ses barrettes.
- **Deuxième slot M.2 laissé libre :** les images de machines virtuelles occupent rapidement 30 à 60 Go chacune. Un disque dédié pourra être ajouté sans démontage.

---

## Synthèse

| Profil | Budget | Total relevé | Écart | Compatibilité | Alimentation vs consommation |
|---|---|---|---|---|---|
| PC Gamer | 1'500.– | **1'453.95** | −3,1 % | ✔ | 650 W / ≈ 330 W |
| PC Administration | 600.– | **620.06** | +3,3 % | ✔ | 450 W / ≈ 170 W |
| PC Étudiant | 900.– | **856.46** | −4,8 % | ✔ | 450 W / ≈ 145 W |

Les trois configurations respectent leur budget dans la tolérance de ±10 %, sont compatibles composant par composant, et disposent d'une alimentation dimensionnée avec au moins 30 % de marge sur la consommation réelle.

---

[← Retour au sommaire](README.md) · [CDC2 — Déploiement des systèmes →](CDC2-deploiement-os.md)
