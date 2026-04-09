![logo Millenuits](img/millenuitlogofinal.jpg)

## Documentation - Installation du serveur MN21


## 1 - Installation de docker sous debian 12

```
sudo apt update 
sudo apt install ca-certificates curl gnupg
```

Pour que debian fasse confiance aux paquets docker : 

```
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg 
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

Configuration du dépôt :

```
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Installer le moteur Docker, l'interface et Docker Compose :

```
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Vérification du bon fonctionnement : 

```
sudo docker run hello-world
```

## 2 - Installation d'Apache sous Docker Compose

Créer un dossier dédié :

 ```
 mkdir ~/mon-apache && cd ~/mon-apache
 ```

Création de la page d'accueil : 

```
mkdir html
echo "<h1>Salut mon chou ! Ton Apache Docker fonctionne !</h1>" > html/index.html
```

Rédiger le yml :

```
nano docker-compose.yml
```

et coller le code suivant dedans

```
services:
  mon-serveur-web:
    image: httpd:latest
    container_name: apache-container
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/local/apache2/htdocs/
    restart: always
```

Ensuite, lancer le tout : 

```
docker compose up -d
```

Vérification : 

```
docker ps
```


## 3 - Installation de MYSQL

```

```