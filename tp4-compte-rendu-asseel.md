# Compte Rendu TP4 Asseel

## Exercice 1



#### 1) Quels sont les 5 derniers paquets installés sur votre machine ?

* >`grep installed dpkg.log | tail -5`

``` 
2019-09-23 12:31:19 status installed systemd:amd64 240-6ubuntu5.7
2019-09-23 12:31:26 status installed man-db:amd64 2.8.5-2
2019-09-23 12:31:26 status installed plymouth-theme-ubuntu-text:amd64 0.9.4-1ubuntu1
2019-09-23 12:31:26 status installed dbus:amd64 1.12.12-1ubuntu1.1
2019-09-23 12:31:47 status installed initramfs-tools:all 0.131ubuntu19.1 
```


#### 2) Utiliser dpkg et apt pour compter le nombre de paquets installés (ne pas hésiter à consulter le manuel !). Comment explique-t-on la (petite) différence de comptage ?

* >`dpkg -l | grep "^ii" | wc -l`

```
514
```

>`apt list --installed |wc -l`

```
515
```

IL y a une differences, cela vient du fait qu'il y a une ligne de lignes de texte au debut du **apt list**.

#### 2) Combien de paquets sont disponibles en téléchargement ?

* >`apt list  | wc -l`

    il y a ***64118**

#### 4) Créer un alias “maj” qui met à jour le système

```
- nano .bashrc
- maj ='sudo apt full upgrade'
-source ~/.bashrc
-maj
```

#### 5) A quoi sert le paquet fortunes ? Installez-le.

* >`apt show fortunes`
```
Package: fortunes

Description: Fichiers de données contenant des biscuits chinois
 Il s’agit d’un programme affichant des épigrammes, connues sous le nom de
 cookies fortune, choisies aléatoirement à partir de fichiers fortune.
 .
 Il y a plus de 15 000 biscuits chinois différents dans ce paquet.
 .
 Vous aurez besoin du paquet fortune-mod pour afficher les biscuits
 chinois.
```
>`apt install fortunes`
#### 6) Quels paquets proposent de jouer au sudoku ?

* >`apt search sudoku`
```
ksudoku/disco 4:18.12.3-0ubuntu1 amd64
  Jeu et Solveur de Sudoku
```

#### 7) Lister les derniers paquets installés explicitement avec la commande apt install



## Exercice 2

#### A partir de quel paquet est installée la commande ls ? Comment obtenir cette information en une seule commande, pour n’importe quel programme (indice : la réponse est dans le poly de cours 2, dans la liste des commandes utiles) ? Utilisez la réponse à pour écrire un script appelé origine-commande (sans l’extension .sh) prenant en argument le nom d’une commande, et indiquant quel paquet l’a installée.

* >`which -a ls | xargs dpkg -S 2>>/dev/null`
```
coreutils: /bin/ls
```
>`>touch origine-commande`
>`>nano origine-commande`
```
#!/bin/bash

echo $(which -a $1 | xargs dpkg -S 2> /dev/null)
```

## Exercice 3

#### Ecrire une commande qui affiche “INSTALLÉ” ou “NON INSTALLÉ” selon le nom et le statut du package spécifié dans cette commande.

* >`nano installed-package`
```
#!/bin/bash

(dpkg -l $1 | grep "^ii" ) && echo "installé" || echo "non installé"
```
## Exercice 4

#### Lister les programmes livrés avec coreutils. A quoi sert la commande ’[’ et comment afficher ce qu’elle retourne ?

```
 Précisement, ce paquet comprend : arch base64 basename cat chcon chgrp
 chmod chown chroot cksum comm cp csplit cut date dd df dir dircolors
 dirname du echo env expand expr factor false flock fmt fold groups head
 hostid id install join link ln logname ls md5sum mkdir mkfifo mknod mktemp
 mv nice nl nohup nproc numfmt od paste pathchk pinky pr printenv printf
 ptx pwd readlink realpath rm rmdir runcon sha*sum seq shred sleep sort
 split stat stty sum sync tac tail tee test timeout touch tr true truncate
 tsort tty uname unexpand uniq unlink users vdir wc who whoami yes
```
La commande **'['** est equivalent a test