# Compte Rendu TP4 Asseel

## Exercice 1



### 1) Exercice 1. Gestion des utilisateurs et des groupes

#### Commencez par créer deux groupes groupe1 et groupe2

>sudo addgroup groupe1

``` 
Adding group 'groupe1' (GID 1001) ...
Done.
```

>sudo addgroup groupe2

``` 
Adding group 'groupe2' (GID 1001) ...
Done.
```

#### Créez ensuite 4 utilisateurs u1, u2, u3, u4 avec la commande useradd, en demandant la création de leur dossier personnel et avec bash pour shell

```
sudo useradd u1 -m --shell /bin/bash
sudo useradd u2 -m --shell /bin/bash
sudo useradd u3 -m --shell /bin/bash
sudo useradd u3 -m --shell /bin/bash
``` 

#### Placez les utilisateurs dans les groupes :
#### - u1, u2, u4 dans groupe1
#### - u2, u3, u4 dans groupe2

```
sudo usermod -a -G groupe1 u1
sudo usermod -a -G groupe1 u2
sudo usermod -a -G groupe1 u4

sudo usermod -a -G groupe2 u2
sudo usermod -a -G groupe2 u3
sudo usermod -a -G groupe2 u4
```

#### Donnez deux moyens d’afficher les membres de groupe2

>cat /etc/group | grep "groupe2"

```
groupe2:x:1002:u2,u3,u4
```

>sudo apt install members

>members groupe2

```
u2 u3 u4
```

#### Faites de groupe1 le groupe propriétaire de /home/u1 et /home/u2 et de groupe2 le groupe propriétaire de /home/u3 et /home/u4

```
sudo chgrp groupe1 /home/u1
sudo chgrp groupe1 /home/u2
sudo chgrp groupe2 /home/u3
sudo chgrp groupe2 /home/u4
```

#### Remplacez le groupe primaire des utilisateurs :
#### - groupe1 pour u1 et u2
#### - groupe2 pour u3 et u4

```
usermod u1 -g groupe1
usermod u2 -g groupe1

usermod u3 -g groupe2
usermod u4 -g groupe2
```

#### Créez deux répertoires /home/groupe1 et /home/groupe2 pour le contenu commun aux groupes, et mettez en place les permissions permettant aux membres de chaque groupe d’écrire dans le dossier associé.

```
sudo mkdir /home/groupe1
sudo mkdir /home/groupe2
sudo chgrp groupe1 /home/groupe1
sudo chgrp groupe2 /home/groupe2
sudo chmod 770 /home/groupe1
sudo chmod 770 /home/groupe2
```

#### Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer ou supprimer ce fichier ?

on peut faire cela grace au sticky bit : 

>chmod +t dossier

#### Pouvez-vous vous connecter en tant que u1 ? Pourquoi ?

Non car u1 n'est pas activé tant qu'il n'y a pas de mot de passe

#### Activez le compte de l’utilisateur u1 et vérifiez que vous pouvez désormais vous connecter avec son compte.

>sudo passwd u1

>su u1

On peut effectivement se connecter

#### Quels sont l’uid et le gid de u1 ?

>id u1

```
uid=1001(u1) gid=1001(groupe1) groups=1001(groupe1)
```

#### Quel utilisateur a pour uid 1003 ?

>id u3

```
uid=1003(u3) gid=1002(u3) groups=1002(groupe2)
```

#### Quel est l’id du groupe groupe1 ?

gid=1001(groupe1)

#### Quel groupe a pour guid 1002 ?

gid=1002(groupe2)

#### Retirez l’utilisateur u3 du groupe groupe2. Que se passe-t-il ? Expliquez.

>id u3

```
uid=1003(u3) gid=1002(groupe2) groups=1002(groupe2)
```

>sudo gpasswd -d u3 groupe2

```
Removing user u3 from group groupe2
gpasswd: user 'u3' is not a member of 'groupe2'
```

>id u3

```
uid=1003(u3) gid=1002(groupe2) groups=1005(u3)
```

On ne peut pas l'enlever du groupe car c'est le groupe primaire

#### Modifiez le compte de u4 de sorte que :
#### — il expire au 1er juin 2020
#### — il faut changer de mot de passe avant 90 jours
#### — il faut attendre 5 jours pour modifier un mot de passe
#### — l’utilisateur est averti 14 jours avant l’expiration de son mot de passe
#### — le compte sera bloqué 30 jours après expiration du mot de passe

```
sudo chage --mindays 90 u4
sudo chage --mindays 5 u4
sudo chage --maxdays 90 u4
sudo chage --inactive 30 u4
sudo chage --warndays 14 u4
sudo chage --expire 2020/06/01 u4
sudo chage -l u4

Last password change                                    : juin 01, 2019
Password expires                                        : août 30, 2019
Password inactive                                       : sept. 29, 2019
Account expires                                         : juin 01, 2020
Minimum number of days between password change          : 5
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 14
```

#### à quoi correspond l’utilisateur nobody ?

Dans la plupart des variantes d'Unix, nobody (personne en anglais) est le nom conventionnel d'un compte d'utilisateur à qui aucun fichier n'appartient, qui n'est dans aucun groupe qui a des privilèges et dont les seules possibilités sont celles que tous les "autres utilisateurs" ont.

#### Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ? Quelle commande permet de forcer sudo à oublier votre mot de passe ?

la commande sudo conserve le mot de passe pendant 15 minutes

la commande qui permert de forcer sudo a oublier le mot de passe est : 
>sudo -K

## Exercice 2
