# PoS_ERP
### Technologien: 
- Docker
    - Ubuntu mit Apache2-Foreground
    - MariaDB (MySQL): V10.10.2
- GitHub Actions
    - YML Files
    - Markdowns
- Dolibarr 16.0
    - PHP 8.1.13

### Guide für lokal ausführbares Programm

Folgenden Code in einer docker-compose.yml, mittels *docker-compose -up -d* (im selben Ordner wie die Datei), ausführen
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
            DOLI_DB_HOST: database
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
            - database
```


Ablauf:
1. Die Images für die Datenbank (MariaDB) und Web (Dolibarr Build Image from Dockerhub, pushed from Github Action) runterladen und laufen lassen
2. http://localhost aufrufen und anmelden (**Credentials** username: admin, pw: admin)
3. Das laufende ERP genießen

### Github Actions for DockerHub
#### 1. Docker hub - Generate New Access Token 
https://hub.docker.com/settings/security?generateToken=true

#### 2. Add a GitHub Action (Docker template)
In your Repository: Settings -> Secrets -> Add Actions

#### 3. Create GitHub Secrets
Add DOCKER_HUB_ACCESS_TOKEN and DOCKER_HUB_USERNAME Secrets

#### 4. Create our workflow config file
Create our workflow config file named .github/workflows/docker.yml and add some code

```yml
name: Publish Docker Image

on:
  push:
    branches:
      - main

jobs:
  build-docker-images:
    name: Push Docker image to (private) Docker Hub
    runs-on: ubuntu-latest 
    steps:
      - 
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      - 
        name: Login to DockerHub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_HUB_USERNAME }}
          password: ${{ secrets.DOCKER_HUB_ACCESS_TOKEN }}
      - 
        name: Build and push
        id: docker_build
        uses: docker/build-push-action@v2
        with:
          push: true
          tags: ${{ secrets.DOCKER_HUB_USERNAME }}/${{ secrets.REPOSITORY_NAME }}:latest
```

**_Note:_** if the repository name isn't lowercase, create a REPOSITORY_NAME Secret and use this instead of the repository_name value

#### 5. Testing the GitHub Action
Die Github Action wird bei jedem Push auf die angegebenen branches ausgeführt.

#### 6. Pull only the built dolibarr Container
Allgemein haben wir in unserer GitHub Action definiert, das es auf *${{ secrets.DOCKER_HUB_USERNAME }}/${{ secrets.REPOSITORY_NAME }}:latest* gepushed wird.<br />
In unserem Fall zum testen: if22b190/dolibarr_erp:latest

Somit können wir uns den container holen mit: *docker pull if22b190/dolibarr_erp:latest*<br />
**_Note:_** Es ist zu empfehlen den Container mit der obrigen docker-compose auszuführen, da automatisch ein weiterer Container mit einer Datenbank gestartet wird.

### Proof of concept für Entwicklung von Modulen/Erweiterungen
#### Create Test Module
https://wiki.dolibarr.org/index.php?title=Module_development#Create_your_module
##### Generate Files from Module Builder (ab Version 9 notwendig)
1. Aktiviere das Module 'Module Builder' in der Module-Liste
2. Öffne das ModuleBuilder Menü (Rechts in der oberen Menüleiste, das neue Symbol mit einem Käfer)
3. Klicke auf neues Module erstellen und fülle Daten wie Name, Autor, Version, etc. des Modules aus
Das neues Modul befindet sich nun bei 'htdocs/custom/<modulename>/'.
##### Seite Anpassen
Option A: '<mymodule>index.php' überschreiben, weches die Startseite des Plugins ist
Option B: eine neue File erstellen und im Module Descriptor ergänzen (z.B. als Tab der auf die .php file referenziert)
Immer gilt: im '<mymodule>/admin> befinden sich die Seiten für die Configuration und Readme (About) Page
