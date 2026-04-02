# Documentation : Mise en place de Windows Server 2019 (AD DS & DNS)

Cette documentation décrit la procédure de déploiement d'un **Contrôleur de Domaine (DC)** incluant les rôles **Active Directory Domain Services** et **DNS**.

## 1. Paramètres de l'Infrastructure

- **Adresse IP du serveur :** `172.16.54.25`
    
- **Masque de sous-réseau :** `255.255.255.0` 
    
- **OS :** Windows Server 2019 Standard 
    
- **Utilisateur :** Administrateur local
    

---

## 2. Préparation du Système

### Configuration de la carte réseau

Avant d'installer les rôles, la configuration IP doit être statique.

1. Allez dans **Centre Réseau et partage** > **Modifier les paramètres de la carte**.
    
2. Propriétés de **Protocole Internet version 4 (TCP/IPv4)** :
    
    - Adresse IP : `172.16.54.25`
        
    - Masque : `255.255.255.0`
        
    - Passerelle : `172.16.54.252` (ou l'IP de votre routeur)
        
    - DNS Préféré : `127.0.0.1, 8.8.8.8` (Le serveur consultera sa propre base DNS).
        

### Nommage du serveur

1. Dans le **Gestionnaire de serveur**, cliquez sur **Serveur local**.
    
2. Cliquez sur le nom actuel pour le modifier (ex: `SRV-AD-01`).
    
3. **Redémarrez impérativement** après le changement de nom.
    

---

## 3. Installation des Rôles AD DS et DNS

1. Dans le **Gestionnaire de serveur**, cliquez sur **Gérer** > **Ajouter des rôles et fonctionnalités**.
    
2. Type d'installation : **Installation basée sur un rôle ou une fonctionnalité**.
    
3. Sélection du serveur : Sélectionnez `SRV-AD-01` (172.16.54.25).
    
4. Rôles de serveurs :
    
    - Cochez **Services de domaine Active Directory**.
        
    - Acceptez l'ajout des fonctionnalités requises.
        
    - Cochez **Serveur DNS**.
        
5. Cliquez sur **Suivant** jusqu'à la fin et lancez l'installation.
    

---

## 4. Promotion du Contrôleur de Domaine

Une fois l'installation terminée :

1. Cliquer sur l'icône **Drapeau jaune** (Notifications) et choisissez **Promouvoir ce serveur en contrôleur de domaine**.
    
2. **Opération de déploiement** : Sélectionnez **Ajouter une nouvelle forêt**.
    
    - _Nom de domaine racine_ : Entrez votre nom de domaine (ex: `MN54.lan`).
        
3. **Options du contrôleur** :
    
    - Niveau fonctionnel : `Windows Server 2016`.
        
    - Saisissez un mot de passe de restauration (**DSRM**).
        
4. **Options DNS** : L'avertissement sur la délégation est normal, cliquez sur **Suivant**.
    
5. **Chemins d'accès** : Laissez par défaut (`C:\Windows\NTDS` et `SYSVOL`).
    
6. **Vérification** : Si les tests de prérequis réussissent, cliquez sur **Installer**.
    
    > Le serveur redémarrera automatiquement.
    

---

## 5. Configuration Post-Installation (DNS)

Pour assurer une résolution de noms parfaite sur votre réseau `172.16.54.0/24` :

### Création de la Zone de recherche inversée

1. Ouvrez l'outil **DNS** (via le menu Outils).
    
2. Faites un clic droit sur **Zones de recherche inversée** > **Nouvelle zone**.
    
3. Choisissez **Zone principale** > **Vers tous les serveurs DNS de ce domaine**.
    
4. Sélectionnez **Zone de recherche inversée IPv4**.
    
5. ID Réseau : Entrez les trois premiers octets de votre IP : `172.16.54`.
    
6. Terminez l'assistant.

## 6. Préparation du Client (Windows/Linux)

Avant d'essayer de vous connecter, le client doit pouvoir "résoudre" le nom du domaine `MN54.lan`.

- **DNS :** Modifiez les paramètres réseau du client pour que son **DNS Primaire** soit l'adresse IP de votre contrôleur de domaine (AD).
    
- **Test :** Ouvrez un terminal (CMD ou Shell) et tapez :
    
    Bash
    
    ```
    ping MN54.lan
    ```
    
    Si cela ne répond pas, la connexion échouera.
    

---

## 2. Joindre le client au Domaine (Windows)

Si vous voulez que l'utilisateur se connecte avec sa session AD :

1. Allez dans **Paramètres > Système > À propos de > Paramètres avancés du système**.
    
2. Onglet **Nom de l'ordinateur**, cliquez sur **Modifier**.
    
3. Sélectionnez **Domaine**, tapez `MN54.lan`.
    
4. Entrez les identifiants d'un administrateur du domaine.
    
5. Redémarrez.
    

---

## 3. Accéder au partage TrueNAS

Une fois que le client est sur le réseau (même s'il n'est pas joint au domaine, il peut s'authentifier) :

### Sur Windows (Explorateur de fichiers) :

1. Tapez `\\IP_DE_TRUENAS` dans la barre d'adresse.
    
2. Une fenêtre d'authentification s'ouvre.
    
3. **Utilisateur :** `MN54\nassio` (ou le nom de l'utilisateur AD créé).
    
4. **Mot de passe :** Le mot de passe défini dans l'AD.