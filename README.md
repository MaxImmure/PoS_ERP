# PoS_ERP
### Benötigte Technologien:
- Docker

### Guide für lokal ausführbares Programm

Folgenden Code in einer docker-compose.yml, mittels *docker-compose -up -d*, ausführen
```yml
version: "3"

services:
    database:
        image: mariadb:latest
        environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: dolibarr

    web:
        image: if22b190/dolibarr_erp:latest
        environment:
            DOLI_DB_HOST: mariadb
            DOLI_DB_USER: root
            DOLI_DB_PASSWORD: root
            DOLI_DB_NAME: dolibarr
            DOLI_ADMIN_LOGIN: admin
            DOLI_ADMIN_PASSWORD: admin
            DOLI_URL_ROOT: 'http://localhost'
            PHP_INI_DATE_TIMEZONE: 'Europe/Paris'
        ports:
            - "80:80"
        links:
            - mariadb
```


Ablauf:
1. Die Images für die Datenbank (MariaDB) und Web (Dolibarr Build Image from Dockerhub, pushed from Github Action) runterladen und laufen lassen
2. http://localhost aufrufen und anmelden (**Credentials** username: admin, pw: admin)
3. Das laufende ERP genießen

### Github Actions for DockerHub
#### Docker hub - Generate New Access Token 
https://hub.docker.com/settings/security?generateToken=true
#### Create GitHub Secrets
In your Repository: Settings -> Secrets -> Add Actions
Add DOCKER_HUB_ACCESS_TOKEN and DOCKER_HUB_USERNAME Secrets
#### Create our workflow config file
Create our workflow config file named .github/workflows/docker.yml and add some code
##### Note: if the repository name isn't lowercase, create a REPOSITORY_NAME Secret and use this instead of the repository_name value

### Proof of concept für Entwicklung von Modulen/Erweiterungen
