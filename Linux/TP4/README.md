# TP4 : Conteneurs

# I. Docker

## 1. Install

🌞 **Installer Docker sur la machine**

- en suivant [la doc officielle](https://docs.docker.com/engine/install/)
- démarrer le service `docker` avec une commande `systemctl`
- ajouter votre utilisateur au groupe `docker`
  - cela permet d'utiliser Docker sans avoir besoin de l'identité de `root`
  - avec la commande : `sudo usermod -aG docker $(whoami)`
  - déconnectez-vous puis relancez une session pour que le changement prenne effet
    
```
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo systemctl start docker
sudo systemctl is-active docker
active
sudo docker run hello-world

sudo usermod -aG docker karl
sudo logout

```

## 2. Vérifier l'install

➜ **Vérifiez que Docker est actif est disponible en essayant quelques commandes usuelles :**

```bash
# Info sur l'install actuelle de Docker
$ docker info

# Liste des conteneurs actifs
$ docker ps
# Liste de tous les conteneurs
$ docker ps -a

# Liste des images disponibles localement
$ docker images

# Lancer un conteneur debian
$ docker run debian

# -d sert à mettre un conteneur en tâche de fond (-d pour daemon)
$ docker run -d debian sleep 99999

# à l'inverse, -it sert à avoir un shell interactif (incompatible avec -d)
$ docker run -it debian bash

# Consulter les logs d'un conteneur
$ docker ps # on repère l'ID/le nom du conteneur voulu
$ docker logs <ID_OR_NAME>
$ docker logs -f <ID_OR_NAME> # suit l'arrivée des logs en temps réel

# Exécuter un processus dans un conteneur actif
$ docker ps # on repère l'ID/le nom du conteneur voulu
$ docker exec <ID_OR_NAME> <COMMAND>
$ docker exec <ID_OR_NAME> ls
$ docker exec -it <ID_OR_NAME> bash # permet de récupérer un shell bash dans le conteneur ciblé

# supprimer un conteneur donné
$ docker rm <ID_OR_NAME>
# supprimer un conteneur donné, même s'il est allumé
$ docker rm -f <ID_OR_NAME>
```

➜ **Explorer un peu le help**, si c'est pas le man :

```bash
$ docker --help
$ docker run --help
$ man docker
```

```
docker run -d --name web  -v /home/myconf.conf:/etc/nginx/conf.d/myconf.conf -v /home/myindex.html:/var/www/tp4/index.html 
-m="1g" --cpus="1.0" -p 7777:9999 nginx

```

## 3. Lancement de conteneurs

La commande pour lancer des conteneurs est `docker run`.

Certaines options sont très souvent utilisées :

```bash
# L'option --name permet de définir un nom pour le conteneur
$ docker run --name web nginx

# L'option -d permet de lancer un conteneur en tâche de fond
$ docker run --name web -d nginx

# L'option -v permet de partager un dossier/un fichier entre l'hôte et le conteneur
$ docker run --name web -d -v /path/to/html:/usr/share/nginx/html nginx

# L'option -p permet de partager un port entre l'hôte et le conteneur
$ docker run --name web -d -v /path/to/html:/usr/share/nginx/html -p 8888:80 nginx
# Dans l'exemple ci-dessus, le port 8888 de l'hôte est partagé vers le port 80 du conteneur
```

🌞 **Utiliser la commande `docker run`**

- lancer un conteneur `nginx`
  - l'app NGINX doit avoir un fichier de conf personnalisé
  - l'app NGINX doit servir un fichier `index.html` personnalisé
  - l'application doit être joignable grâce à un partage de ports
  - vous limiterez l'utilisation de la RAM et du CPU de ce conteneur
  - le conteneur devra avoir un nom

Petit rappel de fonctionnement sur l'application NGINX : 

- le fichier de conf par défaut se trouve dans `/etc/nginx/nginx.conf`
- si vous ouvrez ce fichier, vous constaterez qu'il inclut tout ce qu'il y a dans `/etc/nginx/conf.d`
  - pour que les fichiers de ce dossier soient inclus, ils doivent porter l'extension `.conf`
- il "suffit" donc 
  - de créer un fichier de conf NGINX sur l'hôte
    - il porte l'extension `.conf`
    - il comporte une conf minimale pour écouter sur un port et servir un site dans un dossier précis
  - grâce à une option `-v ...` sur le `docker run`
    - de poser votre fichier de conf dans `/etc/nginx/conf.d/`
- un fichier de conf NGINX minimal pour faire ça est aussi simple que :

```nginx
server {
  # on définit le port où NGINX écoute dans le conteneur
  listen 9999;
  
  # on définit le chemin vers la racine web
  # dans ce dossier doit se trouver un fichier index.html
  root /var/www/tp4; 
}

```


# II. Images

La construction d'image avec Docker est basée sur l'utilisation de fichiers `Dockerfile`.

L'idée est la suivante :

- vous créez un dossier de travail
- vous vous déplacez dans ce dossier de travail
- vous créez un fichier `Dockerfile`
  - il contient les instructions pour construire une image
  - `FROM` : indique l'image de base
  - `RUN` : indique des opérations à effectuer dans l'image de base
- vous exécutez une commande `docker build . -t <IMAGE_NAME>`
- une image est produite, visible avec la commande `docker images`

## Exemple de Dockerfile et utilisation

Exemple d'un Dockerfile qui :

- se base sur une image ubuntu
- la met à jour
- installe nginx

> La commande `nginx -g "daemon off;"` permet de lancer NGINX au premier-plan, et ainsi demande à notre conteneur d'exécuter le programme NGINX à son lancement.

Plutôt que de préciser à la main à chaque `docker run` quelle commande doit lancer le conteneur (notre `nginx -g "daemon off;"` en fin de ligne ici), on peut, au moment du `build` de l'image, choisir d'indiquer que chaque conteneur lancé à partir de cette image lancera une commande donneé.

```
cd home/DockerFile
sudo nano Dockerfile

docker build . -t nginx_images
docker run -d --name test -p 6666:80 nginx_images
curl 10.104.1.11:8888

```

## 2. Construisez votre propre Dockerfile

🌞 **Construire votre propre image**

- image de base (celle que vous voulez : debian, alpine, ubuntu, etc.)
  - une image du Docker Hub
  - qui ne porte aucune application par défaut
- vous ajouterez
  - mise à jour du système
  - installation de Apache (pour les systèmes debian, le serveur Web apache s'appelle `apache2` et non pas `httpd` comme sur Rocky)
  - page d'accueil Apache HTML personnalisée

➜ Pour vous aider, voilà un fichier de conf minimal pour Apache (à positionner dans `/etc/apache2/apache2.conf`) :

```
docker build . -t second_nginx_img
docker run -d --name test -p 6666:80 second_nginx_img

```

📁 [**`Dockerfile`**](./Dockerfile)

# III. `docker-compose`

## 1. Intro

➜ **Installer `docker-compose` sur la machine**

```
sudo dnf install docker-compose-plugin
```
## 2. Make your own meow

🌞 **Conteneurisez votre application**

- créer un `Dockerfile` maison qui porte l'application
- créer un `docker-compose.yml` qui permet de lancer votre application
- vous préciserez dans le rendu les instructions pour lancer l'application
  - indiquer la commande `git clone`
  - le `cd` dans le bon dossier
  - la commande `docker build` pour build l'image
  - la commande `docker-compose` pour lancer le(s) conteneur(s)

```
mkdir /home/DockerFile/app ** cd /home/DockerFile/app
sudo chmod 777 /home/DockerFile/app #Give permission to clone a git repo
git clone  https://github.com/Eregn/CurrencyConvertor.git
docker run --rm -v $(pwd):/app composer install
ls
Dockerfile dokcer-compose.yml CurrencyConvertor 
docker build . -t laravel
docker compose up

```

📁 [**`app/Dockerfile`**](./app/Dockerfile)

📁 [**`app/docker-compose.yml`**](./app/docker-compose.yml)
