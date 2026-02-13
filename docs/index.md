# Guide de Configuration Réseau

> Référence pour Étudiants en Informatique — Configuration pratique des composants réseau essentiels

---

## Table des Matières

| #   | Sujet                                                                                                                     | Protocole / Couche     |
| --- | ------------------------------------------------------------------------------------------------------------------------- | ---------------------- |
| 01  | [[#01 — Routeur]]                                                                                                         | Couche 3 — Routage IP  |
| 02  | [[#02 — Commutateur Réseau]]                                                                                              | Couche 2 — MAC / VLANs |
| 03  | [Serveur FTP](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#03--serveur-ftp)                                | TCP Port 21            |
| 04  | [Serveur DNS](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#04--serveur-dns)                                | UDP/TCP Port 53        |
| 05  | [Serveur DHCP](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#05--serveur-dhcp)                              | UDP Port 67/68         |
| 06  | [Point d'Accès Sans Fil](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#06--point-dacc%C3%A8s-sans-fil)      | IEEE 802.11            |
| 07  | [Serveur Web (Apache / Nginx)](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#07--serveur-web-apache--nginx) | TCP Port 80 / 443      |

---

## 01 — Routeur

**Équipement de couche 3** qui achemine les paquets entre différents réseaux en utilisant l'adressage IP et les tables de routage.

### Qu'est-ce qu'un Routeur ?

Un routeur fonctionne à la **couche 3 (couche Réseau)** du modèle OSI. Son rôle principal est de transférer les paquets de données entre différents réseaux — généralement entre votre réseau local (LAN) et Internet (WAN). Il utilise une **table de routage** pour déterminer le meilleur chemin pour chaque paquet.

> **Concept clé :** Les routeurs séparent les domaines de diffusion. Chaque interface d'un routeur représente un segment réseau distinct.

### Schéma Réseau

```
    [Internet / FAI]
          │
      ┌───┴───┐
      │ROUTEUR│  ← 192.168.1.1 (passerelle)
      └───┬───┘
          │ LAN
   ───────┴─────────────────
      │         │          │
   [PC1]      [PC2]    [Serveur]
  .10          .20       .100
```

### Configuration — Cisco IOS

**Étape 1 — Connexion via Console**

Utilisez un câble console (rollover) entre votre PC et le port console du routeur. Ouvrez un émulateur de terminal (PuTTY, SecureCRT) à **9600 bauds, 8N1**.

**Étape 2 — Passer en Mode Privilégié**

```ios
Router> enable
Router# configure terminal
```

**Étape 3 — Définir le Nom d'Hôte et le Mot de Passe**

```ios
Router(config)# hostname MonRouteur
MonRouteur(config)# enable secret MotDePasseFort123
MonRouteur(config)# service password-encryption
```

**Étape 4 — Configurer l'Interface LAN**

```ios
MonRouteur(config)# interface GigabitEthernet0/0
MonRouteur(config-if)# ip address 192.168.1.1 255.255.255.0
MonRouteur(config-if)# no shutdown
MonRouteur(config-if)# description Interface LAN
```

**Étape 5 — Configurer l'Interface WAN et la Route par Défaut**

```ios
MonRouteur(config)# interface GigabitEthernet0/1
MonRouteur(config-if)# ip address dhcp
MonRouteur(config-if)# no shutdown
MonRouteur(config)# ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/1
```

**Étape 6 — Sauvegarder la Configuration**

```ios
MonRouteur# copy running-config startup-config
```

**Termes clés :** `Table de routage` · `NAT` · `WAN` · `Passerelle par défaut` · `Cisco IOS`

---

## 02 — Commutateur Réseau

**Équipement de couche 2** qui interconnecte les appareils d'un même réseau grâce à l'apprentissage des adresses MAC et à la segmentation optionnelle en VLANs.

### Qu'est-ce qu'un Commutateur ?

Un commutateur fonctionne à la **couche 2 (couche Liaison de données)**. Il apprend les adresses MAC et transmet les trames uniquement vers le port destinataire — contrairement à un hub qui diffuse vers tous les ports. Les **commutateurs administrables** modernes supportent les VLANs, STP, QoS et la mise en miroir de ports.

|Type|Configuration|Cas d'utilisation|
|---|---|---|
|**Non administrable**|Aucune|Petits réseaux domestiques/bureaux|
|**Administrable**|Requise (CLI ou interface web)|Entreprise, VLANs, supervision|

### Architecture VLAN

```
  Commutateur (24 ports)
  ┌──────────────────────────┐
  │  VLAN 10 — Ports  1–8   │  ← Personnel
  │  VLAN 20 — Ports  9–16  │  ← Étudiants
  │  VLAN 99 — Port   24    │  ← Gestion
  └──────────────────────────┘
        Port trunk → Routeur (marquage 802.1Q)
```

### Configuration — Commutateur Administrable Cisco

**Étape 1 — Créer les VLANs**

```ios
Switch(config)# vlan 10
Switch(config-vlan)# name PERSONNEL
Switch(config)# vlan 20
Switch(config-vlan)# name ETUDIANTS
```

**Étape 2 — Affecter les Ports d'Accès aux VLANs**

```ios
Switch(config)# interface range Fa0/1 - 8
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
```

**Étape 3 — Configurer le Port Trunk (vers le Routeur)**

```ios
Switch(config)# interface Fa0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,99
```

**Étape 4 — Définir l'Adresse IP de Gestion**

```ios
Switch(config)# interface vlan 99
Switch(config-if)# ip address 192.168.99.2 255.255.255.0
Switch(config-if)# no shutdown
```

> **Remarque :** Configurez toujours un VLAN de gestion distinct des VLANs utilisateurs pour l'accès distant via SSH ou l'interface web.

**Termes clés :** `VLAN` · `Trunk 802.1Q` · `STP` · `Table MAC` · `Port d'accès`

---

## 03 — Serveur FTP

**File Transfer Protocol** — permet le transfert de fichiers sur un réseau via `vsftpd` sous Linux.

### Fonctionnement du FTP

Le FTP utilise **deux canaux distincts** :

- **Canal de contrôle** (port 21) — pour l'authentification et les commandes
- **Canal de données** (port 20 actif / port négocié passif) — pour le transfert effectif des fichiers

|Mode|Connexion de données|NAT/Pare-feu|
|---|---|---|
|**Actif**|Serveur → Client (port 20)|❌ Échoue avec le NAT|
|**Passif**|Client → Serveur (port élevé)|✅ Fonctionne avec le NAT|

> ⚠️ **Avertissement de sécurité :** Le FTP standard transmet les identifiants en **texte clair**. En production, utilisez **SFTP** (port 22, via SSH) ou **FTPS** (FTP sur TLS). Réservez le FTP classique aux réseaux de laboratoire isolés.

### Configuration — vsftpd sur Ubuntu

**Étape 1 — Installer vsftpd**

```bash
sudo apt update && sudo apt install vsftpd -y
sudo systemctl enable vsftpd
```

**Étape 2 — Modifier le Fichier de Configuration**

```bash
sudo nano /etc/vsftpd.conf
```

Ajoutez ou décommentez les lignes suivantes :

```ini
# Autoriser les utilisateurs locaux à écrire des fichiers
write_enable=YES

# Confiner les utilisateurs dans leur répertoire personnel
chroot_local_user=YES
allow_writeable_chroot=YES

# Activer le mode passif pour la compatibilité NAT
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=40100
```

**Étape 3 — Créer un Utilisateur FTP et son Répertoire**

```bash
sudo adduser utilisateurftp
sudo mkdir -p /home/utilisateurftp/fichiers
sudo chown nobody:nogroup /home/utilisateurftp
sudo chown utilisateurftp:utilisateurftp /home/utilisateurftp/fichiers
```

**Étape 4 — Ouvrir les Ports du Pare-feu et Redémarrer**

```bash
sudo ufw allow 21/tcp
sudo ufw allow 40000:40100/tcp
sudo systemctl restart vsftpd
```

**Étape 5 — Tester la Connexion**

```bash
ftp 192.168.1.20
# ou utiliser FileZilla avec le mode passif activé
```

**Termes clés :** `vsftpd` · `Port 21` · `Mode passif` · `chroot` · `SFTP`

---

## 04 — Serveur DNS

**Domain Name System** — résout les noms d'hôtes en adresses IP via `BIND9` sous Linux.

### Fonctionnement de la Résolution DNS

```
 Le navigateur demande : "quelle est l'IP de webserver.lab.local ?"
        │
   ┌────▼─────┐
   │Résolveur │  → vérifie le cache local d'abord
   └────┬─────┘
        │ absence dans le cache
   ┌────▼──────────────┐
   │    BIND9 DNS      │  192.168.1.53
   │  (autoritaire)    │
   └────┬──────────────┘
        │ correspondance trouvée dans le fichier de zone
        ▼
   Retourne : 192.168.1.10  ✓
```

### Types d'Enregistrements DNS Courants

|Enregistrement|Rôle|Exemple|
|---|---|---|
|`A`|Nom d'hôte → IPv4|`webserver → 192.168.1.10`|
|`AAAA`|Nom d'hôte → IPv6|`webserver → ::1`|
|`CNAME`|Alias vers un autre nom d'hôte|`www → webserver`|
|`MX`|Serveur de messagerie du domaine|`@ → mail.lab.local`|
|`PTR`|Résolution inverse (IP → nom)|`10 → webserver.lab.local`|
|`NS`|Serveur de noms autoritaire|`@ → ns1.lab.local`|
|`SOA`|Enregistrement d'autorité de zone|Début du fichier de zone|

### Configuration — BIND9 sur Ubuntu

**Étape 1 — Installer BIND9**

```bash
sudo apt install bind9 bind9utils bind9-doc -y
```

**Étape 2 — Déclarer la Zone**

Éditez `/etc/bind/named.conf.local` :

```
zone "lab.local" {
    type master;
    file "/etc/bind/zones/db.lab.local";
};
```

**Étape 3 — Créer le Fichier de Zone**

```bash
sudo mkdir -p /etc/bind/zones
sudo nano /etc/bind/zones/db.lab.local
```

```dns
$TTL    604800
@  IN  SOA  ns1.lab.local. admin.lab.local. (
              2025010101 ; Numéro de série  ← à incrémenter après chaque modification
              604800     ; Rafraîchissement
              86400      ; Nouvelle tentative
              2419200    ; Expiration
              604800 )   ; TTL minimum

; Enregistrement du serveur de noms
@       IN  NS    ns1.lab.local.
ns1     IN  A     192.168.1.53

; Enregistrements A des hôtes
router      IN  A   192.168.1.1
webserver   IN  A   192.168.1.10
fileserver  IN  A   192.168.1.20

; Alias CNAME
www     IN  CNAME  webserver
```

**Étape 4 — Valider et Redémarrer**

```bash
sudo named-checkconf
sudo named-checkzone lab.local /etc/bind/zones/db.lab.local

sudo systemctl restart bind9
```

**Étape 5 — Tester la Résolution**

```bash
nslookup webserver.lab.local 192.168.1.53
dig @192.168.1.53 www.lab.local
```

> **Conseil :** Incrémentez toujours le **numéro de série** après avoir modifié un fichier de zone, sinon les serveurs DNS secondaires ne prendront pas en compte vos changements.

**Termes clés :** `BIND9` · `Fichier de zone` · `Enregistrement A` · `SOA` · `Port 53` · `nslookup`

---

## 05 — Serveur DHCP

**Dynamic Host Configuration Protocol** — attribue automatiquement des adresses IP et la configuration réseau aux clients via `isc-dhcp-server`.

### Le Processus DORA

```
 Client                        Serveur DHCP
   │                                │
   ├──── DISCOVER ─────────────────▶│  "Y a-t-il un serveur ? J'ai besoin d'une IP."
   │◀─── OFFER ──────────────────── │  "Je vous propose 192.168.1.105"
   ├──── REQUEST ───────────────────▶│  "J'accepte 192.168.1.105, merci."
   │◀─── ACK ───────────────────────┤  "Confirmé — bail valide 24h."
   │                                │
```

### Ce que le DHCP Attribue

- **Adresse IP** — depuis une plage définie (pool)
- **Masque de sous-réseau** — pour le segment réseau
- **Passerelle par défaut** — l'adresse IP du routeur
- **Serveur(s) DNS** — pour la résolution de noms
- **Durée du bail** — délai avant renouvellement

### Configuration — isc-dhcp-server sur Ubuntu

**Étape 1 — Installer le Paquet**

```bash
sudo apt install isc-dhcp-server -y
```

**Étape 2 — Définir l'Interface d'Écoute**

Éditez `/etc/default/isc-dhcp-server` :

```ini
INTERFACESv4="eth0"
```

**Étape 3 — Configurer le Sous-réseau et la Plage d'Adresses**

Éditez `/etc/dhcp/dhcpd.conf` :

```
default-lease-time 86400;    # 24 heures
max-lease-time 172800;       # 48 heures maximum

subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.100 192.168.1.200;        # plage d'adresses IP
  option routers 192.168.1.1;               # passerelle par défaut
  option domain-name-servers 192.168.1.53, 8.8.8.8;
  option domain-name "lab.local";
}

# Réservation statique par adresse MAC (ex. pour une imprimante)
host imprimante {
  hardware ethernet AA:BB:CC:DD:EE:FF;
  fixed-address 192.168.1.50;
}
```

**Étape 4 — Démarrer et Surveiller**

```bash
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server

# Afficher les baux actifs
cat /var/lib/dhcp/dhcpd.leases
```

> **Conseil pratique :** Utilisez les **réservations statiques** (liaison IP + MAC) pour les serveurs et imprimantes afin qu'ils reçoivent toujours la même adresse IP sans configuration statique manuelle sur l'équipement.

**Termes clés :** `DORA` · `Bail` · `Plage IP` · `Réservation` · `Port 67/68`

---

## 06 — Point d'Accès Sans Fil

Équipement **IEEE 802.11** qui étend un réseau filaire aux clients sans fil sans assurer de routage.

### PAP vs Autres Équipements

|Équipement|Routage|Wi-Fi|Cas d'utilisation|
|---|---|---|---|
|**PAP (WAP)**|❌|✅|Étend un LAN filaire existant|
|**Routeur sans fil**|✅|✅|Appareil tout-en-un pour la maison|
|**Répéteur/Amplificateur**|❌|✅|Rediffuse le signal existant (ajoute de la latence)|
|**Nœud Mesh**|❌|✅|Itinérance transparente sur de grandes surfaces|

### Comparatif des Normes 802.11

|Norme|Nom Wi-Fi|Débit max|Fréquence|
|---|---|---|---|
|802.11n|Wi-Fi 4|600 Mbps|2,4 / 5 GHz|
|802.11ac|Wi-Fi 5|3,5 Gbps|5 GHz|
|802.11ax|Wi-Fi 6|9,6 Gbps|2,4 / 5 / 6 GHz|
|802.11be|Wi-Fi 7|46 Gbps|Multi-bande|

> **Sécurité minimale :** Utilisez toujours **WPA2-Personal** ou supérieur. Préférez **WPA3** si disponible. N'utilisez jamais WEP ni les réseaux ouverts.

### Configuration — Interface Web Générique (Ubiquiti / Cisco / TP-Link)

**Étape 1 — Connexion Physique**

Branchez le PAP sur un **port de commutateur PoE** (ou utilisez un injecteur PoE). Un seul câble Cat5e/Cat6 fournit à la fois l'alimentation et les données. Vérifiez que le port est bien affecté au bon VLAN.

**Étape 2 — Accéder à l'Interface d'Administration**

```
# Accès par défaut selon la marque :
Ubiquiti UniFi  →  Adoption via l'application UniFi Controller
Cisco WAP       →  https://192.168.1.1  (voir l'étiquette)
TP-Link EAP     →  https://192.168.0.254

Identifiants par défaut : admin / admin
⚠ À modifier immédiatement après la première connexion !
```

**Étape 3 — Créer le Réseau Sans Fil (SSID)**

```
Nom SSID :    ReseauLabo_5G
Bande :       5 GHz  (ou double bande)
Sécurité :    WPA2-Personal  (WPA3 si disponible)
Mot de passe : [minimum 12 caractères, mixte]
Tag VLAN :    20  (optionnel, pour la segmentation réseau)
```

**Étape 4 — Configurer le Canal et la Puissance d'Émission**

```
2,4 GHz → Utiliser les canaux 1, 6 ou 11 (sans chevauchement)
5 GHz   → Activer le Canal Auto (les canaux DFS sont acceptables)

Largeur de canal :  40 MHz (2,4 GHz)  /  80 MHz (5 GHz)
Puissance TX :      Auto ou Moyen  ← éviter le Maximum dans les environnements denses
```

**Étape 5 — Activer le Réseau Invité (Optionnel)**

Créez un second SSID associé à un VLAN isolé. Activez **l'isolation des clients** pour empêcher les invités de communiquer entre eux ou d'accéder au LAN principal. Routez le trafic invité vers un sous-réseau distinct avec accès Internet uniquement.

**Termes clés :** `SSID` · `WPA3` · `PoE` · `Canal` · `VLAN` · `Isolation des clients`

---

## 07 — Serveur Web (Apache / Nginx)

**Serveur HTTP/HTTPS** qui sert du contenu web aux navigateurs, configuré via Apache2 ou Nginx sous Linux.

### Apache vs Nginx

|Fonctionnalité|Apache|Nginx|
|---|---|---|
|Architecture|Basée sur les processus|Événementielle (asynchrone)|
|Configuration|Par répertoire `.htaccess`|Configuration centralisée|
|Support PHP|Natif (mod_php)|Via PHP-FPM|
|Performances|Bon pour le contenu dynamique|Excellent pour les fichiers statiques et la forte charge|
|Proxy inverse|Supporté|Excellent|
|Idéal pour|Hébergement mutualisé, apps PHP|Sites à fort trafic, microservices|

### Concept d'Hôte Virtuel

```
  Requête entrante : GET http://site-a.lab.local

        │
  ┌─────▼───────────────────────────────┐
  │           Serveur Web               │
  │                                     │
  │  VHost: site-a.lab.local  ─────────▶ /var/www/site-a  │
  │  VHost: site-b.lab.local  ─────────▶ /var/www/site-b  │
  └─────────────────────────────────────┘
  Une seule adresse IP → Plusieurs sites web
```

### Configuration — Apache2 sur Ubuntu

**Étape 1 — Installer Apache2**

```bash
sudo apt install apache2 -y
sudo systemctl enable apache2
# Vérification : ouvrir http://192.168.1.10 → page par défaut d'Apache
```

**Étape 2 — Créer un Fichier d'Hôte Virtuel**

```bash
sudo nano /etc/apache2/sites-available/site-a.conf
```

```apache
<VirtualHost *:80>
    ServerName    site-a.lab.local
    DocumentRoot  /var/www/site-a

    ErrorLog   ${APACHE_LOG_DIR}/site-a-error.log
    CustomLog  ${APACHE_LOG_DIR}/site-a-access.log combined
</VirtualHost>
```

**Étape 3 — Créer la Racine Web et Activer le Site**

```bash
sudo mkdir -p /var/www/site-a
echo "<h1>Le Site A fonctionne !</h1>" | sudo tee /var/www/site-a/index.html

sudo a2ensite site-a.conf
sudo systemctl reload apache2
```

**Étape 4 — Activer HTTPS**

```bash
# Option A — Certificat auto-signé (pour labs/usage interne) :
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/lab.key \
  -out /etc/ssl/certs/lab.crt

sudo a2enmod ssl
# Puis ajouter les directives SSL dans le VirtualHost sur le port 443

# Option B — Let's Encrypt (pour les serveurs publics) :
sudo apt install certbot python3-certbot-apache
sudo certbot --apache
```

---

### Configuration — Nginx sur Ubuntu

**Étape 1 — Installer Nginx**

```bash
sudo apt install nginx -y
sudo systemctl enable nginx
```

**Étape 2 — Créer un Bloc Serveur**

```bash
sudo nano /etc/nginx/sites-available/site-a
```

```nginx
server {
    listen 80;
    server_name site-a.lab.local;

    root  /var/www/site-a;
    index index.html;

    access_log /var/log/nginx/site-a-access.log;
    error_log  /var/log/nginx/site-a-error.log;
}
```

**Étape 3 — Activer le Site et Recharger**

```bash
sudo ln -s /etc/nginx/sites-available/site-a /etc/nginx/sites-enabled/
sudo nginx -t          # tester la syntaxe de la configuration
sudo systemctl reload nginx
```

**Termes clés :** `Hôte virtuel` · `DocumentRoot` · `TLS/SSL` · `Let's Encrypt` · `Port 80/443` · `Certbot`

---

## Référence Rapide — Ports et Protocoles

|Service|Protocole|Port(s)|Remarques|
|---|---|---|---|
|Contrôle FTP|TCP|21|Authentification et commandes|
|Données FTP (Actif)|TCP|20|Connexion initiée par le serveur|
|Données FTP (Passif)|TCP|40000–40100|Connexion initiée par le client ; compatible NAT|
|SSH / SFTP|TCP|22|Alternative chiffrée au FTP|
|DNS|UDP / TCP|53|UDP pour les requêtes, TCP pour les transferts de zone|
|Serveur DHCP|UDP|67|Le serveur écoute|
|Client DHCP|UDP|68|Le client écoute|
|HTTP|TCP|80|Trafic web non chiffré|
|HTTPS|TCP|443|Trafic web chiffré via TLS|

---

## Commandes de Diagnostic Utiles

```bash
# Tester la résolution DNS
nslookup webserver.lab.local 192.168.1.53
dig @192.168.1.53 site-a.lab.local

# Vérifier les ports ouverts sur un serveur
ss -tulnp
netstat -tulnp

# Tester la réponse HTTP
curl -I http://site-a.lab.local

# Afficher les baux DHCP
cat /var/lib/dhcp/dhcpd.leases

# Vérifier l'état d'un service (remplacer par le service souhaité)
sudo systemctl status bind9
sudo systemctl status apache2

# Consulter les journaux en temps réel
sudo tail -f /var/log/apache2/site-a-access.log
sudo journalctl -u nginx -f

# Ping / traceroute
ping 192.168.1.1
traceroute 8.8.8.8
```

---

_Guide de Configuration Réseau · Référence pour Étudiants en Informatique · 2025_