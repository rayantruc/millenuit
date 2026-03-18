![logo Millenuits](img/millenuitlogofinal.jpg)


---
## 1 - Prérequis

**Php 8.2-fpm / mariadb / apache2 ainsi que les extensions php**

`sudo apt-get update && sudo apt-get upgrade`
`sudo apt-get install apache2 php8.4-fpm mariadb-server`
`sudo apt install php8.4-{curl,gd,intl,mysql,zip,bcmath,mbstring,xml,bz2}`

## 2 - Création de la base de données pour GLPI

`sudo mariadb-secure-installation`
`sudo mysql -u root -p`

`CREATE DATABASE db_glpi;`
`GRANT ALL PRIVILEGES ON db_glpi.* TO 'glpi_adm'@localhost IDENTIFIED BY
'MotDePasseRobuste';`
`FLUSH PRIVILEGES;`
`EXIT;`

## 3 - Télécharger et déployer GLPI

`cd /tmp`
`wget https://github.com/glpi-project/glpi/releases/download/<VERSION>/glpi-
`|VERSION|.tgz`
`sudo tar -xzvf glpi-|VERSION|.tgz -C /var/www/`

*remplacer |VERSION| par la version de glpi souhaitée

## 4 - Organiser les répertoires, permissions et 










