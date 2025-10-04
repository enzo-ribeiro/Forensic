
## Introduction 
La surface d'incident fait référence aux points d'entré du système. 

Il y a une différence entre Surface d'Attaque et une Surface d'Incident. 

Surface d'Attaque :
 - Ports ouverts
 - Services de course
 - Exécution de logiciels ou d'applications présentant des vulnérabilités
 - Communication réseau

Face à la surface d'attaque, le but est de la minimiser :
 - Identifier et corriger les vulnérabilités
 - Minimiser l'utilisation de services indésirables
 - Vérifiez les interfaces avec lesquelles l'utilisateur interagit
 - Minimiser les services, applications, ports, etc. exposés publiquement

Surface d'Incident désigne elle, tous les domaines de système impliqué dans la détection, la gestion et la réponse à un incident de sécurité. 

Exemple de Surface d'Incident :
- Journaux système
- auth.log, syslog, krnl.log, etc
- Trafic réseau
- Processus en cours d'exécution
- Services de course
- L'intégrité des fichiers et des processus

Comprendre la surface de l’incident est essentiel pour répondre efficacement à une attaque en cours, atténuer les dommages, récupérer les systèmes affectés et appliquer les leçons apprises pour prévenir de futurs incidents.
## Processus et Communication Réseau

Les processus et la communication réseau sont essentiels à tout système d'exploitation lors des enquêtes sur les incidents. La surveillance et l'analyse des processus, notamment ceux liés à la communication réseau, peuvent contribuer à identifier et à résoudre les incidents de sécurité. Les processus en cours d'exécution sont un élément clé de la surface d'incident Linux , car ils peuvent constituer une source potentielle de preuves d'une attaque.
### Analyse d'un processus simple
Pour commencer on compile le processus qui est un simple .c
```Bash
gcc simple.c -o /tmp/simple
/tmp/simple
```
Une fois la notre binaire lancé, nous ppouvons ouvrir un nouveau terminal et voir les processus en cours d'exécution avec la commande ``ps aux``:
![[SCR-20251004-lada.png]]
- `a` : Affiche les processus pour tout les utilisateurs
- `u` : Affiche le format orienté utilisateur (inclut l'utilisateur et l'heure de début)
- `x` : Inclut les processus non attachés à un terminal

La sortie de cette commande nous donne ces informations :
Shows ps aux output

La sortie fournit les informations suivantes :
- UTILISATEUR : L'utilisateur qui possède le processus.
- PID : ID du processus.
- %CPU : pourcentage d'utilisation du processeur.
- %MEM : Pourcentage d'utilisation de la mémoire.
- VSZ : Taille de la mémoire virtuelle.
- RSS : Resident Set Size (mémoire actuellement utilisée).
- TTY : Terminal associé au processus.
- STAT : État du processus (par exemple, R pour exécution, S pour veille, Z pour zombie).
- START : Heure de début du processus.
- COMMANDE : Commande qui a démarré le processus

Nous pouvons lier cette commande avec un ``grep`` pour avoir un résultat précis.
Avec le ``PID`` nous pouvons faire une recherche plus appronfondie, comme les ressources qu'utilse notre binaire. Nous allons faire ça avec la commande ``lsof``.
```Bash
lsof -p <PID>
```
![[SCR-20251004-lfdn.png]]
### Analyse d'un processus avec communication réseau
