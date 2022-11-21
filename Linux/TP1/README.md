# TP1 : (re)Familiarisation avec un système GNU/Linux

## I. Utilisateurs

### 1. Création et configuration

🌞 **Ajouter un utilisateur à la machine**, qui sera dédié à son administration

- précisez des options sur la commande d'ajout pour que :
  - le répertoire home de l'utilisateur soit précisé explicitement, et se trouve dans `/home`
  - le shell de l'utilisateur soit `/bin/bash`
- prouvez que vous avez correctement créé cet utilisateur
  - et aussi qu'il a le bon shell et le bon homedir
    
    
🌞 **Créer un nouveau groupe `admins`** qui contiendra les utilisateurs de la machine ayant accès aux droits 
de `root` *via* la commande `sudo`.

Pour permettre à ce groupe d'accéder aux droits `root` :

- il faut modifier le fichier `/etc/sudoers`
- on ne le modifie jamais directement à la main car en cas d'erreur de syntaxe, on pourrait bloquer notre accès aux droits administrateur
- la commande `visudo` permet d'éditer le fichier, avec un check de syntaxe avant fermeture
- ajouter une ligne basique qui permet au groupe d'avoir tous les droits (inspirez vous de la ligne avec le groupe `wheel`)


🌞 **Ajouter votre utilisateur à ce groupe `admins`**

> Essayez d'effectuer une commande avec `sudo` peu importe laquelle, juste pour tester que vous avez le droit d'exécuter des commandes sous l'identité de `root`. Vous pouvez aussi utiliser `sudo -l` pour voir les droits `sudo` auquel votre utilisateur courant a accès.

---

1. Utilisateur créé et configuré

```
Verif de la creation de l'utilisateur node2-admin
sudo cat /etc/passwd | grep node2-admin
node2-admin:x:1001:1001::/home/node2-admin:/bin/bash

```
2. Groupe `admins` créé
   
```
Verif de la creation du groupe admins
sudo cat /etc/group | grep admins
admins:x:1002:

```

3. Groupe `admins` ajouté au fichier `/etc/sudoers`
   
```
Modif du fichier /etc/sudoers
sudo cat /etc/sudoers | grep admins
%admins ALL=(ALL)       ALL
```


4. Ajout de l'utilisateur au groupe `admins`

```
sudo usermod -aG admins node2-admin
```

### 2. SSH

Afin de se connecter à la machine de façon plus sécurisée, on va configurer un échange de clés SSH lorsque l'on se connecte à la machine.

🌞 **Pour cela...**

- il faut générer une clé sur le poste client de l'administrateur qui se connectera à distance (vous :) )
  - génération de clé depuis VOTRE poste donc
  - avec la commande `ssh-keygen` (avec des options)
- déposer la clé dans le fichier `/home/<USER>/.ssh/authorized_keys` de la machine que l'on souhaite administrer
  - vous utiliserez l'utilisateur que vous avez créé dans la partie précédente du TP
  - on peut le faire à la main (voir le cours SSH)
  - ou avec la commande `ssh-copy-id` (dispo uniquement dans `git bash` si vous êtes sous Windows)

🌞 **Assurez vous que la connexion SSH est fonctionnelle**, sans avoir besoin de mot de passe.

```
Generer une clef
    ssh-keygen -t rsa -b 4096   
Deposer la cle dans le fichier de la machine node2
    ssh-copy-id sklord@10.101.1.11

ssh sklord@10.101.1.11
Activate the web console with: systemctl enable --now cockpit.socket
Last login: Mon Nov 14 18:16:24 2022 from 10.101.1.1
[sklord@localhost ~]$ exit
logout
Connection to 10.101.1.11 closed.

```

## II. Partitionnement

### 1. Préparation de la VM

⚠️ **Uniquement sur `node1.tp1.b2`.**

Ajout de deux disques durs à la machine virtuelle, de 3Go chacun.

### 2. Partitionnement

⚠️ **Uniquement sur `node1.tp1.b2`.**

🌞 **Utilisez LVM** pour...

- agréger les deux disques en un seul *volume group*
- créer 3 *logical volumes* de 1 Go chacun
- formater ces partitions en `ext4`
- monter ces partitions pour qu'elles soient accessibles aux points de montage `/mnt/part1`, `/mnt/part2` et `/mnt/part3`.

🌞 **Grâce au fichier `/etc/fstab`**, faites en sorte que cette partition soit montée automatiquement 
au démarrage du système.

```
 sudo pvs
  PV         VG   Fmt  Attr PSize  PFree
  /dev/sda2  rl   lvm2 a--  <7.00g       0
  /dev/sdb   data lvm2 a--  <3.00g   <2.00g
  /dev/sdc   data lvm2 a--  <3.00g 1020.00m
  
 sudo vgs
  VG   #PV #LV #SN Attr   VSize  VFree
  data   2   3   0 wz--n-  5.99g 2.99g
  rl     1   2   0 wz--n- <7.00g    0
  
 sudo lvs
  LV   VG   Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv_1 data -wi-ao----   1.00g
  lv_2 data -wi-ao----   1.00g
  lv_3 data -wi-ao----   1.00g
  
  df -h
   Filesystem             Size  Used Avail Use% Mounted on
   /dev/mapper/data-lv_1  976M  2.6M  907M   1% /mnt/data1
   /dev/mapper/data-lv_3  976M  2.6M  907M   1% /mnt/data3
   /dev/mapper/data-lv_2  976M  2.6M  907M   1% /mnt/data2

```

