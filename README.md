# TP1 - Mise en jambes



![mignon ce chat](./picture/cattp.jpg)


**🌞 Affichez les infos des cartes réseau de votre PC**

Pour la carte WIFI nous avons une adresse MAC et une adresse IP
```
PS C:\Users\heroeselfric> ipconfig /all
[...]
Adresse physique . . . . . . . . . . . : 58-6C-25-81-2A-13
[...]
Adresse IPv4. . . . . . . . . . . . . .: 10.33.18.88(préféré)
[...]
```

En revanche pour la carte Ethernet nous avons une adresse MAC mais pas d'adresse IP
```
PS C:\Users\heroeselfric> ipconfig /all
[...]
Carte Ethernet Ethernet :
       Adresse physique . . . . . . . . . . . : 08-8F-C3-09-C8-CB

```

**🌞 Affichez votre gateway**
Pour coonaitre l'adresse Ip de la passerelle 
```
PS C:\Users\heroeselfric> ipconfig /all
[...]
Carte réseau sans fil Wi-Fi :
        Passerelle par défaut. . . . . . . . . : 10.33.19.254
```

pour connaitre l'adresse Ip de notre passerelle avec notre interface graphique, il faut Sélectionner Démarrer > Paramètres > réseau & internet > Wi-Fi, puis sélectionnez le Wi-Fi réseau à partir de qui vous êtes connecté.


