# Collecte de fichiers

Cette section sert à récupérer les différents fichiers qui feront démarrer macOS, nous attendons de vous que vous connaissiez bien votre matériel avant de commencer et, espérons-le, que vous ayez déjà fait un Hackintosh, car on rentrera pas profond dans les détails ici

> Quelle est la meilleure façon de savoir si mon matériel est pris en charge?

Voir la [**page des limitations matérielles**](https://opencore.pressynou.ch/introduction/limitations-hardware) pour un meilleur aperçu de ce dont macOS a besoin pour démarrer, le support matériel entre Clover et OpenCore est assez similaire.

> Quels sont les moyens de savoir quel matériel j'ai ?

Voir cette  page avant: [Trouver votre matériel](./find-hardware.md)


##  Pilotes Logiciels

Les pilotes logiciels sont des pilotes utilisés par OpenCore pour l'environnemnt UEFI. Ils sont principalement nécessaires pour démarrer le système, soit en étendant la capacité de correction d'OpenCore, soit en vous montrant différents types de lecteurs dans le sélecteur OpenCore (c'est-à-dire les lecteurs HFS (MacOS Étendu)).

* **Note sur l'emplacement de ces fichiers**: Ces fichiers **DOIVENT** être placés dans `EFI/OC/Drivers/`

### Universal

<details>
<summary>Pilotes nécessaires</summary>

Pour la majorité des systèmes, vous allez avoir besoin de 2 `efi` pour démarrer votre système :

* [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)(<span style="color:red">Nécessaire</span>)
  * Nécessaire pour les disques en MacOS Etendu (ou HFS) (c'est à dire les installeurs de macOS et les partitions de récupérations). **Ne mixais pas plusieurs pilotes HFS!**
  * Pour les CPU Sandy Bridge et plus vieux (ainsi que les CPU Ivy Bridge bas de gamme (i3 et Celerons), voir la section en dessous.
* [OpenRuntime.efi](https://github.com/acidanthera/OpenCorePkg/releases)(<span style="color:red">Nécessaire</span>)
  * Héritier de [AptioMemoryFix.efi](https://github.com/acidanthera/AptioFixPkg), utilisé comme une extention d'opencore pour vous aider a patcher boot.efi pour les correctifs NVRAM et une meilleure gestion de la mémoire. (RAM).
  * Rappelez vous que ces EFI sont fournis dans le OpenCorePkg téléchargé plus tôt.

</details>

### Utilisateurs d'anciens systèmes

En bus des drivers d'au dessus, si votre matériel ne supporte pas les UEFI (2011 et avant), alors vous allez avoir besoin de ces EFI. Faites attention a la description de chacun car vous n'aurez peut-être pas besoin des 4:

* [OpenUsbKbDxe.efi](https://github.com/acidanthera/OpenCorePkg/releases)
  * Utilisés pour le sélectionneur de démarrage d'opencore **pour les utilisateuirs d'anciens systèmes utilisant DuetPkg**, [non recommandé et très nuisible sur les systèmes UEFI(Ivy Bridge et plus récent)](https://applelife.ru/threads/opencore-obsuzhdenie-i-ustanovka.2944066/page-176#post-856653)
* [HfsPlusLegacy.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlusLegacy.efi)
  * Variante ancienne de HfsPlus.efi, utilisée pour les systèmes qui n'ont pas de support d'instruction RDRAND. C'est généralement le cas sur les processeurs Sandy Bridge et plus vieux (tout comme les Ivy Bridge bas de gamme (i3 et Celerons))
  * Ne le mélangait PAS avec HfsPlus.efi, choisissez l'un ou l'autre selon votre PC, mais pas les 2.
* [OpenPartitionDxe](https://github.com/acidanthera/OpenCorePkg/releases)
  * Nécessaire pour lancer la partition de récupération de OS X 10.7 à 10.9
    * Ce fichier est fourni dans OpenCorePkg dans le dossier EFI/OC/Drivers
    * Note: Utilisateurs d'OpenDuet(donc sans UEFI) auront ce pilote déjà intégré, donc 0 utilité de le rajouter.
  * Non nécessaire pour OS X 10.10 Yosemite et plus récent

Ces fichiers vont dans le dossier Drivers dans votre EFI

<details>
<summary>Spécifique aux procésseurs 32-bits</summary>

Pour ceux avec des CPUs 32-Bit, vous allez devoir prendre ce driver.

* [HfsPlus32](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus32.efi)
  * Alternative à HfsPlusLegacy mais pour les CPUs 32-bit, ne PAS mélanger avec les autres pilotes HFS.

</details>

## Kexts

Un kest (pour **K**ernel **Ext**ension), est une sorte de Pilote pour macOS, ces fichiers vont dans le dossier Kexts de votre EFI.
 
* **Note pour Windows et Linux**: Les kexts ressemblent à des dossiers sur votre PC, **Vérifiez 2 fois** que le dossier que vous installez est in fichier avec un .kext (et n'esseayez pas de l'ajouter manuellement si il est manquant).
  * Si tout les kexts incluent aussi un fichier `.dSYM`, vous pouvez le supprimer. Ils sont uniquement pour le débuggage.
* **Note d'emplacement**: Ces fichiers **DOIVENT** êtres placés dans le dossier `EFI/OC/Kexts/`.

Tout les kexts listés plus bas sont disponibles **pré-complu=és** dans le [Kext Repo](http://kexts.goldfish64.com/). Les kexts sont compilés a chaque fois qu'un "commit" est posté.

### Nécessaire

- Kexts nécessaire

Sans ces 2 kexts, le système ne DÉMARRERA PAS.

* [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases)(<span style="color:red">Nécessaire</span>)
  * Émule la puce SMC trouvable dans les vrais macs, sans ça, macOS ne démarrera PAS.
  * Nécessite macOS 10.4 ou plus récent.
* [Lilu](https://github.com/acidanthera/Lilu/releases)(<span style="color:red">Nécessaire</span>)
  * Un kext qui patche beaucoup de choses, nécessaire pour AppleALC, WhateverGreen, VirtualSMC et beaucoup d'autres kexts. Sans lilu, ils ne fonctionneront pas.
  * Notz que même si Lilu fonctionne à partir de macOS 10.4, beaucoup de plugins ne marcheront que sur les nouvelles versions.

### Plugins VirtualSMC

Les plugins si dessous ne sont pas nécessaires pour démarrer le système et ajoute principalement des fonctions au systèùe comme du monitoring (Notez que VirtualSMC support 10.4, certains plugins peuvent nécessiter des versions plus récentes):

* SMCProcessor.kext
  * Utilisé pour voir la température CPU, **ne marche pas sur les système basées sur du CPU AMD**
  * Nécessite Mac OS X 10.7 Lion oru plus récent
* [SMCAMDProcessor](https://github.com/trulyspinach/SMCAMDProcessor)
  * Utilisé pour voir la température CPU sur les système basées sur du AMD Zen
  * **Sous developpement actif, peut être instable.**
  * nécessite AMDCPURyzenPowerManagement (voir les kexts spécifiques au CPU AMD plus bas)
  * Nécessite macOS 10.13 High Sierra ou plus récent
* SMCSuperIO.kext
  * Utilisé pour gérer la vitesse du ventilateur CPU, **ne marche pas sur les système basées sur du CPU AMD**
  * Nécessite OS X 10.6 Snow Leopard ou plus récent
* SMCLightSensor.kext
  * Utilisé pour les capteurs de lumière ambiante sur les PC Portables, **les PC Bureaux peuvent ignorer**
  * Ne pas utiliser si vous n'avez pas de capteur de lumière ambiante
  * Nécessite Mac OS X 10.6 Snow Leopard ou plus récent
* SMCBatteryManager.kext
  * utiliser pour gérer les batteries sur PC Portables, **les PC Bureaux peuvent ignorer**
  * Nécessite Mac OS X 10.4 Tiger ou plus récent
* SMCDellSensors.kext
  *Permet une surveillance et un contrôle plus précis des ventilateurs sur les machines Dell prenant en charge le mode de gestion du système (SMM)
  * **Ne pas utiliser si vous n'avez pas de machine dell compatible**, la plupart des PC Portables dell devrait l'utiliser, ça sera bénéfique
  * Nécessite OS X 10.7 Lion ou plus récent

### Partie graphique

* [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases)(<span style="color:red">Nécessaire</span>)
  * Utilisé pour les patchs graphiques, correctifs DRM, vérification d'ID de carte mère, correctifs de framebuffer, etc; ça sera bénéfique à TOUS les GPUs.
  * Notez que le fichier SSDT-PNLF.dsl inclus est uniquement nécessaire pour les PC Portables et les AIOs, voir [Démarrer avec les ACPI](https://dortania.github.io/Getting-Started-With-ACPI/) pour plus d'infos
  * nécessite OS X 10.6 Snow Leopard ou plus récent

### Audio

* [AppleALC](https://github.com/acidanthera/AppleALC/releases)
  * Utilisé pour le patch ApplmeHDA, autorisant le support de la majorité des controleurs audios sur carte mère
  * AppleALCU.kext est une version différente d'AppleALC qui supporte UNIQUEMENTR l'audio digital -mais vous pouvez toujours utiliser AppleALC.kext sur les système avec que de l'audio digital-
  * Les CPUs AMD 15h/16h peuvent avoir des problèmes avec AppleALC et les systèmes sous Ryzen et Threadripper ont rarement le support du micro
  * Nécessite OS X 10.8 Mountain Lion
  
- kext pour les anciens systèmes
Pour ceux qui prévoient de démarrer OS X 10.7 Lion et plus vieux devrait opter pour ces kexts la 
* [VoodooHDA](https://sourceforge.net/projects/voodoohda/)
  * Nécessite OS X 10.6 Snow Leopard
  
* [VoodooHDA-FAT](https://github.com/khronokernel/Legacy-Kexts/blob/master/FAT/Zip/VoodooHDA.kext.zip)
  * Similaire a celui du dessus, mais lui supporte les kernel 32 et 64 bits, donc parfait pour OS X 10.4 Tiger-10.5 Leopard et les CPUs 32 bits


### Ethernet

Ici on part du principe que vous savez quelle carte internet vous avez, on vous rappelle que les pages de spécifications du produit répertorient très probablement le type de carte réseau.

* [IntelMausi](https://github.com/acidanthera/IntelMausi/releases)
  * Nécessaire pour la pluspart des NICs Intel, les chipsets basés sur I211 auront besoin du kext SmallTreeIntel82576
  * Les NICs Intel 82578, 82579, I217, I218 et I219 sont officiellement supportés
  * Nécessite OS X 1.9 Mavericks, les utilisateurs de 10.6 a 10.8 doivent utiliser IntelSnowMausi pour les OSes plus anciens
* [SmallTreeIntel82576 kext](https://github.com/khronokernel/SmallTree-I211-AT-patch/releases)
  * Nécessaire pour les NICs I211,basées sur le Kext SmallTree mais patché pour supporter les I211. (marche pas sur [Monterey](./extras/monterey.md#ethernet)
)
  * Nécessaire pour les systèmes AMD basées sur des NICs Intel.
  * Nécessite OS X 10.9-12(v1.0.6), macOS 10.13-14(v1.2.5), macOS 10.15+(v1.3.0)
* [AtherosE2200Ethernet](https://github.com/Mieze/AtherosE2200Ethernet/releases)
  * Nécessiare pour les NICs Atheros et Killer
  * Nécessite OS X 10.8 Mountain Lion ou plus récent
  * Note: Les modèles Atheros Killer E2500 sont actuellement basées sur du Realtek, pour ces systèmes utilisez plutot[RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases) 
* [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases)
  * Pour les ethernet Realtek Gigabit
  *Nécessite OS X 10.8 Mountain Lion et plus pour les versions v2.2.0 et inférieures, macOS 10.12 et plus pour la version v2.2.2, macOS 10.14 et plus pour les versions v2.3.0 et plus
  * **NOTE:** Parfois la dernière version de ce Kext ne fonctionnera pas avec votre Ethernet. SI vous avez ce problème, esseayez une ancienne version..
* [LucyRTL8125Ethernet](https://www.insanelymac.com/forum/files/file/1004-lucyrtl8125ethernet/)
  * Pour le Realtek 2.5Gb 
  * nécessite macOS 10.15 Catalina ou plus récent.
*Pour les NICs Intel I225-V, les correctifs sont écrits dans la page [Propriété d'appareil comet lake](config.plist/comet-lake.md#deviceproperties). Aucun kext n'est nécessaire.
  * nécessite macOS 10.15 Catalina ou plus récent.
* Pour les NICs Intel I335, les correctifs sont ecrits lans le HEDT [Propriété d'appareil Sandy et Ivy Bridge-E ](config-HEDT/ivy-bridge-e.md#deviceproperties). Aucun kext n'est nécessaire.
  * Nécessite OS X 10.10 Yosemite

- kexts Ethernet pour les anciens systèmes

  Pertinent pour les anciennes installations macOS ou le matériel PC plus ancien.

* [AppleIntele1000e](https://github.com/chris1111/AppleIntelE1000e/releases)
  * Principalement pertinent pour les contrôleurs Intel Ethernet basés sur 10/100MB
  * Nécessite OS X 10.6 Snow Leopard ou plus récent
* [RealtekRTL8100](https://www.insanelymac.com/forum/files/file/259-realtekrtl8100-binary/)
  * Principalement pertinent pour les contrôleurs Ethernet Realtek basés sur 10/100MBe
  * Nécessite macOS 10.12 Sierra ou plus récent avec v2.0.0 [BCM5722D](https://github.com/chris1111/BCM5722D/releases)
  * Principalement pertinent pour les contrôleurs Ethernet Broadcom basés sur BCM5722 
  * Nécessite OS X 10.6 Snow Leopard ou plus récent


Et gardez également à l'esprit que certaines cartes réseau sont en fait prises en charge nativement dans macOS :


#### Aquantia

```md
# AppleEthernetAquantiaAqtion.kext
pci1d6a,1    = Aquantia AQC107
pci1d6a,d107 = Aquantia AQC107
pci1d6a,7b1  = Aquantia AQC107
pci1d6a,80b1 = Aquantia AQC107
pci1d6a,87b1 = Aquantia AQC107
pci1d6a,88b1 = Aquantia AQC107
pci1d6a,89b1 = Aquantia AQC107
pci1d6a,91b1 = Aquantia AQC107
pci1d6a,92b1 = Aquantia AQC107
pci1d6a,c0   = Aquantia AQC113
pci1d6a,4c0  = Aquantia AQC113
```

**Note** : En raison de certains micrologiciels obsolètes fournis sur de nombreuses cartes réseau Aquantia, vous devrez peut-être mettre à jour le micrologiciel sous Linux/Windows pour vous assurer qu'il est compatible avec macOS.

#### Intel

```md
# AppleIntel8254XEthernet.kext
pci8086,1096 = Intel 80003ES2LAN
pci8086,100f = Intel 82545EM
pci8086,105e = Intel 82571EB/82571GB

# AppleIntelI210Ethernet.kext
pci8086,1533 = Intel I210
pci8086,15f2 = Intel I225LM (Ajouté dans macOS 10.15 Catalina)

# Intel82574L.kext
pci8086,104b = Intel 82566DC
pci8086,10f6 = Intel 82574L

```

#### Broadcom 

```md
# AppleBCM5701Ethernet.kext
pci14e4,1684 = Broadcom BCM5764M
pci14e4,16b0 = Broadcom BCM57761
pci14e4,16b4 = Broadcom BCM57765
pci14e4,1682 = Broadcom BCM57762
pci14e4,1686 = Broadcom BCM57766
```



### USB

* [USBInjectAll](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/)
  * Used for injecting Intel USB controllers on systems without defined USB ports in ACPI
  * Shouldn't be needed on Desktop Skylake and newer
    * AsRock is dumb and does need this
    * Coffee Lake and older laptops are however recommended to use this kext
  * Does not work on AMD CPUs **at all**
  * Requires OS X 10.11 or newer

* [XHCI-unsupported](https://github.com/RehabMan/OS-X-USB-Inject-All)
  * Needed for non-native USB controllers
  * AMD CPU based systems don't need this
  * Common chipsets needing this:
    * H370
    * B360
    * H310
    * Z390(Not needed on Mojave and newer)
    * X79
    * X99
    * AsRock boards(On Intel motherboards specifically, B460/Z490+ boards do not need it however)

### WiFi and Bluetooth

#### Intel

* [AirportItlwm](https://github.com/OpenIntelWireless/itlwm/releases)
  * Adds support for a large variety of Intel wireless cards and works natively in recovery thanks to IO80211Family integration
  * Requires macOS 10.13 or newer and requires Apple's Secure Boot to function correctly
* [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases)
  * Adds Bluetooth support to macOS when paired with an Intel wireless card
  * Requires macOS 10.13 or newer

::: details More info on enabling AirportItlwm

To enable AirportItlwm support with OpenCore, you'll need to either:

* Enable `Misc -> Security -> SecureBootModel` by either setting it as `Default` or some other valid value
  * This is discussed both later on in this guide and in the post-install guide: [Apple Secure Boot](https://dortania.github.io/OpenCore-Post-Install/universal/security/applesecureboot.html)
* If you cannot enable SecureBootModel, you can still force inject IO80211Family(**Highly discouraged**)
  * Set the following under `Kernel -> Force` in your config.plist(discussed later in this guide):
  
![](./images/ktext-md/force-io80211.png)

:::

#### Broadcom

* [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup/releases)
  * Used for patching non-Apple/non-Fenvi Broadcom cards, **will not work on Intel, Killer, Realtek, etc**
  * Requires OS X 10.10 or newer
  * For Big Sur see [Big Sur Known Issues](./extras/big-sur#known-issues) for extra steps regarding AirPortBrcm4360 drivers.
* [BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM/releases)
  * Used for uploading firmware on Broadcom Bluetooth chipset, required for all non-Apple/non-Fenvi Airport cards.
  * To be paired with BrcmFirmwareData.kext
    * BrcmPatchRAM3 for 10.15+ (must be paired with BrcmBluetoothInjector)
    * BrcmPatchRAM2 for 10.11-10.14
    * BrcmPatchRAM for 10.8-10.10

::: details BrcmPatchRAM Load order

The order in `Kernel -> Add` should be:

1. BrcmBluetoothInjector
2. BrcmFirmwareData
3. BrcmPatchRAM3

However ProperTree will handle this for you, so you need not concern yourself

:::

### AMD CPU Specific kexts

* [XLNCUSBFIX](https://cdn.discordapp.com/attachments/566705665616117760/566728101292408877/XLNCUSBFix.kext.zip)
  * USB fix for AMD FX systems, not recommended for Ryzen
  * Requires macOS 10.13 or newer
* [VoodooHDA](https://sourceforge.net/projects/voodoohda/)
  * Audio for FX systems and front panel Mic+Audio support for Ryzen system, do not mix with AppleALC. Audio quality is noticeably worse than AppleALC on Zen CPUs
  * Requires OS X 10.6 or newer
* [AMDCPURyzenPowerManagement](https://github.com/trulyspinach/SMCAMDProcessor)
  * CPU power management for Ryzen systems
  * **Under active development, potentially unstable**
  * Requires macOS 10.13 or newer

### Extras

* [AppleMCEReporterDisabler](https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip)
  * Required on macOS 12.3 and later on AMD systems, and on macOS 10.15 and later on dual-socket Intel systems.
  * Affected SMBIOSes:
    * MacPro6,1
    * MacPro7,1
    * iMacPro1,1
* [CpuTscSync](https://github.com/lvs1974/CpuTscSync/releases)
  * Needed for syncing TSC on some of Intel's HEDT and server motherboards, without this macOS may be extremely slow or even unbootable.
  * **Does not work on AMD CPUs**
  * Requires OS X 10.8 or newer
* [NVMeFix](https://github.com/acidanthera/NVMeFix/releases)
  * Used for fixing power management and initialization on non-Apple NVMe
  * Requires macOS 10.14 or newer
* [SATA-Unsupported](https://github.com/khronokernel/Legacy-Kexts/blob/master/Injectors/Zip/SATA-unsupported.kext.zip)
  * Adds support for a large variety of SATA controllers, mainly relevant for laptops which have issues seeing the SATA drive in macOS. We recommend testing without this first.
  * macOS Big Sur Note: [CtlnaAHCIPort](https://github.com/dortania/OpenCore-Install-Guide/blob/master/extra-files/CtlnaAHCIPort.kext.zip) will need to be used instead due to numerous controllers being dropped from the binary itself
    * Catalina and older need not concern

::: details Legacy SATA Kexts

* [AHCIPortInjector](https://github.com/khronokernel/Legacy-Kexts/blob/master/Injectors/Zip/AHCIPortInjector.kext.zip)
  * Legacy SATA/AHCI injector, mainly relevant for older machines of the Penryn era
* [ATAPortInjector](https://github.com/khronokernel/Legacy-Kexts/blob/master/Injectors/Zip/ATAPortInjector.kext.zip)
  * Legacy ATA injector, mainly relevant for IDE and ATA devices(ie. when no AHCI option is present in the BIOS)
  
:::

### Laptop Input

To figure out what kind of keyboard and trackpad you have, check Device Manager in Windows or `dmesg | grep -i input` in Linux

::: warning

Most laptop keyboards are PS2! You will want to grab VoodooPS2 even if you have an I2C, USB, or SMBus trackpad.

:::

#### PS2 Keyboards/Trackpads

* [VoodooPS2](https://github.com/acidanthera/VoodooPS2/releases)
  * Works with various PS2 keyboards, mice, and trackpads
  * Requires macOS 10.11 or newer for MT2 (Magic Trackpad 2) functions
* [RehabMan's VoodooPS2](https://bitbucket.org/RehabMan/os-x-voodoo-ps2-controller/downloads/)
  * For older systems with PS2 keyboards, mice, and trackpads, or when you don't want to use VoodooInput
  * Supports macOS 10.6+

#### SMBus Trackpads

* [VoodooRMI](https://github.com/VoodooSMBus/VoodooRMI/releases/)
  * For systems with Synaptics SMBus trackpads
  * Requires macOS 10.11 or newer for MT2 functions
  * Depends on Acidanthera's VoodooPS2
* [VoodooSMBus](https://github.com/VoodooSMBus/VoodooSMBus/releases)
  * For systems with ELAN SMBus Trackpads
  * Supports macOS 10.14 or newer currently

#### I2C/USB HID Devices

* [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C/releases)
  * Supports macOS 10.11+
  * Attaches to I2C controllers to allow plugins to talk to I2C trackpads
  * USB devices using the below plugins still need VoodooI2C
  * Must be paired with one or more plugins shown below:

::: tip VoodooI2C Plugins

| Connection type | Plugin | Notes |
| :--- | :--- | :--- |
| Multitouch HID | VoodooI2CHID | Can be used with I2C/USB Touchscreens and Trackpads |
| ELAN Proprietary | VoodooI2CElan | ELAN1200+ require VoodooI2CHID instead |
| FTE1001 touchpad | VoodooI2CFTE | |
| Atmel Multitouch Protocol | VoodooI2CAtmelMXT | |
| Synaptics HID | [VoodooRMI](https://github.com/VoodooSMBus/VoodooRMI/releases/) | I2C Synaptic Trackpads (Requires VoodooI2C ONLY for I2C mode) |
| Alps HID | [AlpsHID](https://github.com/blankmac/AlpsHID/releases) | Can be used with USB or I2C Alps trackpads. Mostly seen on Dell laptops and some HP EliteBook models |

:::

#### Misc

* [ECEnabler](https://github.com/1Revenger1/ECEnabler/releases)
  * Fixes reading battery status on many devices (Allows reading EC fields over 8 bits long)
* [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys/releases)
  * Fixes brightness keys automatically

Please refer to [Kexts.md](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/Kexts.md) for a full list of supported kexts

## SSDTs

So you see all those SSDTs in the AcpiSamples folder and wonder whether you need any of them. For us, we will be going over what SSDTs you need in **your specific ACPI section of the config.plist**, as the SSDTs you need are platform specific. With some even system specific where they need to be configured and you can easily get lost if I give you a list of SSDTs to choose from now.

[Getting started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI/) has an extended section on SSDTs including compiling them on different platforms.

A quick TL;DR of needed SSDTs(This is source code, you will have to compile them into a .aml file):

### Desktop

| Platforms | **CPU** | **EC** | **AWAC** | **NVRAM** | **USB** |
| :-------: | :-----: | :----: | :------: | :-------: | :-----: |
| Penryn | N/A | [SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | N/A | N/A | N/A |
| Lynnfield and Clarkdale | ^^ | ^^ | ^^ | ^^ | ^^ |
| SandyBridge | [CPU-PM](https://dortania.github.io/OpenCore-Post-Install/universal/pm.html#sandy-and-ivy-bridge-power-management) (Run in Post-Install) | ^^ | ^^ | ^^ | ^^ |
| Ivy Bridge | ^^ | ^^ | ^^ | ^^ | ^^ |
| Haswell | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html) | ^^ | ^^ | ^^ | ^^ |
| Broadwell | ^^ | ^^ | ^^ | ^^ | ^^ |
| Skylake | ^^ | [SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | ^^ | ^^ | ^^ |
| Kaby Lake | ^^ | ^^ | ^^ | ^^ | ^^ |
| Coffee Lake | ^^ | ^^ | [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html) | [SSDT-PMC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/nvram.html) | ^^ |
| Comet Lake | ^^ | ^^ | ^^ | N/A | [SSDT-RHUB](https://dortania.github.io/Getting-Started-With-ACPI/Universal/rhub.html) |
| AMD (15/16h) | N/A | ^^ | N/A | ^^ | N/A |
| AMD (17/19h) | [SSDT-CPUR for B550 and A520](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-CPUR.aml) | ^^ | ^^ | ^^ | ^^ |

### High End Desktop

| Platforms | **CPU** | **EC** | **RTC** | **PCI** |
| :-------: | :-----: | :----: | :-----: | :-----: |
| Nehalem and Westmere | N/A | [SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | N/A | N/A |
| Sandy Bridge-E | ^^ | ^^ | ^^ | [SSDT-UNC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/unc0) |
| Ivy Bridge-E | ^^ | ^^ | ^^ | ^^ |
| Haswell-E | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html) | [SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | [SSDT-RTC0-RANGE](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html) | ^^ |
| Broadwell-E | ^^ | ^^ | ^^ | ^^ |
| Skylake-X | ^^ | ^^ | ^^ | N/A |

### Laptop

| Platforms | **CPU** | **EC** | **Backlight** | **I2C Trackpad** | **AWAC** | **USB** | **IRQ** |
| :-------: | :-----: | :----: | :-----------: | :--------------: | :------: | :-----: | :-----: |
| Clarksfield and Arrandale | N/A | [SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | [SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html) | N/A | N/A | N/A | [IRQ SSDT](https://dortania.github.io/Getting-Started-With-ACPI/Universal/irq.html) |
| SandyBridge | [CPU-PM](https://dortania.github.io/OpenCore-Post-Install/universal/pm.html#sandy-and-ivy-bridge-power-management) (Run in Post-Install) | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Ivy Bridge | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Haswell | [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug.html) | ^^ | ^^ | [SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad.html) | ^^ | ^^ | ^^ |
| Broadwell | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Skylake | ^^ | [SSDT-EC-USBX](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix.html) | ^^ | ^^ | ^^ | ^^ | N/A |
| Kaby Lake | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Coffee Lake (8th Gen) and Whiskey Lake | ^^ | ^^ | [SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight.html) | ^^ | [SSDT-AWAC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/awac.html) | ^^ | ^^ |
| Coffee Lake (9th Gen) | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Comet Lake | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ | ^^ |
| Ice Lake | ^^ | ^^ | ^^ | ^^ | ^^ | [SSDT-RHUB](https://dortania.github.io/Getting-Started-With-ACPI/Universal/rhub.html) | ^^ |

Continuing:

| Platforms | **NVRAM** | **IMEI** |
| :-------: | :-------: | :------: |
|  Clarksfield and Arrandale | N/A | N/A |
| Sandy Bridge | ^^| [SSDT-IMEI](https://dortania.github.io/Getting-Started-With-ACPI/Universal/imei.html) |
| Ivy Bridge | ^^ | ^^ |
| Haswell | ^^ | N/A |
| Broadwell | ^^ | ^^ |
| Skylake | ^^ | ^^ |
| Kaby Lake | ^^ | ^^ |
| Coffee Lake (8th Gen) and Whiskey Lake | ^^ | ^^ |
| Coffee Lake (9th Gen) | [SSDT-PMC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/nvram.html) | ^^ |
| Comet Lake | N/A | ^^ |
| Ice Lake | ^^ | ^^ |

# Now with all this done, head to [Getting Started With ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)
