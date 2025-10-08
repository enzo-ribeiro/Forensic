## Introduction 

Dans le monde Linux comprendre les logs revient à comprendre comment le système fonctionne. Les logs nous permettent de remonter les historiques d'activités, les erreurs et les événements système. 

### Les types de logs
 - Kernel : Offrent un accès direct au fonctionnement interne de votre système, donnent des informations au niveau du matériel, des drivers et des erreurs système
 - Utilisateur : enregistrent les interactions entre les utilisateurs, les applications et le système d'exploitation, inclus les connexions, les exécution de commandes ...

### Niveaux de log

| Niveau    | Description                                                                           |
| --------- | ------------------------------------------------------------------------------------- |
| EMERGENCY | Niveau le plus élevé, apparait quand le système est inutilisable ou crash             |
| ALERT     | Fournit des informations là où l'attention de l'utilisateur est requise immédiatement |
| CRITICAL  | Fournit des informations sur les erreus critiques                                     |
| ERROR     | Avertit les utilisateur des erreurs non critique                                      |
| WARNING   | Affiche les avertissements d'erreur non imminentes                                    |
| NOTICE    | Avertit des événements qui mérite d'être examiné                                      |
| INFO      | Fournit des informations sur les actions et les opérations du système                 |
| DEBUG     | Information détaillé pour le débogage                                                 |

### Log Kernel

Le kernel est le coeur du système, il orchestre les interactions matérielles, la gestion des processus et l'allocation des ressources. Ces logs nous offres un accès direct au fonctionnement du système. 

Nous pouvons examiner ces logs avec la commande ``dmesg``.

### Question THM

```
Q : Which type of logs provide messages related to hardware events and system errors?

A : kernel
```

```
Q : What is the memory space used to store system messages?

A : Kernel ring buffer
```

```
Q : What is the default log level used to inform about non-imminent errors?

A : Warning
```

## Repertoire `/var/log`

Ces logs sont indispensables dans une investigation, car il renferme des informations sur les activités, les événements du système, des enregistrements de processus, des activités utilisateurs, des connexions réseaux et bien plus encore. 

### `Kern.log` & `dmesg`

Le fichier `Kern.log` est dédié à l'enregistrement des messages du noyau. Il est essentiel pour diagnostiquer les pannes matérielles. 
```Bash
$ tail -f /var/log/kern.log

Jun 27 10:54:13 tryhackme kernel: raid6: sse2x1   gen()  4131 MB/s
Jun 27 10:54:13 tryhackme kernel: raid6: sse2x1   xor()  3083 MB/s
Jun 27 10:54:13 tryhackme kernel: raid6: using algorithm avx2x1 gen() 9658 MB/s
Jun 27 10:54:13 tryhackme kernel: raid6: .... xor() 4742 MB/s, rmw enabled
Jun 27 10:54:13 tryhackme kernel: raid6: using avx2x2 recovery algorithm
Jun 27 10:54:13 tryhackme kernel: xor: automatically using best checksumming function   avx       
Jun 27 10:54:13 tryhackme kernel: Btrfs loaded, crc32c=crc32c-intel, zoned=yes, fsverity=yes
Jun 27 12:11:02 tryhackme kernel: custom_kernel: loading out-of-tree module taints kernel.
Jun 27 12:11:02 tryhackme kernel: custom_kernel: module verification failed: signature and/or required key missing - tainting kernel
Jun 27 12:11:02 tryhackme kernel: Custom Kernel Module Loaded: Simulating Kernel Exploit
```

Le fichier `dmesg` est nous permet de détecter des messages inhabituels au démarrage, pouvant nous indiquer une altération ou un problème matériel. Il est très compliquer de suivre efficacement ce format de log 
```Bash
$ tail /var/log/dmesg
[   30.719880] kernel: audit: type=1400 audit(1719484416.292:4): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-client.action" pid=352 comm="apparmor_parser"
[   30.719886] kernel: audit: type=1400 audit(1719484416.292:5): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-helper" pid=352 comm="apparmor_parser"
[   30.719888] kernel: audit: type=1400 audit(1719484416.292:6): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/connman/scripts/dhclient-script" pid=352 comm="apparmor_parser"
[   30.719891] kernel: audit: type=1400 audit(1719484416.292:7): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/{,usr/}sbin/dhclient" pid=352 comm="apparmor_parser"
[   30.829947] kernel: audit: type=1400 audit(1719484416.400:8): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/cups/backend/cups-pdf" pid=360 comm="apparmor_parser"
[   30.829954] kernel: audit: type=1400 audit(1719484416.400:9): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/sbin/cupsd" pid=360 comm="apparmor_parser"
[   30.829957] kernel: audit: type=1400 audit(1719484416.400:10): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/sbin/cupsd//third_party" pid=360 comm="apparmor_parser"
[   30.836788] kernel: audit: type=1400 audit(1719484416.408:11): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/bin/man" pid=364 comm="apparmor_parser"
[   49.317142] kernel: EXT4-fs (xvda1): resizing filesystem from 10485499 to 15728379 blocks
[   49.568767] kernel: EXT4-fs (xvda1): resized filesystem to 15728379
```

### `Auth.log`

Comme son nom l'indique il permet de regrouper tous les événements liées à une authentification. Quelle soit réussie ou échouée la tentative est enregistré dans ce fichier.

Simulation d'une connexion SSH sur une machine : 
```Bash
$ ssh root@localhost -p 22

The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is ------
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
root@localhost: Permission denied (publickey).
```

Voici le retour dans le fichier `auth.log`
```Bash
$ tail -f /var/log/auth.log

Jun 27 08:55:45 tryhackme sshd[70808]: Connection closed by authenticating user root 127.0.0.1 port 43086 [preauth]
Jun 27 08:56:10 tryhackme sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/tail -f /var/log/auth.log
Jun 27 08:56:10 tryhackme sudo: pam_unix(sudo:session): session opened for user root by ubuntu(uid=0)
```

