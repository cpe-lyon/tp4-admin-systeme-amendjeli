# Compte Rendu TP4 Asseel

## Exercice 1  Gestion des utilisateurs et des groupes

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

## Exercice 2 Gestion des permissions

####  Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques lignes de texte. Quels sont les droits sur test et fichier ?

>stat -c %a test
```
775
```

Donc pour le proprietaire et son groupe, il y a les tout les droits et tout les droits sauf l'ecriture pour les autres

>stat -c %a test/fichier
```
664
```
Donc pour le proprietaire et son groupe, il y les droits de lecture et d'ecriture et seulement le droits de lecture pour les autres

#### Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en tant que root. Conclusion ?

>chmod ugo-rwx fichier$

il est alors impossible de lire ou ecrire dans le fichier

>sudo su

>cat fichier

```
hollow world
```

il est possible de passer a travers les restriction en root

#### Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un fichier s’il existe déjà. Que peut-on dire au sujet des droits ?

>chmod ugo+wx fichier

>"echo Hello" > fichier

il est possible d'ecrire mais impossible de lire sauf en root

#### Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez

>./fichier

```
bash: ./fichier: Permission denied
```

>sudo ./fichier

```
hello
```

Il est necessaire d'avoir les authorisation d'ecriture pour pouvoir executer un fichier

#### Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ? Rétablissez le droit en lecture sur test

>chmod ugo-r ../test

>ls

```
ls: cannot open directory '.': Permission denied
```

>./fichier

```
bash: ./fichier: Permission denied
```

Il est necessaire d'avoir les droits de lecture dans le doosier qui coontient pour executer fichier ou lire le contenu d'un dossier

#### Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. Que pouvezvous déduire de toutes ces manipulations ?

```
chmod ugo-r nouveau
chmod ugo-r ../test
echo "echo Hello" > nouveau
```

>-bash: nouveau: Permission denied

```
chmod ugo+r ../test
echo "echo Hello" > nouveau
```

>-bash: nouveau: Permission denied

```
rm nouveau
```

>rm: remove write-protected regular empty file 'nouveau'? y

on peut supprimer un un fichier si on peut ecrire dans le dossier qui le contient meme si on ne peut pas ecrire sur le fichier

#### Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test. Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?

>chmod ugo-x test

>touch test/essai

```
touch: cannot touch 'test/essai': Permission denied
```
>echo "echo Hello" > test/nouveau

```
-bash: test/nouveau: Permission denied
```

execution signifie que l'on ne peut pas acceder au fichier donc ni lire et ecrire dedans

####  Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd ..” ? Pouvez-vous donner une explication ?
