# TP 3

## Document your inventory and base commands

La commande suivante permet d'afficher la distribution de chaque machine de l'inventaire :
```bash
ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
```
- `all` : Option all, permet de préciser que toutes les applications de l'inventaire sont concernées par la commande. 
- `-i inventories/setup.yml` : Option -i, permet de préciser le fichier d'inventaire à utiliser.
- `-m setup` : Option -m, indique le module à utiliser.
- `-a "filter=ansible_distribution*"` : Option -a, permet de préciser l'argument à passer au module, dans ce cas, le filtre à appliquer. Le filtre est utilisé pour afficher uniquement la distribution des machines.

La commande suivante permet de supprimer le package `httpd` à l'aide du gestionnaire de package `yum` :
```bash
ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become
```
- `all` : Option all, permet de préciser que toutes les applications de l'inventaire sont concernées par la commande. 
- `-i inventories/setup.yml` : Option -i, permet de préciser le fichier d'inventaire à utiliser.
- `-m yum` : Option -m, indique le module à utiliser.
- `-a "name=httpd state=absent"` : Option -a, permet de préciser l'argument à passer au module, dans ce cas, le paquet est `httpd` et l'état `absent` signifie que le paquet sera supprimé s'il existe.

## Document the playbook

Le playbook est disponbile ici : [here](ansible/playbook.yml)

## Document container tasks

Chaque conteneur Docker est défini de la même manière. Les différences concernent le nom du conteneur, les variables d'environnement, le port à exposer et enfin l'image à conteneuriser.

## Ansible-vault

- Création d'un fichier vault : `ansible-vault create path/to/vault.yml`
- Modification d'un fichier vault : `ansible-vault edit path/to/vault.yml`
- Visualisation d'un fichier vault : `ansible-vault view path/to/vault.yml`

Les variables d'environnement présentes dans le fichier vault sont chiffrées et peuvent être utilisées selon la syntaxe `{{ My_VAR }}`.

A chaque déploiement, il sera nécessaire de préciser l'option `--ask-vault-pass` afin que le fichier vault puisse être déchiffré.

## Github Actions

La Github Action permettant le lancement du déploiement est disponible ici : [here](../github/workflows/deploy.yml).

## Frontend Nginx

Afin de centraliser la configuration au sein de notre fichier vault, j'ai opté pour la mise en place d'un reverse proxy au niveau du serveur web du frontend. Ce dernier redirige l'ensemble des requêtes HTTP dont le point de terminaison commence par `/api` au reverse proxy apache, point d'entrée de notre serveur. Cela permet de supprimer la présence de variables d'environnements VueJS ne pouvant pas être transmises lors de la construction du conteneur. Ces variables d'envrionnements sont reportées dans la configuration Nginx pouvant être adaptée à la construction du conteneur.

## Extras

Le load balancer est fonctionnel et dispatche les requêtes entre les deux instances docker de l'API.