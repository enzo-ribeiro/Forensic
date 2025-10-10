
## Mémoire Volatile

Les PC stockent et accèdent aux informations grâce à une hiérarchie, chaque niveau offrant un compromis entre vitesse et capacité. Dans le schéma qui suit c'est du plus rapide au plus lent : 

CPU Registers -> CPU Cache -> RAM -> SSD

Les registres et cache du CPU sont extrêmement rapides, mais leurs tailles est très limité. La RAM, elle, est la principale mémoire de travail du système et des programmes actifs. Le disque lui est dédiée au stockage de données long termes. 

### RAM
La RAM est séparé en 2 espaces : 
 - Noyau : réservé au système et aux services bas niveau
 - Utilisateur : contient les processus lancés par l'utilisateur ou les applications


## Dump Mémoire

La création d'un dump mémoire dépend du système utilisé. 

Pour les outils, sur Windows nous avons : RAMMap (sysinternals), WinPmem ou FTK Imager.
Pour Linux, nous pouvons évoquer LiME.

### Type de Dump Mémoire 

| Type      | Description                                                                                                                                                                                                                                                                                                                                        |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Complet   | Dump de toute la RAM                                                                                                                                                                                                                                                                                                                               |
| Processus | Capture la mémoire d'un processus en cours d'exécution                                                                                                                                                                                                                                                                                             |
| Swap      | Les systèmes déchargent une partie de la mémoire sur le disque lorsque la RAM est pleine. Sous Windows, ce contenu est stocké dans pagefile.sys, et sous Linux , dans la partition ou le fichier d'échange. Ces fichiers peuvent contenir des fragments de données qui se trouvaient auparavant en RAM , offrant ainsi un contexte supplémentaire. |

### Les défis 

| Défis                                           | Description                                                                                                                                                                                                                            |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Modules non liés ou masqués                     | Les logiciels malveillants peuvent se dissocier des listes de processus, les rendant ainsi invisibles pour les outils s'appuyant sur des requêtes de système d'exploitation classiques .                                               |
| DKOM (manipulation directe des objets du noyau) | Modifie les structures du noyau pour masquer les processus, les threads ou les pilotes des outils système standard.                                                                                                                    |
| Injection de code                               | Un code malveillant est injecté dans des processus légitimes (par exemple, explorer.exe, svchost.exe) pour se fondre dans la masse et échapper à la détection.                                                                         |
| Correction de la mémoire                        | Les logiciels malveillants modifient le contenu de la mémoire ou les API système lors de l'exécution pour perturber les outils d'analyse médico-légale ou produire de fausses données.                                                 |
| API d'accrochage ou appels système              | Intercepte et modifie la sortie des fonctions critiques (par exemple, ReadProcessMemory, ZwQuerySystemInformation) pour masquer les comportements malveillants.                                                                        |
| Charges utiles chiffrées ou compressées         | Les charges utiles sont conservées cryptées ou compressées en mémoire, et ne sont décryptées qu'au moment de l'exécution, ce qui rend l'analyse statique difficile.                                                                    |
| Charges utiles basées sur des déclencheurs      | Certains logiciels malveillants résidant en mémoire ne se décompressent ou ne s'exécutent que lorsque des conditions spécifiques sont remplies, limitant ainsi ce que les analystes peuvent capturer lors de l'acquisition de routine. |

### Question THM
```
Q : What tool is commonly used by attackers to extract credentials from memory?

A : Mimikatz
```

```
Q : What type of memory dump captures all RAM, including user and kernel space?

A : Full
```

```
Q : What Linux tool can be used to extract memory for forensic purposes?

A : LiME
```

```
Q : Which file on Windows systems stores memory during hibernation?

A : hiberfil.sys
```

```
Q : What anti-forensics technique hides processes by altering kernel structures?

A : DKOM
```

## Les empreintes
Généralement ce que nous allons rechercher dans une analyse Forensic c'est :
 - Process Hollowing : technique où la mémoire d'un processus de confiance est remplacée par un code malveillant.
 - Processus suspect : Processus qui s'exécutent sans fichier correspondant sur le disque.
 - DLL Injection : DLL injecté dans l'espace mémoire d'un processus légitime.
 - API Hooking : intercepte ou modifie les appels de fonction normaux afin de masquer l'activité ou de manipuler le comportement du système.
 - RootKits : en particulier ceux fonctionnant dans l'espace noyau, qui manipulent les structures de mémoire pour masquer des fichiers, des processus ou des connexions réseau.

### MITRE ATT&CK
Voici des codes assez courant pour les rapports : 

| Nom                               | Code      |
| --------------------------------- | --------- |
| Credential Access                 | T1003     |
| In-Memory Script Execution        | T1086     |
| Scheduled Task                    | T1053.005 |
| Create or Modify System Process   | T1543.003 |
| Remote Services                   | T1021.002 |
| Command and Scripting Interpreter | T1059.001 |

### Question THM
```
Q : What technique involves replacing a trusted process’s memory with malicious code?

A : Process Hollowing
```

```
Q : Which Windows service provides PowerShell remoting?

A : WinRM
```

```
Q : What MITRE technique ID is associated with in-memory PowerShell execution?

A : T1086
```

```
Q : What command-line tool enables remote execution and is linked to lateral movement (T1021.002)?

A : PsExec
```

```
Q : Which MITRE technique involves setting tasks that persist through reboots (e.g., schtasks.exe)?

A : T1053.005
```