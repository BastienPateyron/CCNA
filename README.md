# CISCO

# Classes d’adresses réseau privées et publiques
A : 0.0.0.0 	126.255.255.255	/ 8
B : 128.0.0.0 	191.255.255.255	/16
C : 192.0.0.0 	223.255.255.255	/ 24

# Classes réseau privées
A : 10.0.0.0 	10.255.255.255		/ 8
B : 172.16.0.0 	172.31.255.255		/12
C : 192.168.0.0 	192.168.255.255	/ 16

# Connexion au routeur par série
1)	Se brancher avec le câble série au port « COM » marqué en bleu
2)	Lancer « minicom » dans un terminal 	(-s si bug pour le configurer)
a.	-s : /dev modem -> /dev/tty/S0 ou S1
b.	Vitesse: 9600 8N1
Configuration de la machine
Ifconfig  eth0  192.0.0.2 netmask 255.255.255.0
route  add default gw 192.0.0.1  eth0.

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

# Configurer une sous-interface
(config-if) int fa0/0.15			// Crée une sous interface N°15
(config-subif) encapsulation dot1Q  10	// Définit le type d’encapsulation du vlan
(config-subif) ip addr 192.168.15.254  255.255.255.0
(config-subif) no shutdown

# Sécuriser les accès de gestion
Sécurisation des modes (dans l’ordre) :
•	Exécution privilégiée avec le password « class »
•	Exécution utilisateur avec le password « cisco »
•	Exécution à distance avec le password « cisco »
•	Chiffre tous les mots de passe qui ne le sont pas encore (les nouveaux entre autres)
 
[Optionnel]
(config) description Link to R2

(config) banner motd $ Vous ne passerez pas ! $ : Bannière d’échec de login, « $ » est un délimiteur

(config) interface loopback 0 : Configuration d’une IP de boucle locale
(config-if) ip addr 127.0.0.1  255.255.255.0

# Configurer l’horloge
# show controller serial 1/3 : Affiche si on est en DTE ou en DCE
(config-if) clock rate 64000 : Configuration de la clock rate côté DCE
 
# Routage
Routage statique
(config) ip route 172.16.1.0  255.255.255.0  172.16.2.1
Route par défaut
(config) ip route 0.0.0.0   0.0.0.0   172.16.2.1 
# RIP
Création d’une route RIP
(config) router rip : active le routage en mode RIP
(config-router) version 2 : met le RIP en version 2 (gère aussi les sous réseaux)
(config-router) no auto-summary : Supprime les résumés de route (pour qu’un /24 n’écrase pas u n /25)
(config-router)  network 192.168.10.0

Suppression d’une route RIP
(config) router rip
(config-router)no network 192.168.10.0

# OSPF
(config) router ospf 100					// 100 : process id ~ correspond au nom
(config-router) network 145.4.0.0  0.0.0.255 area 0	// Ajout d’une route

Redistribution inter-protocol
Il faut le configurer sur les deux interfaces du routeur. Côté RIP et côté OSPF
Si on ne précise pas « metric » côté RIP, la valeur par défaut est 16 ce qui correspond à 0 sauts

OSPF vers RIP
(config) router ospf 100				
(config-router) redistribute  rip			// [subnets] rajoute la gestion des sous-réseaux

RIP vers OSPF
(config) router rip
(config-router) redistribute ospf 100 metric 1 	// metric [1-16], 15 = 1 saut, 14 = 2 sauts, 1 = 15 sauts

# BGP
// TODO
Déclarer les reseaux qui nous appartiennent dans l’AS
network @réseau mask_si_nécessaire
neighbor @IP mask_si_nécessaire

# EIGRP
TODO
Mirroring
Redirection du traffic vu par une interface vers une autre interface

(config) monitor session 1 source interface fa 0/1
(config) monitor session 1 destination interface fa0/2









 
 
# Vlan
Configuration d’un Vlan
# show vlan : montre les vlans du device
(config) vlan 10,20 : crée les Vlans 10 et 20
(config-vlan) name étudiants : nomme le vlan « étudiants »
(config) interface range fastEthernet 0/1 – 10 : sélectionne les interfaces 1 à 10
(config-if-range) switchport mode access : active les accès par VLAN
(config-if-range) switchport access vlan 10 : les ports permettent l’accès au vlan 10
(config-if)  no switchport access vlan 2			// Supprimer un VLAN
Trunk
(config) interface FastEthernet 0/0	
(config-if) switchport mode trunk			// Pour communiquer entre vlan
(config-if) switchport trunk native vlan 99		// Si pas de tag VLAN, tag en 99
(config-if) switchport trunk allowed vlan 10, 20, 99	// Laisse passer les VLAN spécifés
(config-if) switchport trunk allowed vlan all		// Variante:  passer tous les VLAN
VTP 
Le mode client interdit la création de nouveaux VLANS
Client choix 1						Client choix 2
(config) vtp mode client					# vlan database	
Serveur						(vlan) vtp client
(config) vtp mode server
(config) vtp domain CCNA				// C’est mieux de le mettre
(config) vtp password cisco1234				// Optionnel








Configuration sous interface routeur
(conf) interface FastEthernet 0/0
(conf-if) no shutdown			// Active l’interface mère
(conf) interface FastEthernet 0/0.10	// Configure une sous interface N° 10
(conf-subif) encapsulation dot1Q 10 	// Encapsule en dot1Q le Vlan 10
(cong-subif) ip addr 192.168.1.1		// affecte l’ip à la sous interface
(config-subif) no shutdown


# NAT / PAT
PAT = NAT dynamique

# show ip nat statistics					// liste les NAT du device
Configurer un PAT
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

# show ip dhcp binding						// liste les NAT du device
A faire sur le routeur
(config) interface FastEthernet 0				// On donne une ip à l’interface
(config-if) ip addr 192.168.4.1  255.255.255.0		
(config) ip dhcp pool CLIENT_LAN				// Crée un pool d’IP « client_lan »
(dhcp-config) network 192.168.3.0 255.255.255.0		// Spécifie le réseau du DHCP
(dhcp-config) dns-server 8.8.8.8					// DNS par défaut, au hasard
(dhcp-config) default-router 192.168.3.1			// On met l’IP du routeur actuel

// Si on connecte le DHCP à un switch, si l’attribution ne marche pas parmi les machines, il faut peut-être activer la range des interfaces du switch
Réinitialiser un appareil
# erase startup-config
# delete flash :vlan.dat		// Pour Switchs uniquement
# reload

# Configurer un accès SSH
// Définir une IP pour l’interface
(config) enable secret monPassword	// Définit le mot de passe quand on enable
(config) hostname nomDuDevice
(config) ip domain-name monHote.zz.fr // Configure un nom de domaine ?
(config) username login secret passwd	// Crée un user avec un password chiffré (en 5)
(config) crypto key generate rsa 	// Génère les clés pour le SSH (1024 ou 2048)
(config) service password encryption 	// Chiffre les mots de passe       // Que si on utilise  « password »
(config) line vty  0  15			// Configure des lignes 0 à 15 pour que ssh puisse les modifier ?
(config-line) transport input ssh	 	// Autorise les modifications via SSH
(config-line) login local			// Rend l’authentification obligatoire et requiert un compte local

# Memo
•	Un Routeur 811 doit être connecté au coeur de réseau par son port WAN
•	Un Routeur 811 configuré en NAT utilise un VLAN comme IP interne
 
Calculs sous réseaux

 