Nous pouvons filtrer l'output de la commande avec grep : 
```Bash
$ grep 'Accepted password' /var/log/auth.log

May 31 11:17:00 server sshd[2009]: Accepted password for root from 192.168.1.50 port 22 ssh2
```

'Accepted password' peut être remplacé par 'sudo' ou autre chose. 

### `syslog`

Ce fichier nous permet de collecter divers messages système, ce qui en fait un point central pour comprendre les événements système. Ce journal capture tout, des exécutions de tâches cron aux activités du noyau, offrant une vue complète de l'état et des activités de votre système.

```Bash
$ grep 'CRON' /var/log/syslog

Jun 18 00:09:01 tryhackme CRON[18304]: (root) CMD (  [ -x /usr/lib/php/sessionclean ] && if [ ! -d /run/systemd/system ]; then /usr/lib/php/sessionclean; fi)
Jun 18 00:09:01 tryhackme CRON[18305]: (root) CMD (   test -x /etc/cron.daily/popularity-contest && /etc/cron.daily/popularity-contest --crond)
Jun 18 00:17:01 tryhackme CRON[18358]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Jun 18 00:39:01 tryhackme CRON[18367]: (root) CMD (  [ -x /usr/lib/php/sessionclean ] && if [ ! -d /run/systemd/system ]; then /usr/lib/php/sessionclean; fi)
Jun 18 01:09:01 tryhackme CRON[18424]: (root) CMD (  [ -x /usr/lib/php/sessionclean ] && if [ ! -d /run/systemd/system ]; then /usr/lib/php/sessionclean; fi)
Jun 18 01:17:01 tryhackme CRON[18476]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Jun 18 01:39:01 tryhackme CRON[18486]: (root) CMD (  [ -x /usr/lib/php/sessionclean ] && if [ ! -d /run/systemd/system ]; then /usr/lib/php/sessionclean; fi)
```

### `btmp` & `wtmp`
Le `/var/log/btmp`le fichier enregistre les tentatives de connexion échouées, tandis que le `/var/log/wtmp`enregistre chaque connexion et déconnexion.

```Bash
$ last -f /var/log/wtmp

ubuntu   pts/3        10.110.7.185      Thu Jun 27 11:50 - 15:04  (03:13)
ubuntu   pts/2        10.110.7.185      Thu Jun 27 11:50 - 15:05  (03:15)
ubuntu   pts/1        10.110.7.185      Thu Jun 27 10:36 - 13:28  (02:52)
ubuntu   pts/0        10.110.7.185      Thu Jun 27 10:34 - 13:28  (02:53)
reboot   system boot  5.15.0-1053-aws  Thu Jun 27 10:33   still running
reboot   system boot  5.15.0-1053-aws  Sun Jun 23 16:34   still running
```

### Question THM
```
Q : Which log file can be used to record failed login attempts only?

A : btmp
```

## Log utilisateurs avec `syslog`

### Présentation

`Syslog` a évolué avec le temps avec déiférentes versions : 
 - syslogd : démon d'origine
 - syslog-ng : version amélioré avec d'avantages de fonctionnalités basé sur le contenu, le transport TCP ...
 - rsyslog : démon le plus récents avec des performances élevées, prise ne cahrge du TLS, du stockage de BDD et de la modification des messages

Syslog se présente sous trois composants principaux : 
 - Le démon : `rsyslog` ou `syslogd` gères la journalisation
 - Fichier de configuration : `/etc/rsyslog` ou `/etc/syslog.conf`
 - Fichier de journaux : `/var/log`

### Niveaux de gravité des logs

| Valeur | Gravité       | Mot-clé | Description                         |
| ------ | ------------- | ------- | ----------------------------------- |
| 0      | Emergency     | emerg   | Le système est inutilisable         |
| 1      | Alert         | alert   | Une action immédiate est nécessaire |
| 2      | Critical      | crit    | Conditions critiques                |
| 3      | Error         | err     | Conditions critiques                |
| 4      | Warning       | warning | Conditions d'avertissement          |
| 5      | Notice        | notice  | Normal mais significatif            |
| 6      | Informational | info    | Messages d'information              |
| 7      | Debug         | debug   | Messages de niveau de débogage      |

### Installations Syslog
| Code numérique | Mot-clé          | Nom d'installation                      |
| -------------- | ---------------- | --------------------------------------- |
| 0              | kern             | Messages du noyau                       |
| 1              | user             | Messages au niveau de l'utilisateur     |
| 2              | mail             | Messages au niveau de l'utilisateur     |
| 3              | daemon           | Démons système                          |
| 4              | auth             | Messages de sécurité/d'authentification |
| 5              | syslog           | Messages syslog internes                |
| 6              | lpr              | Sous-système d'imprimante en ligne      |
| 7              | news             | Sous-système d'informations du réseau   |
| 8              | uucp             | Sous-système UUCP                       |
| 9              | cron             | Sous-systèmes Cron                      |
| 10             | authpriv         | Messages de sécurité                    |
| 11             | ftp              | FTP démon                               |
| 12             | ntp              | NTP Sous-système                        |
| 13             | security         | Audit du journal de sécurité            |
| 14             | console          | Alertes du journal de la console        |
| 15             | solaris-cron     | Journaux de planification               |
| 16-23          | local0 to local7 | Installations utilisées localement      |
### Question THM
```
Q : What severity level keyword is used to indicate immediate action is needed in a syslog message?

A : Alert
```

```
Q : What facility code is used for cron jobs?

A : 9
```


## `Journalctl`
