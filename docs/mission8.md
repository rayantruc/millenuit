## 1. Présentation de la mission

L'entreprise **Mille Nuits** souhaite isoler ses équipes de développement dans un sous-réseau spécifique pour améliorer la sécurité et la gestion des flux. Cette mission consiste à configurer un environnement réseau étanche, incluant une segmentation par VLAN et un accès Wi-Fi protégé.

## 2. Segmentation Réseau (VLAN)

Pour garantir une isolation stricte vis-à-vis du reste du système d'information, un réseau local virtuel est mis en place.

- **Nom du VLAN** : VLAN_DEV
    
- **ID du VLAN** : 8
    
- **Réseau IP** : `172.40.8.0 /24`
    
- **Nombre de postes prévus** : 20.
    

### Plan d'adressage prévisionnel

| **Élément**               | **Adresse IP**               | **Rôle**                                     |
| ------------------------- | ---------------------------- | -------------------------------------------- |
| **Passerelle (Routeur)**  | `172.40.8.254`               | Interface de sortie du VLAN 8.               |
| **Plage DHCP**            | `172.40.8.1` - `172.40.8.50` | Attribution dynamique pour les développeurs. |
| **Masque de sous-réseau** | `255.255.255.0`              |                                              |

## 3. Configuration de l'Accès Wi-Fi

Un réseau sans fil dédié est déployé pour permettre une mobilité sécurisée aux développeurs.

- **SSID** : `dev-millenuit`.
    
- **Diffusion** : SSID masqué (Hidden) pour limiter la visibilité du réseau.
![logo Millenuits|363](img/wifi)

## 5. Fiche de Recette (Tests de validation)

| **Test**            | **Description**                                     | **Résultat Attendu**                                       |
| ------------------- | --------------------------------------------------- | ---------------------------------------------------------- |
| **Connectivité IP** | Brancher un client sur un port configuré en VLAN 8. | Obtention d'une adresse en `172.40.8.x`.                   |
| **Accès Wi-Fi**     | Rechercher et se connecter au SSID `dev-millenuit`. | Connexion réussie via configuration manuelle (SSID caché). |
| **Accès DMZ**       | Tenter d'accéder aux ressources de la DMZ interne.  | Accès bloqué                                               |

---
