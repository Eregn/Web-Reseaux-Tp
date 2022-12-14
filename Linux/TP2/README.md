# TP2 : Gestion de service

# I. Un premier serveur web
## 1. Installation

🖥️ **VM web.tp2.linux**

| Machine         | IP            | Service     |
|-----------------|---------------|-------------|
| `web.tp2.linux` | `10.102.1.11` | Serveur Web |

🌞 **Installer le serveur Apache**

- paquet `httpd`
- la conf se trouve dans `/etc/httpd/`
  - le fichier de conf principal est `/etc/httpd/conf/httpd.conf`
  - je vous conseille **vivement** de virer tous les commentaire du fichier, à défaut de les lire, vous y verrez plus clair
    - avec `vim` vous pouvez tout virer avec `:g/^ *#.*/d`

> Ce que j'entends au-dessus par "fichier de conf principal" c'est que c'est **LE SEUL** fichier de conf lu par Apache quand il démarre. C'est souvent comme ça : un service ne lit qu'un unique fichier de conf pour démarrer. Cherchez pas, on va toujours au plus simple. Un seul fichier, c'est simple.  
**En revanche** ce serait le bordel si on mettait toute la conf dans un seul fichier pour pas mal de services.  
Donc, le principe, c'est que ce "fichier de conf principal" définit généralement deux choses. D'une part la conf globale. D'autre part, il inclut d'autres fichiers de confs plus spécifiques.  
On a le meilleur des deux mondes : simplicité (un seul fichier lu au démarrage) et la propreté (éclater la conf dans plusieurs fichiers).

🌞 **Démarrer le service Apache**

- le service s'appelle `httpd` (raccourci pour `httpd.service` en réalité)
  - démarrez le
  - faites en sorte qu'Apache démarre automatique au démarrage de la machine
  - ouvrez le port firewall nécessaire
    - utiliser une commande `ss` pour savoir sur quel port tourne actuellement Apache
    - une portion du mémo commandes est dédiée à `ss`
    
```
    Line de command: ss -alnpt
    State       Recv-Q      Send-Q           Local Address:Port           Peer Address:Port     Process
    LISTEN      0           128                    0.0.0.0:22                  0.0.0.0:*
    LISTEN      0           511                          *:80                        *:*
    LISTEN      0           128                       [::]:22                   [::]:*
    
```
                   


🌞 **TEST**

- vérifier que le service est démarré
- vérifier qu'il est configuré pour démarrer automatiquement
- vérifier avec une commande `curl localhost` que vous joignez votre serveur web localement
- vérifier avec votre navigateur (sur votre PC) que vous accéder à votre serveur web


## 2. Avancer vers la maîtrise du service

🌞 **Le service Apache...**

- affichez le contenu du fichier `httpd.service` qui contient la définition du service Apache
```
This page is used to test the proper operation of an HTTP server after it has been installed on a Rocky Linux system. If you can read this page, it means that the software it working correctly.

Just visiting?
This website you are visiting is either experiencing problems or could be going through maintenance.

If you would like the let the administrators of this website know that you've seen this page instead of the page you've expected, you should send them an email. In general, mail sent to the name "webmaster" and directed to the website's domain should reach the appropriate person.

The most common email address to send to is: "webmaster@example.com"

Note:
The Rocky Linux distribution is a stable and reproduceable platform based on the sources of Red Hat Enterprise Linux (RHEL). With this in mind, please understand that:

Neither the Rocky Linux Project nor the Rocky Enterprise Software Foundation have anything to do with this website or its content.
The Rocky Linux Project nor the RESF have "hacked" this webserver: This test page is included with the distribution.
For more information about Rocky Linux, please visit the Rocky Linux website.

I am the admin, what do I do?
You may now add content to the webroot directory for your software.

For systems using the Apache Webserver: You can add content to the directory /var/www/html/. Until you do so, 
people visiting your website will see this page. If you would like this page to not be shown, follow the instructions in: /etc/httpd/conf.d/welcome.conf.

For systems using Nginx: You can add your content in a location of your choice and edit the root configuration 
directive in /etc/nginx/nginx.conf.

```

🌞 **Déterminer sous quel utilisateur tourne le processus Apache**

- mettez en évidence la ligne dans le fichier de conf principal d'Apache (`httpd.conf`) qui définit quel user est utilisé
  
```
User alpache

```
- utilisez la commande `ps -ef` pour visualiser les processus en cours d'exécution et confirmer que apache 
  tourne bien sous l'utilisateur mentionné dans le fichier de conf
  
