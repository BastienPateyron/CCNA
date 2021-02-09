# C I S C O

# Classes d’adresses réseau privées et publiques
A : 0.0.0.0 	126.255.255.255	/ 8  
B : 128.0.0.0 	191.255.255.255	/16  
C : 192.0.0.0 	223.255.255.255	/ 24  

# Classes réseau privées
A : 10.0.0.0 	10.255.255.255		/ 8  
B : 172.16.0.0 	172.31.255.255		/12  
C : 192.168.0.0 	192.168.255.255	/ 16  

# Connexion au routeur par série
1.	Se brancher avec le câble série au port « COM » marqué en bleu  
2.	Lancer « minicom » dans un terminal 	(-s si bug pour le configurer)  
      1.	-s : /dev modem -> /dev/tty/S0 ou S1  
      2.	Vitesse: 9600 8N1  

# Configuration de la machine
Ifconfig  eth0  192.0.0.2 netmask 255.255.255.0  
route  add default gw 192.0.0.1  eth0  

# Cisco IOS
s ip int b :	alias pour show ip interface brief  
show ip interface brief : affiche un récapitulatif concis du statut et de la configuration IP.  
show ip route : affiche la table de routage IP entière.  
show ip route connected : affiche la liste des réseaux connectés directement qui sont actifs.  
show running-config : affiche la configuration en cours.  
show running-config | section line vty :   
show running-config | include up :   
traceroute : trace la route vers la destination  
copy running-config startup-config : copie dans la config de démarrage  

# Attribuer une IP au Switch
enable : débute la configuration  
configure terminal : débute la configuration via terminal  
(config)  interface vlan 1 : sélectionne l’interface du commutateur à configurer  
(config-if) ip address  192.168.10.2 2   255.255.255.0 : Affecte une ip et un mask à l’interface  
(config-if)  (no) shutdown: éteins l’interface ou bien l’allume si on utilise no en option.  
(config-if) exit : sors de la configuration de l’interface  
(config)  ip default-gateway 192.168.10.1: définit l’IP de la passerelle par défaut du device  

# Supprimer l’IP d’un switch
(config) interface vlan 1	: sélectionne le vlan 1  
(config-if) no ip address : supprime l’IP d’accès à ce VLAN. (Attention à ne pas être en ssh)  

# Configuration basique d’un routeur
Nommer un périphérique  
> enable : élévation de privilèges  
configure terminal ou conf t: entre en mode configuration  
hostname malaria  


# Configurer une interface
(config-if) int fa0/0  			
(config-if) ip addr 192.168.15.10  255.255.255.0  
(config-if) no shutdown  

## Activer IPV6 sur un routeur
(config) ipv6 unicast-routing
(config) int FastEthernet 0/0
(config-if) ipv6 address 2001:0BB9:AABB:1234::Suffixe_souhaité/64

## Activer IPv6 sur un poste client
Il faut juste passer en allocation automatique

## Pinger en ipv6
ping 2001:1:2::1        // IP d'un routeur ici par exemple


# Configurer une sous-interface
(config-if) int fa0/0.15			// Crée une sous interface N°15  
(config-subif) encapsulation dot1Q  10	// Définit le type d’encapsulation du vlan  
(config-subif) ip addr 192.168.15.254  255.255.255.0  
(config-subif) no shutdown  

# Sécuriser les accès de gestion
Sécurisation des modes (dans l’ordre) :  
 - Exécution privilégiée avec le password « class »  
 - Exécution utilisateur avec le password « cisco »  
 - Exécution à distance avec le password « cisco »  
 - Chiffre tous les mots de passe qui ne le sont pas encore (les nouveaux entre autres)   
 

**Optionnel**  
(config) description Link to R2  
(config) banner motd $ Vous ne passerez pas ! $ : Bannière d’échec de login, « $ » est un délimiteur  
(config) interface loopback 0 : Configuration d’une IP de boucle locale  
(config-if) ip addr 127.0.0.1  255.255.255.0  

# Configurer l’horloge
show controller serial 1/3 : Affiche si on est en DTE ou en DCE  
(config-if) clock rate 64000 : Configuration de la clock rate côté DCE  
 
