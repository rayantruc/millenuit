

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