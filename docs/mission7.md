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
