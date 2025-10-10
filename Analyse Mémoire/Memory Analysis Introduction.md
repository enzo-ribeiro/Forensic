
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

Pour Linux, nous pouvons évoquer 