# Routage
## Routage statique  
(config) ip route 172.16.1.0  255.255.255.0  172.16.2.1  
## Route par défaut  
(config) ip route 0.0.0.0   0.0.0.0   172.16.2.1   


# RIP
## Création d’une route RIP  
(config) router rip : active le routage en mode RIP  
(config-router) version 2 : met le RIP en version 2 (gère aussi les sous réseaux)  
(config-router) no auto-summary : Supprime les résumés de route (pour qu’un /24 n’écrase pas un /25)  
(config-router)  network 192.168.10.0  

## Suppression d’une route RIP  
(config) router rip  
(config-router)no network 192.168.10.0  

# OSPF
(config) router ospf 100					// 100 : process id ~ correspond au nom  
(config-router) network 145.4.0.0  0.0.0.255 area 0	// Ajout d’une route en zone 0

## Zones OSPF
Il faut utiliser la commande précédente.  
'area 0' correspond à la zone principale dite 'backbone ?'.  
Toutes les autres zones ('area 1', 'area 2', ...) doivent y être rattachées.  

## Redistribution inter-protocol  
Il faut le configurer sur les deux interfaces du routeur. Côté RIP et côté OSPF  
Si on ne précise pas « metric » côté RIP, la valeur par défaut est 16 ce qui correspond à 0 sauts  

## OSPF vers RIP  
(config) router ospf 100  				
(config-router) redistribute  rip			// [subnets] rajoute la gestion des sous-réseaux  

## RIP vers OSPF  
(config) router rip  
(config-router) redistribute ospf 100 metric 1 	// metric [1-16], 15 = 1 saut, 14 = 2 sauts, 1 = 15 sauts  

# BGP
// TODO  
Déclarer les reseaux qui nous appartiennent dans l’AS  
network @réseau mask_si_nécessaire  
neighbor @IP mask_si_nécessaire  

# EIGRP
Générique
```
(config) router eigrp [as number]
(config-router) network [my network address]
```

Exemple
```
router eigrp 100
network 15.0.0.0
```

Vérification:
```
# show ip protocols
``` 

# Mirroring  
Redirection du traffic vu par une interface vers une autre interface  

(config) monitor session 1 source interface fa 0/1  
(config) monitor session 1 destination interface fa0/2  


# Vlan
## Configuration d’un Vlan  
show vlan : montre les vlans du device

(config) vlan 10,20 : crée les Vlans 10 et 20  
(config-vlan) name étudiants : nomme le vlan « étudiants »  
(config) interface range fastEthernet 0/1 – 10 : sélectionne les interfaces 1 à 10  
(config-if-range) switchport mode access : active les accès par VLAN  
(config-if-range) switchport access vlan 10 : les ports permettent l’accès au vlan 10  
(config-if)  no switchport access vlan 2			// Supprimer un VLAN  

## Trunk  
(config) interface FastEthernet 0/0	  
(config-if) switchport mode trunk			// Pour communiquer entre vlan  
(config-if) switchport trunk native vlan 99		// Si pas de tag VLAN, tag en 99  
(config-if) switchport trunk allowed vlan 10, 20, 99	// Laisse passer les VLAN spécifés  
(config-if) switchport trunk allowed vlan all		// Variante:  passer tous les VLAN  

## VTP   
Le mode client interdit la création de nouveaux VLANS  

### Client choix 1						Client choix 2  
(config) vtp mode client					# vlan database

### Serveur						(vlan) vtp client  
(config) vtp mode server
(config) vtp domain CCNA				// C’est mieux de le mettre  
(config) vtp password cisco1234				// Optionnel  


## Configuration sous interface routeur  
(conf) interface FastEthernet 0/0  
(conf-if) no shutdown			// Active l’interface mère  
(conf) interface FastEthernet 0/0.10	// Configure une sous interface N° 10  
(conf-subif) encapsulation dot1Q 10 	// Encapsule en dot1Q le Vlan 10  
(cong-subif) ip addr 192.168.1.1		// affecte l’ip à la sous interface  
(config-subif) no shutdown  


