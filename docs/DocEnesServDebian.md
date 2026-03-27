




--------------------------------------------------------------------------
--------------------------------------------------------------------------

## Ajout d'un nouveau disque avec LVM


On veut obtenir les informations concernant le VG

```
sudo vgdisplay
```

Ensuite, nous ajoutons un nouveau disque dans un groupe de volume existant

```
sudo vgextend vgsio1 /dev/sdb
```

![logo Millenuits|614](img/debi1.png)

On refait notre vgdisplay pour vérifier que notre nouveau disque est bien ajouté 

```
sudo vgdisplay
```

![logo Millenuits|614](img/debi2.png)

On voit donc ici à l'avant dernière ligne que c'est bon :

```
Free PE / Size          2815 / <11.00 GiB
```

On augmente désormais la taille du volume logique souhaité, ici "lvhome"

```
sudo lvextend  -L +4G /dev/vgsio1/lvhome
```

![logo Millenuits|614](img/debi3.png)

Après augmenté la taille de notre volume logique, il est important de redimensionner le système de fichier utilisé

```
sudo btrfs filesystem resize max /home
```

Ensuite vérifions que tout est en ordre 

```
df -h
```

![logo Millenuits|614](img/debi4.png)

Maintenant, il faut créer notre volume logique "lvsrv" dans notre VG "vgsio1"

```
sudo lvcreate -L 3G -n lvsrv vgsio1
```

![logo Millenuits|614](img/debi6.png)

Désormais, il faut formater notre LV en ext4 

```
sudo mkfs.ext4 /dev/vgsio1/lvsrv
```

![logo Millenuits|614](img/debi7.png)

Montage du disque dur sur le système 

```
sudo mount /dev/vgsio1/lvsrv /srv
```

![logo Millenuits|614](img/debi8.png)



--------------------------------------------------------------------------
--------------------------------------------------------------------------


## Création de snapshots pour volumes logiques 

Commencer par s'assurer qu'il reste de la place dans le groupe de volume avec la commande : 

```
sudo vgs
```

Ensuite nous allons identifier le volume logique pour lequel nous voulons créer un instantané :

```
sudo lvs
```

Normalement on voit apparaître : 

![logo Millenuits|614](img/deb1.png)

Ici nous voyons donc nos différents volumes logiques.
Je vais personnellement mettre au point un snapshot pour mon ajoutHome, grâce à la commande suivante : 

```
sudo lvcreate -L 0.5G -s -n snaphome /dev/Home/ajoutHome 
```

Nous voyons donc apparaitre ceci si tout fonctionne bien : 

![logo Millenuits|614](img/deb2.png)

A chaud si nous retournons sur un ancien snapshot, il est important de reboot



--------------------------------------------------------------------------
--------------------------------------------------------------------------

## OpenSSH

```
The authenticity of host 'serveur.example.com (192.168.1.10)' can't be established.
ECDSA key fingerprint is SHA256:...
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```
 
1) Si ce type de message apparaît lors d'un première connexion, c'est normal, lors de la première connexion au serveur, le client enregistre la clé publique du serveur, pour ainsi vérifier l'identité du serveurs lors des prochaines connexions.

2) Donc forcément si nous avons répondu "yes" au préalable, ce message ne réapparaîtra plus, étant donné que nous avons déjà stocké sa clé publique.

3) `/etc/ssh/ssh_host_*` est l'emplacement où sont stockées les paires de clés.
		`ssh_host_ed25519_key` --> Clé privée Ed25519 (recommandée)
		`ssh_host_ed25519_key.pub` --> Clé publique Ed25519
		`ssh_host_rsa_key` --> Clé privée RSA (obsolète, à éviter)


**Sécuriser les connexions ssh

Premièrement, les bonnes pratiques sont de faire une sauvegardes avant toutes modifications 

```
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

Ensuite, redémarrer le service après modifications 

```
sudo systemctl restart ssh
```


Maintenant, générons nos clés, pour cela nous allons nous servir de l'algorithme ed 


