
## Architecture Android

L'architecture d'Android est superposée et modulaire, ce qui la rend polyvalente et complexe d'un point de vue Forensic.
![[5e8dd9a4a45e18443162feab-1747826237632.svg]]

Elle est composé des couche suivantes : 
#### Noyau Linux
C'est la base du système, elle va fournir des fonctionnalités de base du système. Elle agit comme couche entre l'hardware et le software

- Services de base
- Gestion des processus
- Gestion de la mémoire
- Conducteurs de dispositifs

#### Bibliothèques Native
Elles fournissent des fonctionnalités de base du système et aux applications.

- Bibliothèques du système
- Cadres pour les médias
- Moteur de base de données SQLite
- Moteur de navigateur WebKit

#### Framework Application
Elle fournit des API pour des niveau plus haut, comme la localisation, la téléphonie ... 

- API pour le développement d'applications
- Gestion des ressources
- Interaction des composants
- Traitement des services

#### Application
La couche la plus haute de l'architecture Android. Elle comprend le système et les applications installé par l'utilisateur. C'est la principale source de données dans une enquêtes Forensic. 

- Demandes d'utilisation
- Applications du système
- Applications de tiers


### Système de fichiers Android

Android utilise un système de fichier basé sur Linux qui partitionne et met des "couches" pour gérer les données de manière sécurisée et efficace. La structure est personnalisée en fonction du matériel et de l'OS.   
![[5e8dd9a4a45e18443162feab-1747796686545-1.png]]

### Partitions du système de fichier
```Bash
        
├── system/                  → Android OS system files (read-only in user mode) │  ├── bin/                     → System binaries 
│   ├── lib/                 → Shared libraries 
│   └── framework/           → Java framework .jar files 
│ 
├── data/                    → Main user data partition 
│   ├── app/                 → Installed APKs 
│   ├── data/                → App private data 
│   ├── misc/                → Misc system info (e.g., WiFi configs) 
│   ├── media/               → Encrypted storage mount point 
│   └── system/              → User accounts, settings 
│ 
├── sdcard/ (or /storage/emulated/0) → User files, photos, downloads 
│ 
├── vendor/                 → OEM-specific binaries/libraries 
│ 
└── dev/, proc/, sys/       → Kernel and device interfaces (like Linux)`
```

### Analyse d'une image 
Nous avons un fichier ``.ad1``, nous l'importons dans FTK Imager. 
Ensuite nous avons accès à certains fichiers de l'appareil suspect : 
![[Pasted image 20251001170204.png]]

Le but de l'exercice ici est de trouver le numéro de série qui se trouve dans ``/system/build.prop``: 
![[Pasted image 20251001170322.png]]

### Question THM
```txt
Q : Navigate the directories in FTK Imager. Examine the build.prop file found in the system folder. What is the device's serial number?

A : ABC123456789
```


## Artefacts 