# NAT / PAT
PAT = NAT dynamique  
show ip nat statistics					   // liste les NAT du device

# Configurer un PAT  
(config) interface VLAN 1				// Sur le 811, on utilise le VLAN1  
(config-if) ip addr 192.168.4.1  255.255.255.0		// IP du VLAN  
(config-if) ip nat inside  				
(config-if) no shutdown  
(config) interface FastEthernet 4			// FastEthernet 4 car c’est le port WAN  
(config-if) ip addr 10.0.2.1  255.255.255.0		// IP de l’interface externe  
(config-if) ip nat outside  				
(config-if) access-list 1 permit 10.0.2.0  0.0.0.255	// Masque avec bits inversés  
(config-if) no shutdown  
(config) ip nat inside source list 1 interface  FastEthernet 4  overload  
 
# DHCP
PAT = NAT dynamique  
```
show ip dhcp binding						// liste les NAT du device
A faire sur le routeur  
(config) interface FastEthernet 0				// On donne une ip à l’interface  
(config-if) ip addr 192.168.4.1  255.255.255.0  		
(config) ip dhcp pool CLIENT_LAN				// Crée un pool d’IP « client_lan »  
(dhcp-config) network 192.168.3.0 255.255.255.0		// Spécifie le réseau du DHCP  
(dhcp-config) dns-server 8.8.8.8				// DNS par défaut, au hasard  
(dhcp-config) default-router 192.168.3.1			// On met l’IP du routeur actuel  
```

Si on connecte le DHCP à un switch, si l’attribution ne marche pas parmi les machines, il   faut peut-être activer la range des interfaces du switch  
## Réinitialiser un appareil  
erase startup-config  
delete flash :vlan.dat  	// Pour Switchs uniquement  
reload  

# Configurer un accès SSH
Définir une IP pour l’interface  
``` java
(config) enable secret monPassword        // Définit le mot de passe quand on enable  
(config) hostname nomDuDevice  
(config) ip domain-name monHote.zz.fr     // Configure un nom de domaine ?  
(config) username login secret passwd     // Crée un user avec un password chiffré (en 5)  
(config) crypto key generate rsa          // Génère les clés pour le SSH (1024 ou 2048)  
(config) service password-encryption      // Chiffre les mots de passe. Utilie que si on utilise  « password »  
(config) line vty  0  15                  // Configure des lignes 0 à 15 pour que ssh puisse les modifier ?  
(config-line) transport input ssh         // Autorise les modifications via SSH  
(config-line) login local                 // Rend l’authentification obligatoire et requiert un compte local  
```

## Exemple
``` java
conf t
enable secret cisco
hostname Router4	
ip domain-name router4.zz.fr
username admin secret cisco
crypto key generate rsa
2048 	
line vty  0  15		
transport input ssh	
login local
exit
exit
```	

## Connexion SSH
``` bash
ssh -l admin 192.168.2.1
```

## Connexion FTP
``` bash
ftp 192.168.2.1
cisco
cisco
```

# HSRP
La priorité la plus élevée est le chemin privilégié  
L'IP virtuelle est une IP qui correspond au réseau commun entre les deux routeurs  
**L'IP virtuelle deviens la nouvelle Ip du gateway qu'il faudra donner aux machines hôtes**  

## Générique
```
standby [n° groupe] ip [@ip de la passerelle virtuelle cible]     // Déclare la passerelle virtuelle pour le groupe donné      
standby [n° groupe] priority [valeur] preempt                     // Déclare la priorité de notre routeur pour le groupe et le met en routeur de secours
```

## Exemple
```
configure terminal
show standby
interface gigabitethernet 0/0
standby 100 ip 10.0.0.253
standby 100 priority 10
standby preempt
end
```


# Ether channel
Se fait sur un **Switch**  
Il faut que le channel entre deux switchs aient le même groupe  

