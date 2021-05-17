---
title: "TP utilisation de Linux"
author: [Olivier LASNE]
date: "2020-11-23"
subject: "Markdown"
keywords: [Linux, bash, ligne de commande, shell, TCP, netcat, nc]
subtitle: "Introduction à Linux et la ligne de commande"
lang: "fr"
titlepage: true
...

# TP utilisation de Linux

## Introduction

Le but de ce TP est de faire un rappel sur l'utilisation courante de Linux et la ligne de commande.

## Les bases de Linux

On va ici apprendre à se servir de l'interface en **ligne de commande**, aussi appelée **Shell**.
**Afin de retenir, il est nécessaire de taper les commandes. Et non les copier-coller***

Une fois connecté, ouvrir l'application Terminal.

Vous devriez avoir un shell avec les élément suivants :

![shell](./images/shell.png)

Le premier **kali** indique le nom de l'utilisateur. Celui après le @ le nom de la machine.
Entre crochet le dossier courant est indiqué. **~** symbolise le dossier utilisateur.
Finalement **$** correspond à un utilisateur standard. Ce symbole devient **#** pour l'utilisateur **root** (administrateur sur les systèmes Linux).

### Entrer une commande

On peut exécuter en commande simple telle que **date**
```
$ date
Tue 24 Nov 2020 01:23:25 PM EST
```
Cette dernière va afficher l'heure, ici en anglais car c'est la langue configurée sur ma machine.

## L'arboresence des fichiers sous Linux
Les fichiers sous Linux forme une arboresence (des dossiers dans des dossiers). Un peu comme sous Windows.

* **/** est le dossier au sommet.
* Les dossiers sont séparés par des **/** contrairement à Windows qui utilise les **\\**.
* Ici ```/home/kali``` signife donc que nous sommes dans le dossier **kali**, contenu dans le dossier **home** qui est lui même au **à la racine** aka **/** .

![Système de fichier Linux](./images/linux_filesysteme.png)

Dans l'exemple ci-dessus. Les utilisateurs existants sont jono, marko et cory. Sur votre machine vous avez seulement un utilisateur **kali**.


## Commandes pour se déplacer et manipuler les fichiers

### Se déplacer

Pour voir le dossier courant, vous pouvez utiliser la commande **pwd**
```sh
$ pwd   
/home/kali
```

Elle indique que vous vous trouvez dans le dossier **/home/kali**.

### Lister le contenu du répertoire
Pour lister les fichiers, vous pouvez utiliser la commande **ls**.
```sh
$ ls                
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```

### Se déplacer
Pour se déplacer on utilise la commande **cd**.

**cd** sans paramètre vous rammènera dans votre dossier utilisateur.
```sh
kali@kali:/etc$ cd
kali@kali:~$
```

On peut utiliser **cd** avec des chemins absolus ou relatifs :
Absolu :
```sh
kali@kali:~$ cd /etc/
kali@kali:/etc$
```

Relatif :
```sh
kali@kali:~/test$ cd ../../etc
kali@kali:/home/etc$
```

### Voir et éditer un fichier

Pour voir un fichier, on peut utiliser **cat** ou **less**.
```sh
$ cat /etc/hostname
kali
```

L'éditeur **nano** peut être utiliser pour éditer un fichier texte.\
Pour quitter : Ctrl-X. Pour sauvegarder Ctrl-O.

**Exercice** : créer un fichier texte dans votre dossier utilisateur avec la commande **nano**.
**Exercice 2** : afficher un fichier avec less

## Créer un fichier
Pour créer un fichier sans l'éditer, vous pouvez utiliser la commande **touch**
```sh
$ ls
test1.txt test2.txt

$ touch test3.txt
$ ls
test1.txt test2.txt test3.txt
```

### Créer un dossier
La commande **mkdir** permet de créer un dossier
```sh
$ mkdir tp_cyber
$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  tp_cyber  Videos
```
### Copier et déplacer des fichiers

Pour copier un fichier, on utilise la commande **cp**.\ 
Pour copier un dossier on utilise **cp -r**, cela copie tout le contenu du dossier.

```sh
$ ls
fichier1

$ cp fichier1 fichier2

$ ls
fichier1 fichier2
```

On utilise la commande **mv** pour déplacer des fichier. 
```sh
$ ls
dossier1  dossier2

$ ls dossier1 dossier2
dossier1:
fichier

dossier2:

$ mv dossier1/fichier dossier2/
$ ls dossier1 dossier2
dossier1:

dossier2:
fichier
```

Elle peut également être utiliser pour renommer un fichier `mv boring_file awesome_file`.


### Supprimer un fichier ou un dossier

La suppresion d'un fichier se fait avec la commande **rm**.
On utilise l'option **-r** pour supprimer un dossier.

