# TP NoSQL - Partie 1: Installation et Configuration du Cluster Elasticsearch

## Objectifs
- Créer un cluster Elasticsearch sur un seul serveur via Docker.
- Comprendre l'architecture de Elasticsearch et les principes de clustering.

## Prérequis
- Docker installé sur la machine. Si ce n'est pas le cas, téléchargez et installez Docker Desktop depuis le [site officiel de Docker](https://www.docker.com/products/docker-desktop).

## Étapes Réalisées

### Étape 1: Choix de l'Environnement

Pour simplifier la gestion et l'isolation des services, j'ai choisi d'utiliser Docker pour créer et gérer le cluster Elasticsearch.

### Étape 2: Installation de Elasticsearch via Docker

J'ai utilisé Docker Compose pour gérer facilement plusieurs conteneurs Elasticsearch. 

1. Créez un fichier `docker-compose.yml` dans un répertoire de travail avec le contenu suivant :

    ```yaml
    version: '3'
    services:
      es01:
        image: docker.elastic.co/elasticsearch/elasticsearch:7.12.0
        container_name: es01
        environment:
          - node.name=es01
          - cluster.name=es-docker-cluster
          - discovery.seed_hosts=es02,es03
          - cluster.initial_master_nodes=es01,es02,es03
          - bootstrap.memory_lock=true
          - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
        ulimits:
          memlock:
            soft: -1
            hard: -1
        volumes:
          - esdata01:/usr/share/elasticsearch/data
        ports:
          - 9200:9200
        networks:
          - esnet

      es02:
        image: docker.elastic.co/elasticsearch/elasticsearch:7.12.0
        container_name: es02
        environment:
          - node.name=es02
          - cluster.name=es-docker-cluster
          - discovery.seed_hosts=es01,es03
          - cluster.initial_master_nodes=es01,es02,es03
          - bootstrap.memory_lock=true
          - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
        ulimits:
          memlock:
            soft: -1
            hard: -1
        volumes:
          - esdata02:/usr/share/elasticsearch/data
        networks:
          - esnet

      es03:
        image: docker.elastic.co/elasticsearch/elasticsearch:7.12.0
        container_name: es03
        environment:
          - node.name=es03
          - cluster.name=es-docker-cluster
          - discovery.seed_hosts=es01,es02
          - cluster.initial_master_nodes=es01,es02,es03
          - bootstrap.memory_lock=true
          - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
        ulimits:
          memlock:
            soft: -1
            hard: -1
        volumes:
          - esdata03:/usr/share/elasticsearch/data
        networks:
          - esnet

    volumes:
      esdata01:
        driver: local
      esdata02:
        driver: local
      esdata03:
        driver: local

    networks:
      esnet:
    ```

### Étape 3: Configuration du Cluster

Le fichier `docker-compose.yml` ci-dessus configure trois instances d'Elasticsearch (es01, es02, es03) pour fonctionner en mode cluster. Chaque instance est configurée avec des variables d'environnement nécessaires pour la formation du cluster.

### Étape 4: Démarrage du Cluster

Pour démarrer le cluster, exécutez la commande suivante dans le répertoire contenant le fichier `docker-compose.yml` :

`docker-compose up -d`

```
[+] Running 10/3
 ✔ es01 Pulled                                                                                                                                              59.1s 
 ✔ es03 Pulled                                                                                                                                              59.1s 
 ✔ es02 7 layers [⣿⣿⣿⣿⣿⣿⣿]      0B/0B      Pulled                                                                                                           59.1s 
[+] Running 7/7
 ✔ Network partie1_esnet      Created                                                                                                                        0.0s 
 ✔ Volume "partie1_esdata01"  Created                                                                                                                        0.0s 
 ✔ Volume "partie1_esdata02"  Created                                                                                                                        0.0s 
 ✔ Volume "partie1_esdata03"  Created                                                                                                                        0.0s 
 ✔ Container es01             Started                                                                                                                        2.4s 
 ✔ Container es02             Started                                                                                                                        2.4s 
 ✔ Container es03             Started                                                                                                                        2.4s 
```

### Étape 5: Vérification du Cluster

1. Pour vérifier que le cluster fonctionne correctement, utilisez la commande suivante pour accéder aux journaux d'un des nœuds :
`docker logs es01`

2. On peut aussi vérifier le statut du cluster en accédant à l'API REST d'Elasticsearch :
`curl -X GET "localhost:9200/_cluster/health?pretty"`
Cette commande devrait retourner des informations sur l'état du cluster, comme le nombre de nœuds et l'état global du cluster :
```
{
  "cluster_name" : "es-docker-cluster",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 3,
  "number_of_data_nodes" : 3,
  "active_primary_shards" : 0,
  "active_shards" : 0,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
```

### Architecture du Cluster

Le cluster Elasticsearch est composé de trois nœuds (es01, es02, es03) configurés pour découvrir et se connecter les uns aux autres via les variables d'environnement spécifiées dans le fichier docker-compose.yml. Le cluster utilise un réseau Docker nommé esnet pour permettre la communication entre les nœuds.

## Conclusion

En suivant ces étapes, on peut configurer et démarrer un cluster Elasticsearch sur une machine unique en utilisant Docker. Le fichier `docker-compose.yml` fourni permet de déployer facilement le cluster et de le gérer efficacement.