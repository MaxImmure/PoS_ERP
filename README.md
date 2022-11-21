# PoS_ERP
### Benötigte Technologien:
- Docker

### Guide für lokal ausführbares Programm
1. Repository klonen
2. Command `docker-compose up -d` ausfüheren ([Dolibarr Image](https://hub.docker.com/r/tuxgasy/dolibarr) und [MariaDB Image](https://hub.docker.com/_/mariadb/) werden gedownloaded/aktualisiert und Conaintainer werden gestartet)
3. http://localhost aufrufen und anmelden (**Credentials** username: admin, pw: admin)

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