**🌞 Trouvez comment afficher les informations sur une carte IP (change selon l'OS)**

pour afficher les infos d'une carte IP

```
PS C:\Users\heroeselfric> ipconfig /all

Carte réseau sans fil Wi-Fi :

   [...]
   Adresse physique . . . . . . . . . . . : 58-6C-25-81-2A-13
   [...]
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.18.88(préféré)
   [...]
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   [...]
```


- 🌞 à quoi sert la [gateway](../../cours/lexique/README.md#passerelle-ou-gateway) dans le réseau d'YNOV ?



🌞 Utilisez l'interface graphique de votre OS pour **changer d'adresse IP** :

en faisant le même chemin que pour trouver l'adresse de la gateway, Démarrer > Paramètres > réseau & internet > Wi-Fi, puis sélectionnez le reseaux wifi. on peut trouver un bouton pour Modifier son adresse IP, la valeur par defaut est "automatique (DHCP)" et nous on veut mettre notre adresse en manuel donc on choisi l'option manuel


🌞 **Il est possible que vous perdiez l'accès internet.**




# II. Exploration locale en duo


🌞Si vos PCs ont un port RJ45 alors y'a une carte réseau Ethernet associée :


```
PS C:\Users\heroeselfric> ipconfig /all

Carte Ethernet Ethernet :
[...]
   Adresse IPv4. . . . . . . . . . . . . .: 192.168.18.2(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.255.252
[...]
```
pour verifier la connection avec lautre ordinateur 
```
PS C:\Users\heroeselfric> ping 192.168.18.1

Envoi d’une requête 'Ping'  192.168.18.1 avec 32 octets de données :
Réponse de 192.168.18.1 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.18.1 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.18.1 : octets=32 temps=2 ms TTL=128
Réponse de 192.168.18.1 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 192.168.18.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 2ms, Moyenne = 1ms
```

- 🌞 pour tester la connectivité à internet on fait souvent des requêtes simples vers un serveur internet connu
  - encore une fois, un ping vers un DNS connu comme `1.1.1.1` ou `8.8.8.8` c'est parfait

```
PS C:\Users\heroeselfric> ping 192.168.137.1

[...]

Statistiques Ping pour 192.168.137.1:
    Paquets : envoyés = 2, reçus = 2, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 1ms, Moyenne = 1ms

PS C:\Users\heroeselfric> ping 8.8.8.8

[...]

Statistiques Ping pour 8.8.8.8:
    Paquets : envoyés = 2, reçus = 2, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 22ms, Maximum = 23ms, Moyenne = 22ms

PS C:\Users\heroeselfric> ping 1.1.1.1

[...]

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 2, reçus = 2, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 21ms, Maximum = 22ms, Moyenne = 21ms


PS C:\Users\heroeselfric> ping google.com

[...]

Statistiques Ping pour 216.58.215.46:
    Paquets : envoyés = 2, reçus = 2, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 20ms, Maximum = 20ms, Moyenne = 20ms
```


- 🌞 utiliser un `traceroute` ou `tracert` pour bien voir que les requêtes passent par la passerelle choisie (l'autre le PC)

```
PS C:\Users\heroeselfric> tracert 192.168.137.1

Détermination de l’itinéraire vers DESKTOP-LBR4JUU [192.168.137.1]
avec un maximum de 30 sauts :

  1     1 ms     1 ms     1 ms  DESKTOP-LBR4JUU [192.168.137.1]

Itinéraire déterminé.
```


- 🌞 **sur le PC *serveur*** avec par exemple l'IP 192.168.1.1
  - `nc.exe -l -p 8888`
    - "`netcat`, écoute sur le port numéro 8888 stp"
  - il se passe rien ? Normal, faut attendre qu'un client se connecte
- 🌞 **sur le PC *client*** avec par exemple l'IP 192.168.1.2
  - `nc.exe 192.168.1.1 8888`
    - "`netcat`, connecte toi au port 8888 de la machine 192.168.1.1 stp"
  - une fois fait, vous pouvez taper des messages dans les deux sens
- appelez-moi quand ça marche ! :)
  - si ça marche pas, essayez d'autres options de `netcat`

```
C:\Users\heroeselfric\Downloads\netcat-win32-1.11\netcat-1.11>nc.exe 192.168.137.1 8888
cucucuc
oasldg$
bjr
oh nice
on a un petit chat privée
JohnLee Sins
BigBlackClock
on a notre chat
```
![bitdefender c chiant](./picture/bitdefenderlol.jpg)

***cette partie là a été faite sur l'ordinateur de Arthur avec qui j'ai travaillé a cause justement de mon bitdefender ***

- 🌞 pour aller un peu plus loin
  - le serveur peut préciser sur quelle IP écouter, et ne pas répondre sur les autres
  - par exemple, on écoute sur l'interface Ethernet, mais pas sur la WiFI
  - pour ce faire `nc.exe -l -p PORT_NUMBER IP_ADDRESS`
  - par exemple `nc.exe -l -p 9999 192.168.1.37`
  - on peut aussi accepter uniquement les connexions internes à la machine en écoutant sur `127.0.0.1`

```
C:\Users\arthu\Downloads\netcat-win32-1.11\netcat-1.11>nc.exe -l -p 8888 -s 192.168.137.1

PS C:\Windows\system32> netstat -a -b -n  | select-string 8888

  TCP    192.168.137.1:8888     0.0.0.0:0              LISTENING


PS C:\Windows\system32> netstat -a -b -n  | select-string 127.0.0.1

  TCP    127.0.0.1:1491         127.0.0.1:27300        SYN_SENT
  TCP    127.0.0.1:6463         0.0.0.0:0              LISTENING
  TCP    127.0.0.1:8961         127.0.0.1:53635        ESTABLISHED
  TCP    127.0.0.1:14148        0.0.0.0:0              LISTENING
  TCP    127.0.0.1:15292        0.0.0.0:0              LISTENING
  TCP    127.0.0.1:15393        0.0.0.0:0              LISTENING
  TCP    127.0.0.1:16494        0.0.0.0:0              LISTENING
  TCP    127.0.0.1:45623        0.0.0.0:0              LISTENING
  TCP    127.0.0.1:53343        0.0.0.0:0              LISTENING
  TCP    127.0.0.1:53630        0.0.0.0:0              LISTENING
  TCP    127.0.0.1:53635        0.0.0.0:0              LISTENING
  TCP    127.0.0.1:53635        127.0.0.1:8961         ESTABLISHED
  TCP    127.0.0.1:53743        0.0.0.0:0              LISTENING
  UDP    127.0.0.1:1900         *:*
  UDP    127.0.0.1:50668        *:*
  UDP    127.0.0.1:50946        *:*
  UDP    127.0.0.1:60037        *:*

```
- 🌞 Autoriser les `ping`
  - configurer le firewall de votre OS pour accepter le `ping`
  - aidez vous d'internet
  - on rentrera dans l'explication dans un prochain cours mais sachez que `ping` envoie un message *ICMP de type 8* (demande d'ECHO) et reçoit un message *ICMP de type 0* (réponse d'écho) en retour

```
PS C:\Windows\system32> netsh advfirewall firewall add rule name="ICMP Allow incoming V4 echo request" protocol=icmpv4:8,any dir=out action=allow
Ok.


PS C:\Windows\system32> ping 192.168.137.2

Envoi d’une requête 'Ping'  192.168.137.2 avec 32 octets de données :
Réponse de 192.168.137.2 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.137.2 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.137.2 : octets=32 temps=1 ms TTL=128
Réponse de 192.168.137.2 : octets=32 temps=1 ms TTL=128


C:\Users\arthu\Downloads\netcat-win32-1.11\netcat-1.11>nc.exe -l -p 8888 -s 192.168.137.1

azdd
hello
hehehehehehhh
```
- 🌞 Autoriser le traffic sur le port qu'utilise `nc`
  - on parle bien d'ouverture de **port** TCP et/ou UDP
  - on ne parle **PAS** d'autoriser le programme `nc`
  - choisissez arbitrairement un port entre 1024 et 20000
  - vous utiliserez ce port pour [communiquer avec `netcat`](#5-petit-chat-privé-) par groupe de 2 toujours
  - le firewall du *PC serveur* devra avoir un firewall activé et un `netcat` qui fonctionne

***cette partie là a été faite sur l'ordinateur de Arthur avec qui j'ai travaillé a cause justement de mon bitdefender ***

# III. Manipulations d'autres outils/protocoles côté client

## 1. DHCP


🌞**Exploration du DHCP, depuis votre PC**

pour afficher l'adresse IP du serveur DHCP 

```
PS C:\Users\heroeselfric> ipconfig /all
Carte réseau sans fil Wi-Fi :
   [...]
   Serveur DHCP . . . . . . . . . . . . . : 10.33.19.254
```
et on peut trouver la date d'expiration de cette adresse quelque ligne au dessus :
```
   Bail obtenu. . . . . . . . . . . . . . : mercredi 28 septembre 2022 13:36:14
   Bail expirant. . . . . . . . . . . . . : jeudi 29 septembre 2022 13:36:14
```


- 🌞 trouver l'adresse IP du serveur DNS que connaît votre ordinateur

on peut y trouver aussi ladresse IP du serveur DNS que notre ordinateur connait grace a la meme commande 

```
PS C:\Users\heroeselfric> ipconfig /all
Carte réseau sans fil Wi-Fi :
   [...]
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
```

- 🌞 utiliser, en ligne de commande l'outil `nslookup` (Windows, MacOS) ou `dig` (GNU/Linux, MacOS) pour faire des requêtes DNS à la main

```
PS C:\Users\heroeselfric> nslookup google.com
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:808::200e
          216.58.215.46

PS C:\Users\heroeselfric> nslookup ynov.com
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    ynov.com
Addresses:  2606:4700:20::681a:be9
          2606:4700:20::681a:ae9
          2606:4700:20::ac43:4ae2
          104.26.10.233
          172.67.74.226
          104.26.11.233
```

- 🌞 utilisez le pour observer les trames qui circulent entre vos deux carte Ethernet. Mettez en évidence :