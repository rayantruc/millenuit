![logo Millenuits|554](img/millenuitlogofinal.jpg)
## Installation de git et git desktop
Git permettras de modifier notre documentation sur un depot local, de la pousser sur un depot publique et de la partager a l'aide d'un lien avec github pages.
### Installation de git
L'installation de Git est plutôt simple et directe, il suffit de suivre le processus d'installation a partir du fichier d'installation disponible sur le site officiel

![logo Millenuits|363](img/git.png)

### Installation de github desktop 
Github desktop rendras plus facile la gestion du dépôt mais celle ci n'est pas nécessaire.
Similairement il suffit d'utiliser le fichier de téléchargement officiel et de se connecter a son compte github afin d'avoir accès a ses dépôts.

![logo Millenuits|614](img/desktop.png)

Nous avons maintenant accès a github et a nos dépôts publiques

![desktop|614](img/desktops.png)

## Installation d'obsidian

Obsidian sert d'éditeur de texte en markdown pour nous faciliter la création de la documentation, il permettras aussi d'effectuer le lien avec git a l'aide d'une extension et de pull, commit et push automatiquement nos documentations pour être sur qu'elle soit toujours a jour.

Comme le reste des applications l'installation d'obsisian se fait a travers le fichier d'installation disponible sur leur site officiel
![obsidian|614](img/obsidian.png)


## Mise en place d'Mkdocs

Mk docs est l'outil qui permettras de transformer le coffre fort obsidian markdown en site statique html.

Si l'ordinateur possède une version récente de python la commande est la suivante:

`python -m pip install mkdocs python -m mkdocs`

celle si nous permettras d'installer mkdocs.
Ensuite:

`python -m mkdocs`

nous permettras de lancer mkdocs pour vérifier son bon fonctionnement.

l'installation étant terminée il faut créer un dossier pour le markdown et s'y déplacer avec ces commande:

`python -m mkdocs new millenuit`
`cd millenuit

Ceci vas créer un fichier yml et md il suffiras de modifier son fichier md et de lancer cette commande pour créer un site statique a partir du markdown:

`python -m mkdocs serve`

le site vas ensuite être créer et seras disponible a l'adresse de l'hôte
## Mise en place finale

### Arborescence 
```

\Github 
   |
   |__ \millenuit
           |
           |____________________________________
              |            |                   |    
              \.github     \.obsidian          \docs
              repo github   vault obsidian     dossier mkdocs
```

### créer un depot github

Pour pouvoir synchroniser toutes les applications il faut d'abord transformer le dossier mkdocs en dépôt github a l'aide de github desktop (ou du bash git)


![repo|335](img/git_repo.png)

après ca le dossier \.github seras créer et le dossier millenuit seras considéré comme un dépôt github, on pourras donc transformer ce dépôt local en dépôt publique et pousser nos modifications.

### créer un vault obsidian

![vault|537](img/vault.png)

Il suffit de sélectionner "Ouvrir un dosser comme coffre" et de choisir le dossier principal donc /millenuit dans notre cas. Ceci vas créer le dossier /.obsidian et transformer le dossier en coffre obsidian.

### Connexion entre git et obsidian

Pour pou