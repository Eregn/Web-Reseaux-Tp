# TP Réseau n°2
## I. Setup IP

>La première adresse Ip choisie est : 192.168.125.1/26  
>La seconde est : 192.168.125.2/26  

>Pour trouver les adresses Ip nous avons procéder comme suit :  

>ip : 192.168.125.1/26  

>1100 0000.1010 1000.0001 1100.0000 0001 ip  
>1111 1111.1111 1111.1111 1111.1100 0000 masque - > 255.255.255.192 -> 26  
                              
>1100 0000.1010 1000.0001 1100.0000 0000 -> 192.168.125.0/26 réseau  
>1100 0000.1010 1000.0001 1100.0011 1111 -> 192.168.125.63/26 bcast  

>Pour changer mon adresse ip, j'ai utilisé la commande : netsh interface ipv4 set address name ="Ethernet"  >static 192.168.125.2 255.255.255.192  

>ping :  

>C:\WINDOWS\system32>ping 192.168.125.1  

>Envoi d’une requête 'Ping'  192.168.125.1 avec 32 octets de données :  
>Réponse de 192.168.125.1 : octets=32 temps<1ms TTL=128  
>Réponse de 192.168.125.1 : octets=32 temps=1 ms TTL=128  
>Réponse de 192.168.125.1 : octets=32 temps<1ms TTL=128  
>Réponse de 192.168.125.1 : octets=32 temps=1 ms TTL=128  

>Statistiques Ping pour 192.168.125.1:  
>    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),  
>Durée approximative des boucles en millisecondes :  
>    Minimum = 0ms, Maximum = 1ms, Moyenne = 0ms  


>Sur Wireshark on peut observer 2 types de ping/pong :  
>- Echo request (type 8)  
>- Echo reply (type 0)  

## II. ARP my bro

>arp -a pour afficher la table arp  

>Interface : 192.168.125.2 --- 0x6  
>  Adresse Internet      Adresse physique      Type  
>  192.168.125.1         38-63-bb-b8-e5-7f     dynamique  

>  Sa mac est donc 38-63-bb-b8-e5-7f.  

>  Interface : 10.33.18.128 --- 0xf  
>  Adresse Internet      Adresse physique      Type  
>  10.33.19.254          00-c0-e7-e0-04-4e     dynamique  

>  La gateway du réseau Ynov est donc 10.33.19.254  
>  et sa mac est 00-c0-e7-e0-04-4e  

>  Pour supprimer la table arp, j'utilise la commande arp -d.  

#### Avant

>C:\WINDOWS\system32>arp -a  

>Interface : 10.101.1.1 --- 0x5  
>  Adresse Internet      Adresse physique      Type  
>  10.101.1.255          ff-ff-ff-ff-ff-ff     statique  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  224.0.0.251           01-00-5e-00-00-fb     statique  
>  224.0.0.252           01-00-5e-00-00-fc     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  

>Interface : 192.168.125.2 --- 0x6  
>  Adresse Internet      Adresse physique      Type  
>  192.168.125.1         38-63-bb-b8-e5-7f     dynamique  
>  192.168.125.63        ff-ff-ff-ff-ff-ff     statique  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  224.0.0.251           01-00-5e-00-00-fb     statique  
>  224.0.0.252           01-00-5e-00-00-fc     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  

>Interface : 10.33.18.128 --- 0xf  
>  Adresse Internet      Adresse physique      Type  
>  10.33.18.161          78-4f-43-87-f5-11     dynamique  
>  10.33.18.194          78-4f-43-87-f5-11     dynamique  
>  10.33.19.254          00-c0-e7-e0-04-4e     dynamique  
>  10.33.19.255          ff-ff-ff-ff-ff-ff     statique  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  224.0.0.251           01-00-5e-00-00-fb     statique  
>  224.0.0.252           01-00-5e-00-00-fc     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  
>  255.255.255.255       ff-ff-ff-ff-ff-ff     statique  

>Interface : 192.168.56.1 --- 0x13  
>  Adresse Internet      Adresse physique      Type  
>  192.168.56.255        ff-ff-ff-ff-ff-ff     statique  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  224.0.0.251           01-00-5e-00-00-fb     statique  
>  224.0.0.252           01-00-5e-00-00-fc     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  

#### Après

>  C:\WINDOWS\system32>arp -d  

>C:\WINDOWS\system32>arp -a  

>Interface : 10.101.1.1 --- 0x5  
>  Adresse Internet      Adresse physique      Type  
>  224.0.0.22            01-00-5e-00-00-16     statique  

>Interface : 192.168.125.2 --- 0x6  
>  Adresse Internet      Adresse physique      Type  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  

>Interface : 10.33.18.128 --- 0xf  
>  Adresse Internet      Adresse physique      Type  
>  10.33.19.254          00-c0-e7-e0-04-4e     dynamique  
>  224.0.0.22            01-00-5e-00-00-16     statique  

>Interface : 192.168.56.1 --- 0x13  
>  Adresse Internet      Adresse physique      Type  
>  224.0.0.22            01-00-5e-00-00-16     statique  

  #### Après le ping

>  Interface : 10.101.1.1 --- 0x5  
>  Adresse Internet      Adresse physique      Type  
>  10.101.1.255          ff-ff-ff-ff-ff-ff     statique  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  

>Interface : 192.168.125.2 --- 0x6  
>  Adresse Internet      Adresse physique      Type  
>  192.168.125.1         38-63-bb-b8-e5-7f     dynamique  
>  192.168.125.63        ff-ff-ff-ff-ff-ff     statique  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  

>Interface : 10.33.18.128 --- 0xf  
>  Adresse Internet      Adresse physique      Type  
>  10.33.19.254          00-c0-e7-e0-04-4e     dynamique  
>  10.33.19.255          ff-ff-ff-ff-ff-ff     statique  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  

>Interface : 192.168.56.1 --- 0x13  
>  Adresse Internet      Adresse physique      Type  
>  192.168.56.255        ff-ff-ff-ff-ff-ff     statique  
>  224.0.0.22            01-00-5e-00-00-16     statique  
>  239.255.255.250       01-00-5e-7f-ff-fa     statique  

>Pour la première trame, l'adresse source est LCFCHeFe_98:7d:cd et l'adresse de destination est Broadcast  
>Pour la seconde trame, l'adresse source est HewlettP_b8:e5:7f et l'adresse de destination est LCFCHeFe_98:7d:cd  	

>LCFCHeFe_98:7d:cd est mon PC et HewlettP_b8:e5:7f est le deuxieme PC.  