```
configure terminal  
interface range fastEthernet 0/1 - 2      // Sélectionner la plage d'interfaces  
channel-protocol lacp                     // Utilisation du protocole LACP  
channel-group 1 mode active               // Création d'un groupe EtherChannel n°1 avec le protocole LACP (mode active partout ça marche)  
no shutdown  
exit  
interface port-channel 1                  // Sélectionne et crée une interface Port-channel n°1 (même numéro que le groupe)  
switchport mode trunk                     // Passer le port-channel en mode trunk pour pouvoir faire passer plusieurs VLANs   
end                                       
```

# Connexions point à point PPP
Le PAP est beaucoup utilisé. Ex: Avec une box domestique

## PAP
Ne garantie pas la confidentialité. Man-in the middle, rejeu etc.. 
``` bash
R1(config) username R2 secret cisco  # Pour autoriser le routeur R2
R1(config) username R3 secret cisco  # Pour autoriser le routeur R3
R1(config-if) encapsulation ppp      # /!\ Attention au SSH: cette commande coupe les flux de l'interface si les deux côtés ne sont pas configurés
R1(config-if) ppp authentication pap
R1(config-if) ppp pap sent-username R1 password cisco  # On se conecte en tant que "R1" avec le mot de passe "cisco" non hashé
``` 

## CHAP
Mot de passe hashé en md5
Protège du rejeu
/!\ Il semble qu'il faille avoir un hostname identique à celui déclaré par l'autre noeud

``` bash
R1(config) username R2 secret cisco  # Pour autoriser le routeur R2
R1(config-if) encapsulation ppp
R1(config-if) ppp authentication chap      
```

# ACL - Access Control List
Il faut mettre l'ACL sur une interface pour l'activer
ACL étendue: la plus proche de la source : pour bloquer un protocole plus spécifique
ACL standard: la plus proche de la sortie : (src + dst) jsute pour bloquer un réseau

On peut tout faire en **étendue**
## Manipulation des ACL

### ACL numérotée
ACL standard: 1-99, 1300-1999  
ACL étendue:  100-199, 2000-2699

⚠️ **Masque inversé**    
0.0.0.0 : Match exactement une ip  
0.0.0.255 : Match une ip dans un /24  

```
access-list <id> {deny|permit|remark} <src addr> [mask]
```

### ACL nommée
```
ip access-list {standard|extended} <nom>
```
Puis entrer ligne par ligne  

### Supprimer une liste ACL avec **no**
```
no access-list 42
```
## Création de règle dans une ACL (nommée ?)
```
{permit|deny} <proto> {<src>} {<dest>} [established]
```
```
Avec: src / dest = <addr> [<mask>] [<op> <port>]
Avec: op = lt "less than" / gt / eq / neq / range
Avec: any = alias de "0.0.0.0 255.255.255.255"
```

On peut utiliser des noms de port ex: https ou ftp  
Penser à utiliser " **?** "  

## Appliquer une ACL sur une interface
```
conf t
interface fastEthernet 0/0
ip access-group {nom-acl|id-acl} {in|out}
```

## Autoriser une connexion sur un routeur
```
access-class ...
```

## Vérification
show access-list


## Exemple type pour l'access list n°1
``` 
!--- Regles de sortie
conf t
ip access-list extended 100
permit tcp 10.0.0.0 0.0.0.255 20.0.0.0 0.0.0.255 eq 80
permit tcp 10.0.0.0 0.0.0.255 20.0.0.0 0.0.0.255 eq 443
permit tcp 10.0.0.0 0.0.0.255 20.0.0.0 0.0.0.255 range 20 21
exit

!--- Deploiement de l'ACL des sorties sur l'interface
interface fastEthernet 0/0
ip access-group 100 in ??????? or out ????
exit

!--- Regles de sortie
conf t
ip access-list extended 101
permit ip 30.0.0.0 0.0.0.255 10.0.0.0 0.0.0.255

exit

!--- Deploiement de l'ACL des entrees sur l'interface
interface fastEthernet 0/0
ip access-group 101 out
exit

```

## Suppression d'une ACL
```
conf t
no access-list 100
no access-list 101


```

# Memo
• Un Routeur 811 doit être connecté au coeur de réseau par son port WAN  
• Un Routeur 811 configuré en NAT utilise un VLAN comme IP interne  

 Calculs de sous-réseaux
 
