
## C'est quoi ?  
  
Le registre Windows est une collection de base de données qui contient les données de configuration système. Le registre contient des données sur : l'hardware, le software et les utilisateurs. Il va également contenir des données sur les fichiers récemment utilisé, les programmes récemment exécutés et les différent appareils connectés au système. A partir de ces informations nous pouvons comprendre l'importance que le registre peut avoir dans les recherches forensics.  
  
Le registre Windows est accessible a partir du programme `regedit.exe`.  
Il se présente sous forme de Clés et de Valeurs.  
  
## Structure  
  
1. HKEY_CURRENT_USER  
2. HKEY_USERS  
3. HKEY_LOCAL_MACHINE  
4. HKEY_CLASSES_ROOT  
5. HKEY_CURRENT_CONFIG  
  
Voici les clé et leur "description" :  
- **HKCU (HKEY_CURRENT_USER)** : Contient les paramètres de l'utilisateur connecté (dossiers, couleurs, Panneau de config). Sous-clé de **HKU**.  
     
- **HKU (HKEY_USERS)** : Regroupe tous les profils utilisateur chargés sur l’ordinateur.  
     
- **HKLM (HKEY_LOCAL_MACHINE)** : Stocke les paramètres globaux du système, valables pour tous les utilisateurs.  
     
- **HKCR (HKEY_CLASSES_ROOT)** : Définit quelle application ouvre un type de fichier. Fusionne les infos de **HKLM\Software\Classes** (paramètres par défaut) et **HKCU\Software\Classes** (prioritaires pour l'utilisateur).  
     
    - Modification utilisateur → **HKCU\Software\Classes**  
         
    - Modification globale → **HKLM\Software\Classes**  
         
    - Écriture sous **HKCR** → Stockage sous **HKLM\Software\Classes** (sauf si déjà existant sous **HKCU**)  
         
- **HKCC (HKEY_CURRENT_CONFIG)** : Contient le profil matériel utilisé au démarrage du système.  
## Accès aux ruches  
  
Si nous avons accès au système en direct (connecté physiquement à la machine), nous pouvons nous rendre dans le `regedit`. Par contre il se peut que nous ayons accès seulement à l'image, il faut donc savoir que la majorité des ruches se trouve dans : `C:\Windows\System32\Config`. 

Nous pouvons également trouver les ruches de l'utilisateur dans `C:\Users\<username>\` dans les "fichiers" suivant : 
- `NTUSER.DAT` (Monté sur HKEY_CURRENT_USER quand l'utilisateur est loggé)
- `USERCLASS.DAT` (Monté sur HKEY_CURRENT_USER\Software\CLASSES)

Le ``NTUSER.DAT`` est situé dans : ``C:\Users\<username>\``

Le ``USERCLASS.DAT`` est lui situé dans : ``C:\Users\<username>\AppData\Local\Microsoft\Windows``

Mise à part ces 2 ruches importante, nous avons une autre ruche qui peut être tout aussi notable, la ruche  `Amcache`, elle se trouve dans ``C:\Windows\AppCompat\Programs\Amcache.hve``. Le système créer cette ruche pour enregistrer les informations sur les programme récemment exécutés sur le système.

D'autre ruche sont présente voici 5 exemples de ruches : 
- ``DEFAULT``
- ``SAM``
- ``SECURITY``
- ``SOFTWARE``
- ``SYSTEM``

Elles sont toutes présente dans ``C:\Windows\System32\Config\``

## Tools

Voici 3 outils pour l'extraction de la base de registre : 
- KAPE
- Autopsy
- FTK Imager

## Lecture du registre

Voici 2 outils disponible pour l'analyse des registres récupéré : 
- Registry Explorer (de Zimmerman)
- RegRipper : cet utilitaire va traiter une ruche à la fois. Il générera un rapport qui extrait certaines clé et certaines valeurs importante pour l'investigation de cette ruche. 

### Où trouver les élément important ? 

#### Version du système : 
- ``SOFTWARE\Microsoft\Windows NT\CurrentVersion``

#### Nom de l'ordinateur : 
- ``SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName``

#### Information sur le fuseau horaire : 
- ``SYSTEM\CurrentControlSet\Control\TimeZoneInformation``

#### Interfaces réseau : 
- ``SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces``

#### Programme à démarrage automatique : 
- `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Run`
- `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\RunOnce`
- `SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce`
- `SOFTWARE\Microsoft\Windows\CurrentVersion\policies\Explorer\Run`
- `SOFTWARE\Microsoft\Windows\CurrentVersion\Run`

#### Information sur la base SAM : 
- ``SAM\Domains\Account\Users``

#### Fichiers récents (par utilisateur) : 
- `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`

#### Retrouver les ShellBags :
- `USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\Bags`
- `USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\BagMRU`
- `NTUSER.DAT\Software\Microsoft\Windows\Shell\BagMRU`
- `NTUSER.DAT\Software\Microsoft\Windows\Shell\Bags`

On peut analyser les ShellBags avec l'application ShellBags Explorer.
##### Que contiennent les SHellBags ? 
- Le **nom et le chemin** des dossiers visités.
- La **date et l’heure** d’accès, de modification et de création.
- La **taille et l’emplacement** des fenêtres d’Explorateur.
- Des indications sur les dossiers **supprimés**.
- La vue utilisée (icônes, détails, liste, etc.).

##### Pourquoi est-ce utile ? 
- **Retracer l’activité utilisateur** (quels dossiers ont été ouverts, quand et comment).
- **Identifier des traces de fichiers supprimés ou cachés**.
- **Analyser les actions suspectes** (ex. : un dossier sensible ouvert avant un effacement de preuves).
- **Démontrer qu’un utilisateur a accédé à un dossier spécifique**, même s'il a ensuite disparu.


#### Recherche Windows : 
- `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths`
- `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery`

#### Retrouver les USB :
- `SYSTEM\CurrentControlSet\Enum\USBSTOR`
- `SYSTEM\CurrentControlSet\Enum\USB`
- ``SOFTWARE\Microsoft\Windows Portable Devices\Devices``