```
apache       676     659  0 13:01 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache       678     659  0 13:01 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache       679     659  0 13:01 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache       681     659  0 13:01 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND

```
- la page d'accueil d'Apache se trouve dans `/usr/share/testpage/`
  - vérifiez avec un `ls -al` que tout son contenu est **accessible en lecture** à l'utilisateur mentionné 
    dans le fichier de conf

🌞 **Changer l'utilisateur utilisé par Apache**

- créez un nouvel utilisateur
  - pour les options de création, inspirez-vous de l'utilisateur Apache existant
    - le fichier `/etc/passwd` contient les informations relatives aux utilisateurs existants sur la machine
    - servez-vous en pour voir la config actuelle de l'utilisateur Apache par défaut
- modifiez la configuration d'Apache pour qu'il utilise ce nouvel utilisateur
- redémarrez Apache
- utilisez une commande `ps` pour vérifier que le changement a pris effet

```
sklord:x:1002:1002::/usr/share/httpd:/sbin/nologin

sklord      2082    2081  0 13:13 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
sklord      2083    2081  0 13:13 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
sklord      2084    2081  0 13:13 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
sklord      2085    2081  0 13:13 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND

```

🌞 **Faites en sorte que Apache tourne sur un autre port**

- modifiez la configuration d'Apache pour lui demander d'écouter sur un autre port de votre choix
  
```
 Status: "Started, listening on: port 420"
 
```
- ouvrez ce nouveau port dans le firewall, et fermez l'ancien
- redémarrez Apache
- prouvez avec une commande `ss` que Apache tourne bien sur le nouveau port choisi
  
```
Command line: ss -alnpt
LISTEN      0           511                          *:420                       *:*

```
- vérifiez avec `curl` en local que vous pouvez joindre Apache sur le nouveau port
- vérifiez avec votre navigateur que vous pouvez joindre le serveur sur le nouveau port


# II. Une stack web plus avancée

⚠⚠⚠ **Réinitialiser votre conf Apache avant de continuer** ⚠⚠⚠  
En particulier :

- reprendre le port par défaut
- reprendre l'utilisateur par défaut

## 1. Intro blabla

**Le serveur web `web.tp2.linux` sera le serveur qui accueillera les clients.** C'est sur son IP que les clients devront aller pour visiter le site web.  

**Le service de base de données `db.tp2.linux` sera uniquement accessible depuis `web.tp2.linux`.** Les clients ne pourront pas y accéder. Le serveur de base de données stocke les infos nécessaires au serveur web, pour le bon fonctionnement du site web.

---

Bon le but de ce TP est juste de s'exercer à faire tourner des services, un serveur + sa base de données, c'est un peu le cas d'école. J'ai pas envie d'aller deep dans la conf de l'un ou de l'autre avec vous pour le moment, on va se contenter d'une conf minimale.

Je vais pas vous demander de coder une application, et cette fois on se contentera pas d'un simple `index.html` tout moche et on va se mettre dans la peau de l'admin qui se retrouve avec une application à faire tourner. **On va faire tourner un [NextCloud](https://nextcloud.com/).**

En plus c'est utile comme truc : c'est un p'tit serveur pour héberger ses fichiers via une WebUI, style Google Drive. Mais on l'héberge nous-mêmes :)

---

Le flow va être le suivant :

➜ **on prépare d'abord la base de données**, avant de setup NextCloud

- comme ça il aura plus qu'à s'y connecter
- ce sera sur une nouvelle machine `db.tp2.linux`
- il faudra installer le service de base de données, puis lancer le service
- on pourra alors créer, au sein du service de base de données, le nécessaire pour NextCloud

➜ **ensuite on met en place NextCloud**

- on réutilise la machine précédente avec Apache déjà installé, ce sera toujours Apache qui accueillera les requêtes des clients
- mais plutôt que de retourner une bête page HTML, NextCloud traitera la requête
- NextCloud, c'est codé en PHP, il faudra donc **installer une version de PHP précise** sur la machine
- on va donc : install PHP, configurer Apache, récupérer un `.zip` de NextCloud, et l'extraire au bon endroit !

## 2. Setup

🖥️ **VM db.tp2.linux**

**N'oubliez pas de dérouler la [📝**checklist**📝](#checklist).**

| Machines        | IP            | Service                 |
|-----------------|---------------|-------------------------|
| `web.tp2.linux` | `10.102.1.11` | Serveur Web             |
| `db.tp2.linux`  | `10.102.1.12` | Serveur Base de Données |

