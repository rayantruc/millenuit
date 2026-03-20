

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
Je vais personnellement mettre au point un snapshot pour mon ajoutHome, gr