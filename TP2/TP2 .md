#TP2: Ethernet, IP, et ARP
##I. Setup IP
>La premiere adresse IP choisie est: 192.168.125.1/26  
>La seconde est: 192.168.125.2/26  
>1100 0000.1010 1000.0001 1100.0000 0001 ip  
>1111 1111.1111 1111.1111 1111.1100 0000 masque - > 255.255.255.192 -> 26  
>1100 0000.1010 1000.0001 1100.0000 0000 -> 192.168.125.0/26 réseau  
>1100 0000.1010 1000.0001 1100.0011 1111 -> 192.168.125.63/26 bcast  
>Changer d'adresse IP==> `netsh interface ipv4 set address name ="Ethernet" static 192.168.125.2 255.255.192`  
> Ping: C:\WINDOWS\system32>ping 192.168.125.1  
> Envoi d’une requête 'Ping'  192.168.125.1 avec 32 octets de données :  
Réponse de 192.168.125.1 : octets=32 temps<1ms TTL=128  
Réponse de 192.168.125.1 : octets=32 temps=1 ms TTL=128  
Réponse de 192.168.125.1 : octets=32 temps<1ms TTL=128  
Réponse de 192.168.125.1 : octets=32 temps=1 ms TTL=128  
> Statistiques Ping pour 192.168.125.1:
Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
Minimum = 0ms, Maximum = 1ms, Moyenne = 0ms
##III.DHCP you too my brooo
>L'IP a utiliser se trouve dans la tram Offer: 192.168.58.92   
>On peut trouver l'adresse de la passerelle reseau dans la tram Offer et Ack sous l'option Router: 192.168.58.255  
>On peut trouver l'adresse d'un serveur DNS joignable sous l'option Domain Name Server: 192.168.58.196
>Voir screenshot des 4 trames DHCP sur wireshark
##IV.Avant-gout TCP et UDP
>Connexion a une IP de type IPV6 et sur le source port 443 et destination port 49644