Dans l'exemple ci-dessous *tp* est un dossier qui contient *test3.txt*.
```sh
$ ls
test1.txt test2.txt tp
$ ls tp
test3.txt

$ rm test1.txt
$ ls
test2.txt tp

$ rm -r tp
$ ls
test2.txt
```

Vous connaissez maintenant les commandes pour manipuler les fichiers.

\newpage

## Administration
### Obtenir les droits admin
Afin d'exécuter une commande avec les droits admin. Il faut la précéder de **sudo**. Et entrer le mot de passe utilisateur lorsqu'il est demandé.
**/!\\ Le shell n'affiche rien alors que l'on tape le mot de passe.**
```sh
$ cat /etc/shadow
cat: /etc/shadow: Permission denied

$ sudo cat /etc/shadow
[sudo] password for kali:
root:!:18583:0:99999:7:::
daemon:*:18583:0:99999:7:::
bin:*:18583:0:99999:7:::
...
```

### Installer des logiciels

Les distributions Linux ont un système de *dépots*, qui sont l'équivalent d'un App Store. On parle de *paquet* pour désigner les applications / logciels de ces dépots.

On va tout d'abord mettre à jour les informations sur les paquets avec la commande **apt update**.
```sh
$ sudo apt update
```
Sous Kali, l'installation de logiciels s'effectue avec la commande **apt**. On peut l'utiliser rechercher des logicels avec **apt search**.
```sh
$ apt search cmatrix
En train de trier... Fait
Recherche en texte intégral... Fait
cmatrix/bionic,now 1.2a-5build3 amd64
imitation de « Matrix » pour l’affichage
```
Ici la ligne 4 nous indique que le paquet *cmatrix* est disponible.

On peut ensuite utiliser la commande **apt install** pour installer un logiciel. (Ne pas oublier **sudo** devant).
```sh
$ sudo apt install cmatrix
...
Paquets suggérés :
  cmatrix-xfont
Les NOUVEAUX paquets suivants seront installés :
  cmatrix
0 mis à jour, 1 nouvellement installés, 0 à enlever et 137 non mis à jour.
```
Ici les lignes 5, 6 et 7 nous indiquent que le paquet cmatrix a été installé.

Vous pouvez lancer **cmatrix** tout simplement en tapant `cmatrix` dans le terminal. Pour tuer un programme en cours, utiliser le raccourci **Ctrl + C**.

**Exercice :** utiliser la page de **manuel** (commande man), pour lancer cmatrix en gras et en bleu.


Un logiciel peut être désinstaller avec la commande **apt remove**: 
```sh
sudo apt remove cmatrix
```

\newpage


## Options et aide

On peut passer des paramètres à un programme. C'est ce que l'on fait lorsque l'on donne l'option `-r` à **mv** ou **cp**.

### Les paramètres courts
Les paramètres les plus courants sont constitués d'une seule lettre précédée d'un tiret. Par exemple :
```sh
commande -d
```

Si on doit donner plusieurs paramètres, on peut faire comme ceci :
```sh
commande -d -a -U -h
```
Ou plus court
```sh
commande -daUh
```

Exemple :
```sh
$ ls -l          
total 36
drwxr-xr-x 2 kali kali 4096 Nov 17 07:33 Desktop
drwxr-xr-x 2 kali kali 4096 Nov 17 07:33 Documents
drwxr-xr-x 2 kali kali 4096 Nov 25 11:23 Downloads
drwxr-xr-x 2 kali kali 4096 Nov 17 07:33 Music
drwxr-xr-x 2 kali kali 4096 Nov 17 07:33 Pictures
drwxr-xr-x 2 kali kali 4096 Nov 17 07:33 Public
drwxr-xr-x 2 kali kali 4096 Nov 17 07:33 Templates
drwxr-xr-x 3 kali kali 4096 Nov 24 16:57 tp
drwxr-xr-x 2 kali kali 4096 Nov 17 07:33 Video
```

### Les paramètres longs
Les paramètres constitués de plusieurs lettres sont précédés de deux tirets, comme ceci :
```sh
commande --parametre1 --parametre2
```
Cette fois, pas le choix : si vous voulez mettre plusieurs paramètres longs, il faudra ajouter un espace entre chacun d'eux.

On peut aussi combiner les paramètres longs et les paramètres courts dans une commande :
```sh
commande -daUh --autreparametre
```

Parfois deux écritures sont possible pour un paramètre. Pour **ls** ```--all``` est synonyme de ```-a```.

### Aide et manuel
Pour savoir quelles options passer à une commande, vous pouvez utiliser l'aide de la commande ou les pages de manuel.


