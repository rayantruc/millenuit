![logo Millenuits](img/millenuitlogofinal.jpg)

## Prérequis

Avant toute installation vérifiez bien de remplir tout ces critères :
	- Une machine Debian avec une adresse IP fixe 
	- Aucun autre serveur DHCP actif sur le réseau afin d'éviter les conflits
	- Au moins une machine cliente sans configuration IP afin de tester le serveur DHCP
	- Une connexion internet afin de télécharger les paquets d'installation 

## Installation du serveur DHCP KEA

La première étape consiste à effectuer la mise à jour du cache des paquets du système et à installer le paquet correspondant au serveur DHCP IPv4 :

```
sudo apt-get update  
sudo apt-get install kea-dhcp4-server
```

Vous pouvez ensuite vérifier l'état du service : 

 ```
 sudo systemctl status kea-dhcp4-server
 ```

## Configuration du serveur DHCP KEA

À partir de la console, affichez la configuration IP de votre machine pour identifier le nom de l'interface réseau. Dans cet exemple, il s'agit de l'interface nommée "**ens33**".

```
ip a
```

en image : 

Nous allons partir sur un fichier de configuration vierge, car le fichier d'origine contient beaucoup d'informations. Nous allons renommer le fichier de configuration par défaut, car il peut être utile afin d'avoir un exemple :

```
sudo mv /etc/
```