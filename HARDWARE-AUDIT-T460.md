# Audit matériel — collecte des relevés (Lenovo ThinkPad T460)

Méthodologie de collecte des informations matérielles nécessaires à la construction/mise à jour d'un EFI OpenCore. Cette procédure est **générique** dans ses outils, mais son contenu est ici calé sur le **Lenovo ThinkPad T460 (Type 20FN/20FM)** — Core i5-6300U Skylake-U, HD Graphics 520, Broadcom BCM4360, Realtek ALC (100 Series/C230), Intel I219-V, Synaptics RMI.

> ℹ️ Cette version remplace le contenu précédent (issu d'un audit Dell Latitude 3500) : Dell Command|Configure n'a pas d'équivalent Lenovo, le trackpad n'est pas I2C/ELAN mais Synaptics RMI (PS/2 + SMBus), et le Wi-Fi n'est pas un CNVi Intel mais un Broadcom PCIe dédié.

Objectif : produire un dossier de relevés qui fige la réalité technique de la machine, pour ne plus travailler par hypothèses — notamment avant de tenter le passage à macOS Sequoia/Sonoma.

---

## A.1 La pile d'outils

**Les trois indispensables :**

| Outil                        | Licence | Ce qu'il apporte                                                                                                                                                     |
| ----------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **HWiNFO64** (portable)      | Gratuit | 🔴 **Le pilier.** Rapport exhaustif : tous les périphériques PCI avec VEN/DEV/SUBSYS, contrôleurs SMBus/I2C, SMBIOS, SPD des barrettes DDR3L, capteurs, stockage. Export TXT/HTML/CSV |
| **acpidump** (paquet ACPICA) | Gratuit | 🔴 Dump des **tables ACPI** (DSDT, SSDT, FACP, DMAR…) sans installer de pilote. L'archive contient aussi `iasl` pour les décompiler                                   |
| **USBToolBox**               | Gratuit | Cartographie des ports USB (§10)                                                                                                                                     |

**Les compléments utiles :**

| Outil                           | Pourquoi                                                                                                                                      |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **AIDA64 Extreme** (essai 30 j) | Rapport le plus lisible du marché, avec un assistant qui génère un HTML complet en un clic. Redondant avec HWiNFO mais plus agréable à relire |
| ~~**RWEverything**~~            | ❌ Son pilote est bloqué sur Windows 11 (liste des pilotes vulnérables Microsoft). Remplacé par `acpidump`                                     |
| **PCI-Z**                       | Identifie les périphériques PCI inconnus par leurs VEN/DEV quand Windows n'a pas le pilote                                                    |
| **USBDeview** (NirSoft)         | Liste tous les périphériques USB jamais branchés, avec leur VID/PID                                                                           |
| **SSDTTime**                    | Génère les SSDT à partir des tables dumpées (§5) — pratique aussi pour repartir sur une base propre avant Sequoia                              |

> **Pourquoi HWiNFO plutôt que CPU-Z ou Speccy** : ces derniers donnent un aperçu, pas un inventaire. HWiNFO descend au niveau des identifiants PCI et des contrôleurs SMBus/I2C — exactement ce dont on a besoin pour choisir les kexts.

> ⚠️ **Lenovo n'a pas d'équivalent à Dell Command|Configure.** Les BIOS ThinkPad (accès via Entrée puis F1) n'exposent pas d'outil CLI d'énumération avancée type `cctk.exe`. Les réglages cachés éventuels ne sont accessibles, le cas échéant, que via des utilitaires tiers non officiels (à éviter) — voir §A.5 plus bas pour ce que ça implique concrètement.

## A.2 Procédure de collecte

Crée un dossier `C:\Hack\Rapport\` et travaille dedans.

**1 — HWiNFO64**
> ⚠️ **Piège au lancement.** Sur l'écran d'accueil, **décoche à la fois « Sensors-only » et « Summary-only »**. Si l'une des deux reste cochée, l'inventaire matériel complet n'est pas construit et **le bouton de rapport n'existe pas**. C'est la cause n°1 de « je ne trouve pas l'option ».

1. Clic droit sur `HWiNFO64.exe` → **Exécuter en tant qu'administrateur**
2. Écran d'accueil : **toutes les cases décochées** → `Start`
3. Le scan tourne, puis une fenêtre **« System Summary »** s'ouvre par-dessus. **Ferme-la** — c'est elle qui masque la fenêtre principale
4. Tu arrives sur la fenêtre principale : arborescence des composants à gauche, détails à droite
5. Clique sur le bouton **« Save Report »** dans la barre d'outils en haut *(ce n'est pas un menu déroulant)*
6. L'assistant demande successivement :
   - le **format** : MHTML, HTML, CSV, TXT ou CDF
   - le **nom et l'emplacement** du fichier
   - les **catégories à inclure** — développe les branches avec le `+` et coche tout
7. `Finish`

Fais l'opération **deux fois** : une en `.html` (lisible, pour parcourir) et une en `.txt` (cherchable au Ctrl+F). Enregistre sous `Rapport\hwinfo-full.html` et `Rapport\hwinfo-full.txt`.
> Si l'écran d'accueil ne s'affiche pas du tout (il peut avoir été désactivé), ouvre `HWiNFO64.INI` à côté de l'exécutable et vérifie que `SensorsOnly=0` et `SummaryOnly=0`.

**2 — Tables ACPI**
> ⚠️ **RWEverything ne se charge plus sur Windows récent.** Son pilote `RwDrv.sys` figure sur la liste des pilotes vulnérables de Microsoft (utilisé dans des attaques BYOVD). Sur Windows 11, l'intégrité de la mémoire et la liste de blocage sont actives par défaut → erreur « driver cannot be loaded », même en administrateur. Deux alternatives, toutes deux meilleures.

**Méthode A — `acpidump.exe` (recommandée sous Windows)**

Outil officiel du projet ACPICA. **Aucun pilote installé** : il passe par l'API Windows `GetSystemFirmwareTable`.

1. Télécharge `iasl-win-*.zip` depuis `github.com/acpica/acpica/releases`
2. Décompresse — l'archive contient `acpidump.exe` **et** `iasl.exe`
3. Invite de commandes **administrateur** :

```
cd C:\Hack\Rapport\ACPI
acpidump.exe -b
```

Le `-b` produit un fichier binaire par table : `dsdt.dat`, `ssdt1.dat`, `ssdt2.dat`, `facp.dat`, `apic.dat`, `dmar.dat`… Tu peux les renommer en `.aml`, SSDTTime accepte les deux extensions.

**Décompilation du DSDT** — uniquement si tu veux le lire toi-même :

```
iasl.exe -e ssdt*.dat -d dsdt.dat
```
> ⚠️ **N'utilise pas `-da` ni `-dl`.** `-da` sert à désassembler *plusieurs* tables dans un espace de noms unique et se met en défaut si on ne lui passe qu'un fichier ; `-dl` force une syntaxe ASL héritée dont tu n'as pas besoin. La bonne option est **`-d`**, et **`-e`** fournit les SSDT pour résoudre les symboles externes sans les désassembler.
>
> **Si le `.dsl` produit est vide** : vérifie d'abord la taille de `dsdt.dat` avec `dir`. Un fichier vide ou minuscule signifie que `acpidump.exe` a échoué — presque toujours parce qu'il n'a pas été lancé **en administrateur**. Il n'affiche pas d'erreur franche, il écrit simplement des fichiers vides.
>
> **Les avertissements sont normaux.** Un désassemblage ACPI produit toujours des dizaines de remarques. Le seul critère qui compte : le `.dsl` contient-il du code et commence-t-il par `DefinitionBlock` ?
> 💡 **SSDTTime n'a pas besoin du `.dsl`.** Il lit directement les binaires `.aml` / `.dat`. La décompilation ne sert qu'à la lecture humaine — pour chercher `Device (EC`, `Device (GPI0`, `AWAC`, `Device (BAT` à la main. Si tu ne comptes pas éplucher le fichier, saute cette étape.

**Méthode B — depuis le live Linux (la plus simple)**

Tu peux la faire depuis n'importe quelle clé Linux déjà prête :

```
mkdir -p /tmp/acpi && cd /tmp/acpi
cp /sys/firmware/acpi/tables/DSDT .
cp /sys/firmware/acpi/tables/SSDT* .
cp /sys/firmware/acpi/tables/FACP .
cp /sys/firmware/acpi/tables/APIC .
cp /sys/firmware/acpi/tables/DMAR . 2>/dev/null
```

Puis copie le dossier sur une clé de données.

**Méthode C — RWEverything : déconseillée**

Il faudrait désactiver l'intégrité de la mémoire (`Sécurité Windows → Sécurité de l'appareil → Isolation du noyau`) puis la liste de blocage des pilotes vulnérables via le registre, et redémarrer. Tu affaiblirais Windows pour obtenir exactement les mêmes fichiers que les méthodes A et B fournissent sans rien toucher.

**Tables à récupérer dans tous les cas** : `DSDT`, tous les `SSDT`, `FACP`, `APIC`, `DMAR`, `MCFG`.

**3 — Liste des périphériques (PowerShell administrateur)**

```
cd C:\Hack\Rapport

Get-PnpDevice | Select-Object Status,Class,FriendlyName,InstanceId |
  Sort-Object Class | Format-Table -AutoSize |
  Out-File -Width 400 devices-all.txt

# Ciblé sur le trackpad/TrackPoint — Synaptics RMI, pas ELAN I2C
Get-PnpDevice -Class Mouse,HIDClass,System |
  Where-Object { $_.FriendlyName -match "touch|track|SYNA|synaptics|pointing|souris|SMBus" } |
  Select-Object FriendlyName,InstanceId,Status |
  Format-List | Out-File -Width 400 trackpad.txt

msinfo32 /report msinfo.txt
```

**4 — USBToolBox** → suivre §10.1, sauvegarder `UTBMap.kext` dans `Rapport\`

**5 — SSDTTime** → option `D` (Dump DSDT). Si le dump échoue sous Windows, pas grave : les tables récupérées à l'étape 2 font exactement le même travail — SSDTTime sait les lire depuis un dossier, il suffit de le lui glisser.

## A.3 Les informations à extraire du rapport

Coche au fur et à mesure — c'est ça, la « réalité technique » de la machine.

| #  | Information                                               | Où la trouver                                          | Pourquoi                                           |
| --- | ----------------------------------------------------------- | ---------------------------------------------------------- | ------------------------------------------------------- |
| 1  | **Trackpad/TrackPoint : PS/2 ou SMBus (RMI)**             | `trackpad.txt` / HWiNFO → Devices                        | 🔴 Confirme le transport pour VoodooRMI (VoodooPS2 vs VoodooSMBus + VoodooInput) |
| 2  | Nom ACPI du trackpad (ex. `SYNA*`, `TPD0`, `PS2M`)          | idem / DSDT décompilé                                     | Dépannage VoodooRMI                                     |
| 3  | **Contrôleur SMBus Intel** (`8086:9D23` Sunrise Point-LP) | HWiNFO → PCI Bus                                          | Requis par VoodooSMBus si le trackpad passe en RMI/SMBus |
| 4  | iGPU : `VEN_8086&DEV_1916` (HD Graphics 520)               | HWiNFO → Video Adapter                                    | Confirme les valeurs actuelles du `config.plist` ; sert de référence avant le spoof KBL (`DEV_5916`) pour Sequoia |
| 5  | Ethernet : `VEN_8086&DEV_15A2` ou proche (I219-V)          | HWiNFO → Network                                          | Confirme `IntelMausi.kext`, déjà en place                |
| 6  | Contrôleur audio + **codec réel exact**                    | HWiNFO → Audio Devices                                    | Ton `config.plist` fixe `layout-id 28` sans préciser le modèle ALC exact — à vérifier pour confirmer le bon layout AppleALC |
| 7  | Wi-Fi : `VEN_14E4` (Broadcom BCM4360, PCIe dédié)          | HWiNFO → Network / PCI Bus                                | Confirme le montage PCIe natif (pas de CNVi sur ce modèle) — pertinent pour les kexts WiFi Sequoia (§ voir suite) |
| 8  | Bluetooth : identifiant Broadcom USB interne               | HWiNFO → USB Devices                                      | Confirme la chaîne BrcmPatchRAM3/BrcmFirmwareData/BrcmBluetoothInjector |
| 9  | **Emplacement M.2** (SATA/NVMe, 2242/2280 selon modèle T460) | HWiNFO → Drives / Storage Controllers                     | Confirme le contrôleur SATA/AHCI ou NVMe pour d'éventuels quirks |
| 10 | NVMe ou SSD SATA : modèle, firmware                        | HWiNFO → Drives                                            | Vérifie compatibilité native ou besoin de patch NVMe    |
| 11 | **Nom ACPI du contrôleur embarqué** (`EC`, `EC0`, `H_EC`)  | DSDT décompilé                                             | Cohérence avec `SSDT-PTWK`/`SSDT-BATC` déjà présents     |
| 12 | Présence d'un device **`GPI0`** dans le DSDT               | DSDT décompilé                                             | XOSI vs GPI0 (patch éventuel côté ACPI)                 |
| 13 | Présence de **`AWAC`** / `RTC`                              | DSDT décompilé                                             | Détermine si SSDT-AWAC ou SSDT-RTC0 est nécessaire (Skylake-U a en général un RTC classique, à confirmer) |
| 14 | Champs EC de la batterie **>8 bits**                        | DSDT décompilé                                             | Valide (ou non) le besoin d'`ECEnabler` en complément de tes `SSDT-BATC`/`SSDT-BATT`/`SSDT-NTFY` existants |
| 15 | Liste des ports USB + types                                 | USBToolBox                                                  | Cartographie propre avant refonte USBMap (§10)          |
| 16 | **État du CFG Lock**                                        | §A.4                                                        | Valide `AppleXcpmCfgLock` (déjà à `true` dans ton plist) |

Pour les points 11 à 14, il faut décompiler le DSDT :

```
iasl -e ssdt*.dat -d dsdt.dat
```

(iasl est fourni dans la même archive qu'`acpidump` — `github.com/acpica/acpica/releases`)

Puis chercher dans le `.dsl` produit : `Device (EC`, `Device (GPI0`, `AWAC`, `Device (BAT`.

## A.4 Vérifier le CFG Lock

Le BIOS Lenovo T460 n'expose généralement pas l'option directement dans l'interface F1, mais autant savoir où on en est réellement. OpenCorePkg fournit l'outil :

- Copie `Utilities/VerifyMsrE2/VerifyMsrE2.efi` dans `EFI/OC/Tools/` de ta clé
- Ajoute-le dans `Misc → Tools` du config.plist
- Démarre sur la clé, presse **Espace**, lance `VerifyMsrE2`

| Résultat                    | Conséquence                                                      |
| ---------------------------- | ------------------------------------------------------------------ |
| Toutes les lignes en `TRUE`  | CFG Lock **actif** → `AppleXcpmCfgLock = YES` (déjà en place dans ton `config.plist`) |
| Toutes en `FALSE`            | CFG Lock déverrouillé → le quirk devient inutile, mais le laisser actif ne casse rien |
| Mélange                      | Firmware capricieux → laisse le quirk à `YES`                     |

> Note : ton `config.plist` actuel a `AppleXcpmCfgLock: true` — cohérent avec un BIOS Lenovo verrouillé par défaut. Confirme quand même avec `VerifyMsrE2` avant de changer quoi que ce soit à ce quirk.

## A.5 Bonus : options BIOS cachées sur ThinkPad

Contrairement à Dell (qui propose `Command | Configure`), **Lenovo ne fournit pas d'outil CLI officiel équivalent** pour énumérer ou modifier des réglages BIOS non affichés dans l'interface F1.

Sur T460, les réglages BIOS pertinents pour Hackintosh sont accessibles directement dans l'interface F1 standard :

| Menu BIOS Lenovo | Réglage à vérifier |
| ------------------ | --------------------- |
| **Security → Secure Boot**             | Disabled |
| **Security → Trusted Platform Module** | Disabled ou Active (au choix, sans impact bloquant) |
| **Config → Serial ATA (SATA)**         | AHCI |
| **Config → Network → Wake on LAN**     | Disabled |
| **Startup → UEFI/Legacy Boot**         | UEFI Only |
| **Startup → CSM Support**              | Disabled |
| **Config → USB → Always On USB**       | Disabled (optionnel, évite les faux réveils) |

Il n'existe pas d'options DVMT/framebuffer cachées à débloquer côté Lenovo comme cela peut exister sur certains Dell — la mémoire vidéo de l'iGPU Intel HD 520 se règle exclusivement côté `config.plist` via les patchs framebuffer (`WhateverGreen`), ce qui est déjà en place dans ta configuration actuelle.

## A.6 Livrable attendu

```
C:\Hack\Rapport\
├── hwinfo-full.html
├── hwinfo-full.txt
├── devices-all.txt
├── trackpad.txt
├── msinfo.txt
├── UTBMap.kext
└── ACPI\
    ├── DSDT.aml
    ├── DSDT.dsl        (décompilé)
    ├── SSDT-*.aml
    ├── FACP.aml
    ├── APIC.aml
    └── DMAR.aml
```

Avec ce dossier, la construction/mise à jour de l'EFI (notamment le passage à macOS Sequoia) ne repose plus sur aucune supposition — en particulier sur le transport réel du trackpad (RMI/PS2 vs SMBus) et le codec audio exact, deux points qui n'étaient pas encore confirmés dans ton `config.plist` actuel.