Sur pratiquement toutes les commandes. On peut utiliser ```-h``` ou ```--help``` pour obtenir de l'aide.\

On peut également accéder à **page de manuel** d'une commande avec **```man commande```**.\
Vous pouvez quitter avec la touche **q**. Et chercher du texte en tapant **/ma recherche**.


**Exercice:** À l'aide de la page de manuel, exécuter le programme **cmatrix** en gras (bold) et de couleur bleu.

\newpage

## Pro tips
### Autocomplétion

Écrire **``da``** puis appuyer deux fois sur **Tab**.
```sh
$ da
dash     date     dave     davtest
```
Cela liste les commandes qui commencent par ```da```.

Si l'on ajoute un ```t``` pour avoir ```dat``` puis que l'on appuye sur **Tab** la commande va être complétée automatiquement.

### Historique
Vous pouvez utiliser les touches **flèche du haut** et **flèche du bas** pour parcourir l'historique des commandes.

Il est également possible d'utiliser **Ctrl + R** puis d'écrire le début d'une commande pour la chercher dans l'historique.\

Appuyer à nouveau sur **Ctrl + R** pour remonter dans l'historique.

On peut utiliser la commande ```history``` pour voir les commandes écrites précédement.

### Raccourcis

```Ctrl + L``` : efface le contenu de la console. Équivalent à la commande ```clear```.\
```Ctrl + D``` : quitte le terminal. Équivalent à la commande ```exit```.\

```Shift + PgUp``` : remonter dans les messages. La molette de la souris peut être utilisée en mode graphique.\
```Shift + PgDown``` : pareil mais pour descendre.\

```Ctrl + W``` : efface le mot à gauche du curseur.\
```Ctrl + A``` : place le curseur tout à gauche.\
```Ctrl + K``` : efface tout ce qui se trouve à droite du curseur.\

```Ctrl + C``` : tuer le processus en cours.

\newpage

## Redirection et pipe

On peut donner la sortie d'un commande en  à une autre avec **|** (AltGr + 6).
On va utiliser **echo**, **cowsay** et **lolcat** pour illuster ça.

Il faut d'abord les installer cowsay et lolcat :
```sh
$ sudo apt install cowsay lolcat
```

La commande echo va simplement afficher le texte qu'on lui donne en paramètre.
```sh
$ echo aaaa bbbb cccc
aaaa bbbb cccc

```

On peut utiliser un pipe avec le caractère **|** pour passer la sortie de echo au programme cowsay.
```
$ echo bonjour  | cowsay
 _________
< bonjour >
 ---------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

De même on peut passer la sortie de la commande **cowsay** à **lolcat**. 
```sh
echo bonjour | cowsay | lolcat
```

**Exercice :**

1. Avec l'aide de la **page de manuel** de cowsay, et la commande `cowsay -l`. Remplacer la vache par un stegosaurus.
2. Donnez une couleur arc-en-ciel au stegosaurus en utilisant les **pipes** et `lolcat`.
3. Installer le programme **`fortune`** avec `apt`. Une fois installé vous pouvez le lancer simplement avec la commande `fortune`.
4. Faites dire une pharse aléatoire au stegosaurus en utilisant le progamme **fortune**.

\newpage

## Redirection fréquentes
### Grep

**grep** est une commande qui permet de chercher du texte.\
Ci-dessous un exemple avec le fichier */etc/passwd* qui liste les utilisateurs.
```sh
$ cat /etc/passwd
root
daemon
...

$ cat /etc/passwd | grep kali
kali:x:1000:1000:Kali,,,:/home/kali:/usr/bin/zsh
```
Il peut également être utiliser de la façon `grep motif fichier` .
```sh
$ grep kali /etc/passwd
kali:x:1000:1000:Kali,,,:/home/kali:/usr/bin/zsh
```

#### Options de grep

Les options suivantes sont courament utiliées avec `grep`

* `-v` recherche inverse : affiche les lignes qui ne correspondent pas au texte
* `-i` recherche non sensible à la case
* `--color` ex `--color=always` pour avoir une sortie en couleur
* `-r` faire un recherche récursive dans le dossier courant

### Cut
On peut utiliser la commande **cut** pour selectionner du texte séparer par un délimiteur.

**-d** va permettre de définir le *délimiteur*. **-f** va selectionner quel élément on prend.

```sh
$ echo "texte-avec-des-tirets" | cut -d '-' -f 1
texte

