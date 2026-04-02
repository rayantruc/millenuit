#  Documentation : Mise en place TrueNAS SCALE
![logo Millenuits](img/millenuitlogofinal.jpg)

## 1. Prérequis Matériels & Réseau

- **Système :** 8 Go RAM minimum (ECC recommandée), un SSD dédié au boot.
    
- **DNS :** L'adresse IP du contrôleur de domaine (AD) doit être accessible.
    
- **Compte de Service :** Créer un utilisateur dans l'AD nommé **`nas`** avec le mot de passe **`etudiant_007`**.
    

---

## 2. Installation et Initialisation

1. **Installation :** Booter sur l'ISO TrueNAS SCALE, installer sur le disque de boot, définir le mot de passe `admin`.
    
2. **Réseau (Network) :**
    
    - Aller dans **Network > Global Configuration**.
        
    - **Nameserver 1 :** L'IP de votre contrôleur de domaine AD.
        
    - **Domain :** `MN54.lan`.
        
    - _Ceci est vital pour que le NAS puisse "résoudre" le nom du domaine._
        

---

## 3. Configuration du Stockage (Pool)

1. Aller dans **Storage > Create Pool**.
    
2. Nommer le pool (ex: `TANK`).
    
3. Sélectionner les disques et choisir le layout (ex: Mirror pour 2 disques).
    
4. Cliquer sur **Create**.
    

---

## 4. Jonction au Domaine Active Directory

C'est ici que vous connectez TrueNAS à votre infrastructure Windows.

1. Aller dans **Credentials > Directory Services > Active Directory**.
    
2. **Domain Name :** `MN54.lan`.
    
3. **Domain Account :** `nas` (le compte créé précédemment).
    
4. **Domain Password :** `etudiant_007`.
    
5. Cocher **Enable** et **Save**.
    
6. **Vérification :** Le statut doit afficher **HEALTHY**. Si vous avez une `KeyError`, vérifiez le DNS et l'heure système (elle doit être identique à l'AD).
    

---

## 5. Création du Dataset avec Permissions NFSv4

_Note : N'utilisez pas de permissions POSIX pour l'AD, cela cause des erreurs de "Mask" ou de "User_Obj"._

1. Dans **Storage**, cliquer sur les trois points du Pool > **Add Dataset**.
    
2. **Nom :** `Partage_AD`.
    
3. **ACL Type :** Sélectionner impérativement **NFSv4**.
    
4. **ACL Mode :** Passthrough.
    
5. Cliquer sur **Save**.
    

---

## 6. Gestion des Accès Utilisateurs

1. Sur le nouveau Dataset, cliquer sur **Edit Permissions** dans le widget Permissions.
    
2. Cliquer sur **Use ACL Manager**.
    
3. Choisir le preset **NFS4_RESTRICTED**.
    
4. **Ajouter l'utilisateur AD :**
    
    - Cliquer sur **Add Item**.
        
    - **Who :** User.
        
    - **User :** Taper l'utilisateur AD (ex: `MN54\nassio`).
        
    - **Permissions :** `Modify` ou `Full Control`.
        
5. Cocher **Apply permissions recursively** et cliquer sur **Save**.
    

---

## 7. Partage SMB (Accès Windows)

1. Aller dans **Shares > Windows Shares (SMB) > Add**.
    
2. **Path :** `/mnt/TANK/Partage_AD`.
    
3. **Name :** `Partage_AD`.
    
4. Cliquer sur **Save**.
    
5. Vérifier dans **System Settings > Services** que le service **SMB** est activé (Running) et en démarrage automatique.
    

---

## 8. Connexion depuis un Client (Windows)

1. Sur le PC client, ouvrir l'explorateur de fichiers.
    
2. Taper `\\IP_DU_TRUENAS` ou `\\nom_du_nas.MN54.lan`.
    
3. Entrer les identifiants de l'utilisateur AD (ex: `nassio` / mot de passe AD).
    