✨**Bonus** : amusez vous avez les options de montage. Quelques options intéressantes :

- `noexec`
- `ro`
- `user`
- `nosuid`
- `nodev`
- `protect`

## III. Gestion de services

Au sein des systèmes GNU/Linux les plus utilisés, c'est *systemd* qui est utilisé comme gestionnaire de services (entre autres).

Pour manipuler les services entretenus par *systemd*, on utilise la commande `systemctl`.

On peut lister les unités `systemd` actives de la machine `systemctl list-units -t service`.

**Référez-vous au mémo pour voir les autres commandes `systemctl` usuelles.**

## 1. Interaction avec un service existant

⚠️ **Uniquement sur `node1.tp1.b2`.**

Parmi les services système déjà installés sur Rocky, il existe `firewalld`. Cet utilitaire est l'outil de firewalling de Rocky.

🌞 **Assurez-vous que...**

- l'unité est démarrée
- l'unitée est activée (elle se lance automatiquement au démarrage)



## 2. Création de service

### A. Unité simpliste

⚠️ **Uniquement sur `node1.tp1.b2`.**

🌞 **Créer un fichier qui définit une unité de service** 

- le fichier `web.service`
- dans le répertoire `/etc/systemd/system`

Déposer le contenu suivant :

```
[Unit]
Description=Very simple web service

[Service]
ExecStart=/usr/bin/python3 -m http.server 8888

[Install]
WantedBy=multi-user.target
```

Le but de cette unité est de lancer un serveur web sur le port 8888 de la machine. **N'oubliez pas d'ouvrir ce port dans le firewall.**

Une fois l'unité de service créée, il faut demander à *systemd* de relire les fichiers de configuration :

```bash
$ sudo systemctl daemon-reload
```

Enfin, on peut interagir avec notre unité :

```bash
$ sudo systemctl status web
$ sudo systemctl start web
$ sudo systemctl enable web
```

🌞 **Une fois le service démarré, assurez-vous que pouvez accéder au serveur web**

- avec un navigateur depuis votre PC
- ou la commande `curl` depuis l'autre machine (je veux ça dans le compte-rendu :3)
- sur l'IP de la VM, port 8888

```
@ curl 10.101.1.11:8888
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Directory listing for /</title>
</head>
<body>
<h1>Directory listing for /</h1>
<hr>
<ul>
<li><a href="bin/">bin@</a></li>
<li><a href="boot/">boot/</a></li>
<li><a href="dev/">dev/</a></li>
<li><a href="etc/">etc/</a></li>
<li><a href="home/">home/</a></li>
<li><a href="lib/">lib@</a></li>
<li><a href="lib64/">lib64@</a></li>
<li><a href="media/">media/</a></li>
<li><a href="mnt/">mnt/</a></li>
<li><a href="opt/">opt/</a></li>
<li><a href="proc/">proc/</a></li>
<li><a href="root/">root/</a></li>
<li><a href="run/">run/</a></li>
<li><a href="sbin/">sbin@</a></li>
<li><a href="srv/">srv/</a></li>
<li><a href="sys/">sys/</a></li>
<li><a href="tmp/">tmp/</a></li>
<li><a href="usr/">usr/</a></li>
<li><a href="var/">var/</a></li>
</ul>
<hr>
</body>
</html>

```

### B. Modification de l'unité

🌞 **Préparez l'environnement pour exécuter le mini serveur web Python**

- créer un utilisateur `web`
- créer un dossier `/var/www/meow/`
- créer un fichier dans le dossier `/var/www/meow/` (peu importe son nom ou son contenu, c'est pour tester)
- montrez à l'aide d'une commande les permissions positionnées sur le dossier et son contenu

```
ls -al /var/www/meow
total 4
drwxr-xr-x. 2 root root 19 Nov 14 23:46 .
drwxr-xr-x. 3 root root 18 Nov 14 23:40 ..
-rw-r--r--. 1 root root 50 Nov 14 23:46 quote

```

> Pour que tout fonctionne correctement, il faudra veiller à ce que le dossier et le fichier appartiennent à 
> l'utilisateur `web` et qu'il ait des droits suffisants dessus.

🌞 **Modifiez l'unité de service `web.service` créée précédemment en ajoutant les clauses**

- `User=` afin de lancer le serveur avec l'utilisateur `web` dédié
- `WorkingDirectory=` afin de lancer le serveur depuis le dossier créé au dessus : `/var/www/meow/`
- ces deux clauses sont à positionner dans la section `[Service]` de votre unité

🌞 **Vérifiez le bon fonctionnement avec une commande `curl`**

```
curl 10.101.1.11:8888
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Directory listing for /</title>
</head>
<body>
<h1>Directory listing for /</h1>
<hr>
<ul>
<li><a href="quote">quote</a></li>
</ul>
<hr>
</body>
</html>

```