### A. Base de données

🌞 **Install de MariaDB sur `db.tp2.linux`**

- déroulez [la doc d'install de Rocky](https://docs.rockylinux.org/guides/database/database_mariadb-server/)
- je veux dans le rendu **toutes** les commandes réalisées
- vous repérerez le port utilisé par MariaDB avec une commande `ss` exécutée sur `db.tp2.linux`
  - il sera nécessaire de l'ouvrir dans le firewall


🌞 **Préparation de la base pour NextCloud**

- une fois en place, il va falloir préparer une base de données pour NextCloud :
  - connectez-vous à la base de données à l'aide de la commande `sudo mysql -u root -p`
  - exécutez les commandes SQL suivantes :

```sql
-- Création d'un utilisateur dans la base, avec un mot de passe
-- L'adresse IP correspond à l'adresse IP depuis laquelle viendra les connexions. Cela permet de restreindre les IPs autorisées à se connecter.
-- Dans notre cas, c'est l'IP de web.tp2.linux
-- "pewpewpew" c'est le mot de passe hehe
CREATE USER 'nextcloud'@'10.102.1.11' IDENTIFIED BY 'pewpewpew';

-- Création de la base de donnée qui sera utilisée par NextCloud
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

-- On donne tous les droits à l'utilisateur nextcloud sur toutes les tables de la base qu'on vient de créer
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'10.102.1.11';

-- Actualisation des privilèges
FLUSH PRIVILEGES;

-- C'est assez générique comme opération, on crée une base, on crée un user, on donne les droits au user sur la base
```

> Par défaut, vous avez le droit de vous connecter localement à la base si vous êtes `root`. C'est pour ça que `sudo mysql -u root` fonctionne, sans nous demander de mot de passe. Evidemment, n'importe quelles autres conditions ne permettent pas une connexion aussi facile à la base.

🌞 **Exploration de la base de données**

- afin de tester le bon fonctionnement de la base de données, vous allez essayer de vous connecter, comme NextCloud le fera :
  - depuis la machine `web.tp2.linux` vers l'IP de `db.tp2.linux`
  - utilisez la commande `mysql` pour vous connecter à une base de données depuis la ligne de commande
    - par exemple `mysql -u <USER> -h <IP_DATABASE> -p`
    - si vous ne l'avez pas, installez-là
    - vous pouvez déterminer dans quel paquet est disponible la commande `mysql` en saisissant `dnf provides mysql`
- **donc vous devez effectuer une commande `mysql` sur `web.tp2.linux`**
- une fois connecté à la base, utilisez les commandes SQL fournies ci-dessous pour explorer la base

```sql
SHOW DATABASES;
USE <DATABASE_NAME>;
SHOW TABLES;

```

🌞 **Trouver une commande SQL qui permet de lister tous les utilisateurs de la base de données**

- vous ne pourrez pas utiliser l'utilisateur `nextcloud` de la base pour effectuer cette opération : il n'a pas les droits
- il faudra donc vous reconnectez localement à la base en utilisant l'utilisateur `root`

> Les utilisateurs de la base de données sont différents des utilisateurs du système Rocky Linux qui porte la base. 
> Les utilisateurs de la base définissent des identifiants utilisés pour se connecter à la base afin d'y voir ou d'y modifier des données.

Une fois qu'on s'est assurés qu'on peut se co au service de base de données depuis `web.tp2.linux`, on peut continuer.
```
SELECT user FROM mysql.db WHERE db = 'nextcloud';
+-----------+
| user      |
+-----------+
| nextcloud |
+-----------+

```

### B. Serveur Web et NextCloud

⚠️⚠️⚠️ **N'OUBLIEZ PAS de réinitialiser votre conf Apache avant de continuer. En particulier, remettez le port et le user par défaut.**

🌞 **Install de PHP**

```bash
# On ajoute le dépôt CRB
$ sudo dnf config-manager --set-enabled crb
# On ajoute le dépôt REMI
$ sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm -y

# On liste les versions de PHP dispos, au passage on va pouvoir accepter les clés du dépôt REMI
$ dnf module list php

# On active le dépôt REMI pour récupérer une version spécifique de PHP, celle recommandée par la doc de NextCloud
$ sudo dnf module enable php:remi-8.1 -y

# Eeeet enfin, on installe la bonne version de PHP : 8.1
$ sudo dnf install -y php81-php
```

🌞 **Install de tous les modules PHP nécessaires pour NextCloud**

```bash
# eeeeet euuuh boom. Là non plus j'ai pas pondu ça, c'est la doc :
$ sudo dnf install -y libxml2 openssl php81-php php81-php-ctype php81-php-curl php81-php-gd php81-php-iconv php81-php-json php81-php-libxml php81-php-mbstring php81-php-openssl php81-php-posix php81-php-session php81-php-xml php81-php-zip php81-php-zlib php81-php-pdo php81-php-mysqlnd php81-php-intl php81-php-bcmath php81-php-gmp
```

🌞 **Récupérer NextCloud**

- créez le dossier `/var/www/tp2_nextcloud/`
  - ce sera notre *racine web* (ou *webroot*)
  - l'endroit où le site est stocké quoi, on y trouvera un `index.html` et un tas d'autres marde, tout ce qui constitue NextClo :D
- récupérer le fichier suivant avec une commande `curl` ou `wget` : https://download.nextcloud.com/server/prereleases/nextcloud-25.0.0rc3.zip
- extrayez tout son contenu dans le dossier `/var/www/tp2_nextcloud/` en utilisant la commande `unzip`
  - installez la commande `unzip` si nécessaire
  - vous pouvez extraire puis déplacer ensuite, vous prenez pas la tête
  - contrôlez que le fichier `/var/www/tp2_nextcloud/index.html` existe pour vérifier que tout est en place
- assurez-vous que le dossier `/var/www/tp2_nextcloud/` et tout son contenu appartient à l'utilisateur qui exécute le service Apache

```
ls -al /var/www/tp2_nextcloud/
sudo chown -R apache:apache /var/www/tp2_nextcloud/

```

> A chaque fois que vous faites ce genre de trucs, assurez-vous que c'est bien ok. Par exemple, vérifiez avec un `ls -al` que tout appartient bien à l'utilisateur qui exécute Apache.

🌞 **Adapter la configuration d'Apache**

- regardez la dernière ligne du fichier de conf d'Apache pour constater qu'il existe une ligne qui inclut d'autres fichiers de conf
- créez en conséquence un fichier de configuration qui porte un nom clair et qui contient la configuration suivante :

```apache
<VirtualHost *:80>
  # on indique le chemin de notre webroot
  DocumentRoot /var/www/tp2_nextcloud/
  # on précise le nom que saisissent les clients pour accéder au service
  ServerName  web.tp2.linux

  # on définit des règles d'accès sur notre webroot
  <Directory /var/www/tp2_nextcloud/> 
    Require all granted
    AllowOverride All
    Options FollowSymLinks MultiViews
    <IfModule mod_dav.c>
      Dav off
    </IfModule>
  </Directory>
</VirtualHost>
```

```
sudo nano /etc/httpd/conf.d/nextcloud.conf
sudo systemctl restart httpd

```
🌞 **Redémarrer le service Apache** pour qu'il prenne en compte le nouveau fichier de conf

### C. Finaliser l'installation de NextCloud

➜ **Sur votre PC**

- modifiez votre fichier `hosts` (oui, celui de votre PC, de votre hôte)
  - pour pouvoir joindre l'IP de la VM en utilisant le nom `web.tp2.linux`
- avec un navigateur, visitez NextCloud à l'URL `http://web.tp2.linux`
  - c'est possible grâce à la modification de votre fichier `hosts`
- on va vous demander un utilisateur et un mot de passe pour créer un compte admin
  - ne saisissez rien pour le moment
- cliquez sur "Storage & Database" juste en dessous
  - choisissez "MySQL/MariaDB"
  - saisissez les informations pour que NextCloud puisse se connecter avec votre base
- saisissez l'identifiant et le mot de passe admin que vous voulez, et validez l'installation

🌴 **C'est chez vous ici**, baladez vous un peu sur l'interface de NextCloud, faites le tour du propriétaire :)

🌞 **Exploration de la base de données**

- connectez vous en ligne de commande à la base de données après l'installation terminée
- déterminer combien de tables ont été crées par NextCloud lors de la finalisation de l'installation
  - ***bonus points*** si la réponse à cette question est automatiquement donnée par une requête SQL
  
```
sudo mysql -u nextcloud -h 10.102.1.12 -p
> mysql:
  USE nextcloud;
  SHOW TABLE
  SELECT COUNT(*) as NUMBEROFTABLES
    -> FROM INFORMATION_SCHEMA.TABLES
    -> WHERE TABLE_SCHEMA = 'nextcloud';
    
+----------------+
| NUMBEROFTABLES |
+----------------+
|             95 |
+----------------+

```