$ echo "texte-avec-des-tirets" | cut -d '-' -f 4
tirets
```

**Exercice** : Faire le apparaitre le shell **/usr/bin/zsh** de l'utilisateur **kali** depuis le fichier **/etc/passwd** en utilisant `grep`, `cut` et les *pipes*.

## sort
La commande **sort** permet de trier des données.

Par exemple imaginons un fichier de noms
```sh
$ cat prénoms
david
martin
antoine
david
```

On peut trier cette liste de prénoms avec la commande **sort**.
```sh
$ cat prénoms | sort
antoine
david
david
martin
```

On peut utiliser la commande **uniq** pour supprimer les doublons.
```sh
$ cat prénoms | sort | uniq
antoine
david
martin
```

Ou simplement l'option **-u** de `sort`.
```sh
$ cat prénoms | sort -u
antoine
david
martin
```

## head et tail

On peut utiliser les commandes **head** et **tail** pour voir le début ou la fin d'un fichier.

```sh
$ head /etc/group           
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
```

```sh
$ tail /etc/group
saned:x:134:
sambashare:x:135:
inetsim:x:136:
colord:x:137:
geoclue:x:138:
lightdm:x:139:
kpadmins:x:140:
kali:x:1000:
kaboxer:x:141:kali,root
systemd-coredump:x:999:
```

**Exercice**: utiliser les commandes vues précédement pour voir les 5 premiers éléments par ordre alphabétique de */etc/group*. Sans les `:` ni ce qui suit.\
La sortie doit être la suivante
```
adm
audio
avahi
backup
bin
```

\newpage

## Rédiriger depuis/vers un fichier

On peut rediriger la sorite d'une commande vers un fichier en utilisant le symbole **>**.

```sh
$ echo bonjour > test.txt

$ cat test.txt
bonjour
```

On peut utiliser deux chevrons pour ajouter du texte à la fin du document **>>**.
```sh
$ echo 'au revoir' >> test.txt

$ cat test.txt
bonjour
au revoir
```



On peut également lire depuis un fichier avec le symbole **<**.
```
$ cowsay < test.txt               
 _________
< bonjour >
 ---------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

Pour stocker le résultat d'une commande et voir la sortie, on peut utiliser la commande `tee`

```sh
$ cat /etc/passwd | tee ~/passwd

root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
[...]
```

## Netcat


Netcat est un peu le couteau suisse du protocole TCP/IP.

La syntaxe est la suivante : `nc ip port`.

On peut par exemple récupérer la bannière (identifiant de version) de SSH sur Metasploitable avec la commande suivante.
```
$ nc 192.168.56.101 22
SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1

Protocol mismatch.
^C
```

Il est possible de créer une connexion en écoute avec l'option **-l**. Par exemple pour écouter sur le __port 4444__.
```
$ nc -lvp 4444
Listening on [0.0.0.0] (family 0, port 4444)
```

Vous pouvez vous connecter à un port en utilisant la syntaxe

    nc adresse_ip numéro_de_port

Exemple :

    nc 127.0.0.1 4444

**Exercice :**

1. Trouver votre adresse IP avec `ifconfig` ou `ip -a`
2. Ouvrir un port en écoute avec netcat
3. Communiquer avec votre propre machine
4. Communiquer avec un autre stagiaire

| Binôme 1   | Binôme 2 |
|------------|----------|
| Romain     | Quentin  |
| Clément    | Christophe |
| Geoffroy   | Pascal   |
| Jean-Marc  | Clément  |

### Transférer des données avec netcat

Netcat est un utilitaire fiable, et on peut utiliser les __pipes__ et redirections pour transferer des fichiers à travers __netcat__.

Prennons en exemple le fichier hosts.

`/etc/hosts`

    127.0.0.1	localhost
    127.0.1.1	metasploitable.localdomain	metasploitable
    
    # The following lines are desirable for IPv6 capable hosts
    ::1     ip6-localhost ip6-loopback
    fe00::0 ip6-localnet



On peut tranferer un fichier d'une machine à l'autre de la façon suivante.

**Exemple :**

_Machine réceptrice_

```bash
nc -lvp 4444 > hostname
```

_Machine émétrice_

```bash
cat /etc/hostname | nc 192.168.1.101 4444
```

Il est nécessaire d'adapter les IP et les ports par celle de vos machine.


**Exercice :**

Les commandes vues ci-dessus fonctionnent sur des fichiers binaires. Utiliser `netcat` pour transférer le programme `/bin/ls` d'une machine à l'autre.

Néanmoins vous pouvez vérifier que le programme n'a pas été altéré pendant la transmission avec la commande `sha256sum`.

```bash
$ sha256sum ls
8c0d752022269a8673dc38ef5d548c991bc7913a43fe3f1d4d076052d6a5f0b6  ls
```

**Astuce :**

On peut utiliser l'option `-q 0` de netcat pour terminer automatiquement le transfert sur un fichier de taille importante.

Si le résultat est le même avec le fichier original, et le fichier transmis, c'est qu'ils sont parfaitement identiques.