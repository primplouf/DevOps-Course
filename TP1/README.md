# TP1

## Document your database container essentials: commands and Dockerfile.

### Commandes postgres

#### Build

```bash
docker build -t dockercourse/postgres .
```
```-t``` : Option tag, permet de préciser le nom de l'image.

#### Run
```bash
docker run --net=hehe-network --name=postgres -v ./local-db:/var/lib/postgresql/data -d dockercourse/postgres
```
- ```--net``` : Option net, permet de préciser le réseau auquel appartient le conteneur, celui-ci pourra communiquer avec les autres conteneurs présents au sein du même réseau.
- ```--name``` : Option name, permet de nommer un conteneur.
- ```-v``` : Option v, permet de préciser le volume rattaché au conteneur. Un volume correspond au mapping d'un dossier ou fichier du filesystem du conteneur à un dossier du filesystem de la machine hôte. Cela rend possible la persistence des données.
- ```-d``` : Option d, permet de lancer le conteneur en arrière plan de façon 'détachée' du terminal au sein duquel il est lancé.

#### Adminer

```bash
docker run -p "8090:8080" --net=hehe-network --name=adminer -d adminer
```
- ```-p``` : Option p, permet de mapper un port du conteneur à un port de la machine hôte.
- ```--net``` : Option net, permet de préciser le réseau auquel appartient le conteneur, celui-ci pourra communiquer avec les autres conteneurs présents au sein du même réseau.
- ```--name``` : Option name, permet de nommer un conteneur.
- ```-d``` : Option d, permet de lancer le conteneur en arrière plan de façon 'détachée' du terminal au sein duquel il est lancé.

### Why do we need a multistage build? And explain each step of this dockerfile.

Nous utilisons une construction en plusieurs étapes afin de séparer les étapes de build et d'exécution.
L'environnement d'exécution n'a pas besoin des mêmes outils que l'environnement de build.
Il est plus sûr d'utiliser un build en plusieurs étapes car l'image finale est plus légère et ne contient pas d'outils ou de fichiers sources inutiles.

### Document docker-compose most important commands

Commandes docker-compose les plus importantes :
- `docker compose up` : Permet de lancer le parcours du fichier `docker-compose.yaml`, si toutefois aucun fichier `docker-compose.override.yaml` n'est pas présent. Dans le cas où ce dernier est présent, il sera utilisé à la place du fichier `docker-compose.yaml`.

Options :

- ```-f``` : Option 'file', permet de spécifier le fichier à utiliser.
- ```-d``` : Option 'detached', de la même manière que pour ```docker run```, permet de lancer la commande en arrière plan.
- ```--build``` : Option 'build', permet de rebuild les conteneurs correspondant aux services déclarés dans le `docker-compose.yml`.

### Document your docker-compose file

Fichier `docker-compose.yml`: 
```yml
version: '3.8'

services:
    backend:
        container_name: backend
        image: dockercourse/backendapi
        build: './backendapi/simple-api-student-main/'
        env_file: .env
        networks:
          - my-network
        depends_on:
          - database

    database:
        container_name: database
        image: dockercourse/postgres
        build: './postgres'
        env_file: .env
        volumes:
          - myvolume:/var/lib/postgresql/data
        networks:
          - my-network

    httpd:
        container_name: httpd
        image: dockercourse/httpd
        build: './httpd'
        ports:
          - 80:80
        networks:
          - my-network
        depends_on:
          - backend

networks:
    my-network:

volumes:
    myvolume:
```

Ce dernier, plus souple en termes de sécurité, permet d'accéder à la base de données depuis la machine hôte ou via adminer.

Chaque service est relié au même réseau, afin que les conteneurs puissent communiquer entre eux. 

La plupart d'entre eux nécessitent la mise en place d'un fichier `.env`. Il contient les variables d'environnement pour les services (mot de passe, nom d'utilisateur, etc). Ainsi, nous pouvons utiliser le même fichier `docker-compose.yaml` pour chaque environnement. Cela tout en le versionnant dans le git sans aucune information sensible.

### Document your publication commands and published images in dockerhub.

Pour publier, vous devez être connecté au hub de Docker :
```bash
docker login
```

Ensuite, il faut que les images docker soient construites. Vous pouvez le faire en utilisant :
```bash
docker build -t dockercourse/httpd /path/to/httpd-dockerfile-folder
```

Ensuite, vous devez marquer l'image avec votre nom d'utilisateur docker hub et le nom de l'image que vous voulez publier. C'est également une bonne pratique de marquer l'image avec un numéro de version.
```bash
docker tag dockercourse/httpd username/dockercourse-httpd:1.0.0
```

Ensuite, vous pouvez pousser l'image vers le hub docker.
```bash
docker push username/dockercourse-httpd:1.0.0
```

