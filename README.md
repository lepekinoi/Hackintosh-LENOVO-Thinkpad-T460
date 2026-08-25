# 🍎 Hackintosh Lenovo ThinkPad T460 — macOS

<div align="center">

![Lenovo ThinkPad T460](https://img.shields.io/badge/Lenovo-ThinkPad%20T460-E2231A?style=for-the-badge&logo=lenovo)
![macOS](https://img.shields.io/badge/macOS-Compatible-000000?style=for-the-badge&logo=apple)
![OpenCore](https://img.shields.io/badge/OpenCore-Bootloader-4285F4?style=for-the-badge)
![Intel](https://img.shields.io/badge/Intel-Core%20i5--6300U-0071C5?style=for-the-badge&logo=intel)

[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/lepekinoi/Hackintosh-LENOVO-Thinkpad-T460?style=flat-square)](https://github.com/lepekinoi/Hackintosh-LENOVO-Thinkpad-T460)
[![Last Commit](https://img.shields.io/github/last-commit/lepekinoi/Hackintosh-LENOVO-Thinkpad-T460?style=flat-square)](https://github.com/lepekinoi/Hackintosh-LENOVO-Thinkpad-T460/commits)

**Configuration EFI complète et documentée pour faire tourner macOS sur un Lenovo ThinkPad T460.**

[📖 Guide complet](#-guide-complet) • [⚙️ Spécifications](#️-spécifications-hardware) • [✨ État du projet](#-état-du-projet) • [🚀 Installation](#-installation-rapide)

</div>

---

## 📸 Aperçu Machine

<div align="center">

| Composant | Détail |
|-----------|--------|
| **💻 Modèle** | Lenovo ThinkPad T460 (Type 20FN/20FM) |
| **🔧 CPU** | Intel Core i5-6300U Skylake-U (2C/4T, 15W) |
| **🎮 GPU** | Intel HD Graphics 520 |
| **🧠 RAM** | DDR3L 1600 MHz (2×SO-DIMM, dual channel) |
| **💾 Stockage** | SSD SATA/NVMe (selon configuration) |
| **📡 WiFi/BT** | Broadcom BCM4360 802.11ac + Bluetooth ✅ |
| **🔊 Audio** | Realtek ALC (100 Series/C230 HD Audio, AppleALC layout-id 28) ✅ |
| **🖥️ Écran** | 14" HD 1366×768 / FHD 1920×1080 IPS ✅ |
| **🖱️ Trackpad/TrackPoint** | Synaptics RMI (ClickPad + TrackPoint) ✅ |
| **🌐 Ethernet** | Intel I219-V |

</div>

---

## 🎯 État du Projet

<div align="center">

### ✅ Fonctionnel

| Fonctionnalité | État | Notes |
|:---:|:---:|:---|
| 🚀 **Boot & Installation** | ✅ | OpenCore stable, installation complète |
| 🎬 **Accélération GPU** | ✅ | Intel HD 520, QuickSync, encodage/décodage vidéo |
| 🔊 **Audio** | ✅ | AppleALC, layout-id 28, prise jack détectée |
| 📡 **WiFi** | ✅ | Broadcom BCM4360 802.11ac natif (AirportBrcmFixup) |
| 🔵 **Bluetooth** | ✅ | BrcmPatchRAM3 + BrcmFirmwareData + BrcmBluetoothInjector |
| 🖱️ **Trackpad & TrackPoint** | ✅ | VoodooRMI + VoodooInput, multitouch complet |
| ⌨️ **Clavier** | ✅ | VoodooPS2Controller |
| 🌐 **Ethernet** | ✅ | Intel I219-V via IntelMausi |
| 🔋 **Batterie** | ✅ | SMCBatteryManager, pourcentage & cycles détectés |
| 💤 **Veille/Réveil** | ✅ | Stable, HibernationFixup + RTCMemoryFixup |
| ⚙️ **Gestion CPU** | ✅ | CPUFriend + SMCProcessor pour le power management |
| 📱 **USB** | ✅ | SSDT-XHCI, cartographie des ports |

### ⚠️ Limité

| Fonctionnalité | État | Raison |
|:---:|:---:|:---|
| 🤝 **Continuité Apple** | ⚠️ | Dépend de la carte WiFi/BT Broadcom installée et du SMBIOS choisi |
| 🎬 **DRM 4K** | ❌ | Netflix/Apple TV+ en 1080p navigateur seulement |

### 🚫 Impossible

| Fonctionnalité | État | Raison |
|:---:|:---:|:---|
| 🆔 **Touch ID / Apple Pay** | ❌ | Pas de puce T2 |
| 🔒 **FileVault complet** | ❌ | Incompatible avec SIP partiellement désactivé (AppleALC) |

</div>

---

## ⚙️ Spécifications Hardware

### 📊 Tableau Détaillé

```
┌─────────────────────────────────────────────────────────┐
│      🍎 Lenovo ThinkPad T460 Hackintosh                 │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  🔧 Processeur & Chipset                              │
│  ├─ CPU: Intel Core i5-6300U (Skylake-U)              │
│  ├─ Cores/Threads: 2C / 4T @ 2.40-3.00 GHz            │
│  ├─ TDP: 15 W (ultrabook)                             │
│  ├─ CPUID: 406E3 (Skylake)                            │
│  └─ Chipset: Intel Sunrise Point-LP PCH (100 Series)  │
│                                                         │
│  🎮 Graphique                                          │
│  ├─ iGPU: Intel HD Graphics 520                       │
│  ├─ ig-platform-id: 0x19160000                        │
│  ├─ Sorties: eDP (écran interne) + HDMI + Mini DP     │
│  └─ Encodage: H.264, VP9 partiel natif (QuickSync)   │
│                                                         │
│  💾 Mémoire & Stockage                               │
│  ├─ RAM: DDR3L 1600 MHz (2×SO-DIMM dual channel)     │
│  └─ Stockage: SATA SSD/HDD ou NVMe (M.2, selon modèle)│
│                                                         │
│  📡 Connectivité                                       │
│  ├─ WiFi/BT: Broadcom BCM4360 802.11ac               │
│  ├─ Ethernet: Intel I219-V (IntelMausi)              │
│  └─ Cartes: USB 3.0, HDMI, Mini DisplayPort, SD      │
│                                                         │
│  🔊 Audio & Affichage                                 │
│  ├─ Codec: Realtek (100 Series/C230 HD Audio)        │
│  │   └─ layout-id: 28 (AppleALC)                     │
│  ├─ Écran: 14" HD 1366×768 ou FHD 1920×1080 IPS      │
│  └─ Webcam: HD (USB interne, en option)              │
│                                                         │
│  🖱️ Entrées                                           │
│  ├─ TrackPoint + ClickPad: Synaptics RMI (VoodooRMI) │
│  └─ Clavier: VoodooPS2Controller                     │
│                                                         │
│  🔋 Alimentation                                      │
│  ├─ Batterie: Li-ion interne + externe (PowerBridge) │
│  └─ Gestion: SMCBatteryManager, % persistant          │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 🚀 Installation Rapide

### ⏱️ Durée Estimée : 2-3 heures

#### **Prérequis**
- ✅ Clé USB 16 GB (ou plus)
- ✅ Accès à un Mac ou une VM macOS
- ✅ Outils : **OpenCore Legacy Patcher**, **MacForge**, ou **OCLP**
- ✅ Fichiers du dépôt (config.plist, SSDT, drivers, kexts)

#### **Étape 1 : Préparation**
```bash
# Cloner ou télécharger le repo
git clone https://github.com/lepekinoi/Hackintosh-LENOVO-Thinkpad-T460.git
cd Hackintosh-LENOVO-Thinkpad-T460

# Sauvegarder l'EFI actuel (si présent)
cp -R EFI/ EFI_backup_$(date +%Y%m%d)/
```

#### **Étape 2 : Créer la clé USB d'installation**
```bash
# Télécharger le fichier InstallAssistant de macOS visé
# Via App Store ou OpenCore Legacy Patcher

# Créer le média d'installation
sudo /path/to/InstallAssistant.app/Contents/Resources/startosinstall \
  --volume /Volumes/MyUSB \
  --agreetolicense --nointeraction
```

#### **Étape 3 : Copier l'EFI**
```bash
# Monter la partition EFI de la clé USB
diskutil list
diskutil mount disk0s1  # Adapter le disque/partition

# Copier l'EFI
cp -R EFI/ /Volumes/EFI/
```

#### **Étape 4 : Réglages BIOS (Lenovo)**
Redémarre et presse **Entrée** puis **F1** pour accéder au BIOS Lenovo :

| Réglage | Valeur |
|---------|--------|
| **Security → Secure Boot** | Disabled |
| **Security → Trusted Platform Module** | Disabled/Active (au choix) |
| **Config → Serial ATA (SATA)** | AHCI |
| **Config → Network → Wake on LAN** | Disabled |
| **Startup → UEFI/Legacy Boot** | UEFI Only |
| **Startup → CSM Support** | Disabled |
| **Config → USB → Always On USB** | Disabled (optionnel) |

#### **Étape 5 : Premier Boot OpenCore**
```
1. Redémarre, presse F12 pour le menu boot
2. Sélectionne ta clé USB
3. Choisis « Install macOS » dans OpenCore Picker
4. Suit l'assistant d'installation
5. ≈ 15-20 minutes, plusieurs reboots normaux
```

#### **Étape 6 : Post-Installation**
```bash
# WiFi/Bluetooth: BrcmPatchRAM3 + AirportBrcmFixup déjà chargés via config.plist
# Vérifie System Preferences → Network / Bluetooth

# Trackpad & TrackPoint: VoodooRMI + VoodooInput déjà chargés
# Test : System Preferences → Trackpad → Multitouch devrait fonctionner

# Audio: AppleALC avec layout-id 28
# Vérifie System Preferences → Son → Sortie/Entrée
```

---

## 📂 Structure EFI

```
EFI/
├── 📁 OC/                          ← Configuration OpenCore
│   ├── 📁 ACPI/                   ← Tables ACPI synthétiques
│   │   ├── SSDT-BATC.aml         (Batterie)
│   │   ├── SSDT-BATT.aml         (Batterie)
│   │   ├── SSDT-CPUD.aml         (CPU Power Management)
│   │   ├── SSDT-DTGP.aml         (Helper _DTGP)
│   │   ├── SSDT-HRTF.aml         (High Resolution Timer Fix)
│   │   ├── SSDT-KBRD.aml         (Clavier)
│   │   ├── SSDT-NTFY.aml         (GPU notifications)
│   │   ├── SSDT-PMC.aml          (Power Management Controller)
│   │   ├── SSDT-PMCR.aml         (PMC Region)
│   │   ├── SSDT-PNLF.aml         (Backlight control)
│   │   ├── SSDT-PTWK.aml         (PS/2 Wake fix)
│   │   ├── SSDT-VDEV.aml         (Virtual device)
│   │   ├── SSDT-XDSM.aml         (Helper _DSM)
│   │   └── SSDT-XHCI.aml         (USB controller)
│   │
│   ├── 📁 Drivers/                ← Drivers UEFI
│   │   ├── HfsPlus.efi           (Lecture HFS+ UEFI)
│   │   └── OpenRuntime.efi       (Runtime OpenCore)
│   │
│   ├── 📁 Kexts/                  ← Kernel Extensions
│   │   ├── Lilu.kext             (Framework)
│   │   ├── WhateverGreen.kext     (GPU patching)
│   │   ├── AppleALC.kext         (Audio)
│   │   ├── IntelMausi.kext       (Ethernet Intel I219-V)
│   │   ├── AirportBrcmFixup.kext (WiFi Broadcom)
│   │   ├── BrcmFirmwareData.kext (Firmware Bluetooth Broadcom)
│   │   ├── BrcmPatchRAM3.kext    (Bluetooth Broadcom)
│   │   ├── BrcmBluetoothInjector.kext (Injection Bluetooth)
│   │   ├── VoodooPS2Controller.kext (Clavier/TrackPoint PS2)
│   │   ├── VoodooRMI.kext        (Trackpad/TrackPoint Synaptics RMI)
│   │   ├── VoodooInput.kext      (HID Trackpad)
│   │   ├── VoodooSMBus.kext      (Bus SMBus)
│   │   ├── VirtualSMC.kext       (SMC)
│   │   ├── SMCBatteryManager.kext (Batterie)
│   │   ├── SMCProcessor.kext     (Capteurs CPU)
│   │   ├── CPUFriend.kext        (Power management CPU)
│   │   ├── HibernationFixup.kext (Veille prolongée)
│   │   └── RTCMemoryFixup.kext   (RTC)
│   │
│   ├── 📁 Tools/                  ← Outils EFI
│   │   └── CleanNvram.efi        (Reset NVRAM)
│   │
│   ├── 📁 Resources/              ← Thème & audio du picker OpenCore
│   ├── config.plist               ← Configuration principale ⭐
│   └── OpenCore.efi               ← Bootloader
│
└── 📁 BOOT/
    └── BOOTx64.efi                ← Chargeur UEFI
```

### 🔧 Fichiers Critiques

| Fichier | Rôle | Notes |
|---------|------|-------|
| `config.plist` | Configuration centrale | ⚠️ Personnaliser SMBIOS |
| `SSDT-PNLF.aml` | Luminosité | Backlight control interne |
| `SSDT-XHCI.aml` | USB | Cartographie des ports USB |
| `WhateverGreen.kext` | Framebuffer iGPU | Patchs HD Graphics 520 |
| `VoodooRMI.kext` | Trackpad/TrackPoint | Avec VoodooInput + VoodooPS2Controller |
| `AppleALC.kext` | Audio | layout-id 28 |
| `AirportBrcmFixup.kext` / `BrcmPatchRAM3.kext` | WiFi/Bluetooth | Broadcom BCM4360 |

---

## ⚙️ Configuration Clés

### 🖥️ Framebuffer & iGPU

```xml
<!-- Dans config.plist → DeviceProperties → PciRoot(0x0)/Pci(0x2,0x0) -->

<key>AAPL,ig-platform-id</key>
<data>AAAWGQ==</data>              <!-- 0x19160000 HD Graphics 520 -->

<key>framebuffer-patch-enable</key>
<data>AQAAAA==</data>              <!-- Activer les patchs -->

<key>framebuffer-fbmem</key>
<data>AACQAA==</data>

<key>framebuffer-stolenmem</key>
<data>AAAwAQ==</data>

<key>framebuffer-portcount</key>
<data>AwAAAA==</data>              <!-- 3 ports actifs -->
```

### 🔊 Audio & AppleALC

```xml
<!-- DeviceProperties → PciRoot(0x0)/Pci(0x1F,0x3) -->
<key>layout-id</key>
<data>HAAAAA==</data>              <!-- layout-id 28, HD Audio Controller ALC -->
```

### 📡 WiFi & Bluetooth Broadcom

```
Kexts chargés en ordre:
1. Lilu.kext
2. AirportBrcmFixup.kext   (WiFi natif BCM4360)
3. BrcmFirmwareData.kext   (Firmware Bluetooth)
4. BrcmPatchRAM3.kext      (Injection Bluetooth au démarrage)
5. BrcmBluetoothInjector.kext
```

### 🖱️ Trackpad & TrackPoint I2C/PS2

```
Dépendances chargées en ordre:
1. VoodooPS2Controller.kext  (clavier + TrackPoint PS/2)
2. VoodooRMI.kext            (ClickPad Synaptics RMI)
3. VoodooInput.kext          (HID unifié multitouch)
```

---

## 📋 Dépannage Courant

### 🔴 Problème : Luminosité non fonctionnelle

**Diagnostic :**
```bash
ioreg -l -p IODeviceTree | grep -i backlight
```

**Solution :**
```bash
# 1. Vérifier que SSDT-PNLF.aml est bien chargé dans config.plist → ACPI → Add
# 2. Vérifier WhateverGreen avec -igfxblcl0 ou boot-args backlight si besoin
# 3. Reboot
```

---

### 🔊 Problème : Audio absent / muet

**Causes courantes :**

| Cause | Diagnostic | Remède |
|-------|-----------|--------|
| layout-id incorrect | `ioreg -l \| grep layout-id` | Essayer layout-id 27, 28, 29 ou 66 |
| AppleALC désactivé | Vérifier config.plist → Kernel → Add | Activer AppleALC.kext |
| Ordre de chargement | Lilu doit précéder AppleALC | Vérifier l'ordre dans Kernel → Add |

**Diagnostic complet :**
```bash
# Vérifie que AppleALC charge
kextstat | grep AppleALC

# Teste le son
afplay /System/Library/Sounds/Morse.aiff
```

---

### 📡 Problème : WiFi/Bluetooth absent

**Diagnostic :**
```bash
ifconfig | grep -i wifi
system_profiler SPBluetoothDataType
```

**Solutions :**

| Symptôme | Cause | Remède |
|----------|-------|--------|
| Aucun réseau WiFi | `AirportBrcmFixup.kext` désactivé | Vérifie config.plist (kext `Enabled=true`) |
| Bluetooth non détecté | `BrcmPatchRAM3` mal ordonné | Vérifier l'ordre : Lilu → BrcmPatchRAM3 → BrcmFirmwareData |
| Carte WiFi non supportée | Modèle Broadcom incompatible | Remplacer par une carte BCM94360 series compatible |

---

### 🖱️ Problème : Trackpad / TrackPoint ne répond pas

**Diagnostic :**
```bash
ioreg -l | grep -i VoodooRMI
```

**Causes courantes & remèdes :**

| Cause | Remède |
|-------|--------|
| VoodooRMI mal chargé | Vérifier ordre : VoodooPS2Controller → VoodooRMI → VoodooInput |
| Conflit ApplePS2 natif | Désactiver les kexts Apple PS/2 natifs (déjà fait via config.plist) |

---

### 💤 Problème : Veille / Réveils intempestifs

**Diagnostic :**
```bash
pmset -g log | tail -50 | grep -E "Wake from|DarkWake"
```

**Causes courantes & remèdes :**

| Cause | Remède |
|-------|--------|
| `Wake on LAN` | Désactiver dans BIOS Lenovo |
| HibernationFixup absent | Vérifier qu'il est activé dans config.plist |
| RTCMemoryFixup absent | Nécessaire pour la persistance NVRAM au réveil |

---

## 📚 Guide Complet

Pour une documentation **détaillée et complète**, consulte:

- 📖 **Guide A→Z** — Installation complète pas à pas
- 🔧 **Configuration BIOS Lenovo** — Tous les réglages
- 🛠️ **Post-Installation** — Audio, WiFi/Bluetooth, trackpad fine-tuning
- 🐛 **Dépannage Avancé** — Panneaux noirs, kernel panics...

---

## ✨ Fonctionnalités Testées

<div align="center">

| Catégorie | Fonctionnalité | État | Détail |
|-----------|----------------|------|--------|
| **Système** | Boot OpenCore | ✅ | Splash screen macOS |
| | Installation complète | ✅ | APFS volume, récupération |
| **GPU** | Accélération graphique | ✅ | Intel HD Graphics 520 |
| | QuickSync | ✅ | Encodage/décodage matériel |
| **Audio** | Sortie speakers | ✅ | AppleALC, niveau ajustable |
| | Combo jack 3.5 mm | ✅ | Casque + micro détectés |
| **Trackpad** | Multitouch | ✅ | 2-finger scroll, 3-finger swipe |
| | TrackPoint | ✅ | Curseur + boutons dédiés |
| **WiFi** | Connexion | ✅ | Broadcom BCM4360, natif |
| | Bluetooth | ✅ | Appairage clavier/souris |
| **Ethernet** | Détection | ✅ | RJ45 reconnu (IntelMausi) |
| | Vitesse | ✅ | Gigabit full-duplex |
| **Batterie** | Affichage % | ✅ | Précis via SMCBatteryManager |
| **Veille** | Fermeture capot | ✅ | Mise en veille automatique |
| | Réveil trackpad/clavier | ✅ | Fonctionnel |
| **USB** | Cartographie | ✅ | Ports internes/externes |
| **HDMI/Mini DP** | Vidéo | ✅ | Sortie externe stable |
| **Caméra** | Détection | ✅ | Facetime, Photo Booth (si présente) |

</div>

---

## 🔐 Sécurité & Vie Privée

- ✅ **UEFI SecureBoot** : Désactivé (nécessaire pour OpenCore)
- ⚠️ **System Integrity Protection (SIP)** : Configuration standard (voir `csr-active-config` dans config.plist)
- ✅ **FileVault** : Possible selon configuration SIP
- ✅ **iCloud / iMessage** : Fonctionnels avec un SMBIOS et une ROM valides
- ✅ **Pas de telemetry** : Hackintosh hors écosystème Apple

---

## 📸 Galerie & Ressources

### Ressources externes

| Ressource | Lien |
|-----------|------|
| 🍎 OpenCore | [github.com/acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) |
| 📖 Dortania Guide | [dortania.github.io](https://dortania.github.io) |
| 🌐 VoodooRMI | [github.com/VoodooI2C/VoodooRMI](https://github.com/VoodooI2C/VoodooRMI) |
| 🔧 BrcmPatchRAM | [github.com/acidanthera/BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM) |
| 🖥️ IntelMausi | [github.com/acidanthera/IntelMausi](https://github.com/acidanthera/IntelMausi) |
| 🐦 r/hackintosh | [reddit.com/r/hackintosh](https://reddit.com/r/hackintosh) |

---

## 🚨 AVERTISSEMENTS

> ⚠️ **Cet EFI est spécifique à cette configuration matérielle.** Chaque machine a des variantes (WiFi/BT, ROM Ethernet, écran, etc.) qui **doivent être adaptées**.

> 🔐 **Génère toujours tes propres identifiants SMBIOS** avec GenSMBIOS. Ne réutilise jamais ceux d'un tiers (risque de conflit iCloud).

> 📡 **Compatibilité WiFi/Bluetooth :** Vérifie que ta carte Broadcom (ou son remplacement) est bien supportée nativement avant l'installation.

---

## 🤝 Contribuer

Les pull requests sont bienvenues !

1. 🍴 Forke le repo
2. 📝 Crée une branche (`git checkout -b feature/amelioration`)
3. ✏️ Commit tes changements (`git commit -am 'Ajoute détail'`)
4. 🔀 Push vers la branche (`git push origin feature/amelioration`)
5. 🔔 Ouvre une Pull Request

### Types de contributions

- 🐛 Rapports de bugs & dépannage
- 📚 Améliorations documentation
- 🔧 Optimisations config.plist
- 🎯 Screenshots / GIFs de fonctionnalités
- 💡 Nouvelles configurations testées

---

## 📜 Licence

Ce projet est distribué sous licence **MIT** (voir [LICENSE](LICENSE)).

**Les composants externes conservent leurs licences respectives :**
- OpenCore, OpenRuntime : BSD (Acidanthera)
- Kexts (Lilu, WhateverGreen, AppleALC, VoodooRMI, etc.) : Voir chaque dépôt
- macOS : © Apple Inc.

---

## 👤 Auteur

**Configuration créée et testée par :** [@lepekinoi](https://github.com/lepekinoi)

---

<div align="center">

### 💬 Questions ?

Ouvre une [**GitHub Issue**](https://github.com/lepekinoi/Hackintosh-LENOVO-Thinkpad-T460/issues) ou consulte le [**r/hackintosh subreddit**](https://reddit.com/r/hackintosh).

### ⭐ Aime ce projet ?

N'oublie pas le **star** ! ⭐ Ça aide la communauté à le découvrir.

---

**Fait avec ❤️ pour les fans de macOS sur Intel**

![Visitor Badge](https://visitor-badge.laobi.icu/badge?page_id=lepekinoi.Hackintosh-LENOVO-Thinkpad-T460)

</div>
