# TP NoSQL - Partie 3: Intégration de Kibana

## Objectifs
- Utiliser Kibana comme interface de visualisation.

### Étape 1: Intégrer les données factices

1. Installer et Configurer Kibana
Je crée un fichier docker-compose.yml et inclue Kibana

2. Démarrer les services avec la commande : `docker-compose up -d`

3. Importer les données factices :
```
Invoke-WebRequest -X POST "localhost:9200/flights/_bulk" -H "Content-Type: application/json" --data-binary @D:\Dropbox\Julia\Dev\NoSQL\Elasticsearch_Kibana\Partie3\flights.json
```

Réponse du terminal : 

```
Invoke-WebRequest : Impossible de lier le paramètre « Headers ». Impossible de convertir la valeur « Content-Type: application/json » du type « System.String » en type « System.Collections.IDictionary ».
Au caractère Ligne:1 : 61
+ ... lhost:9200/flights/_bulk" -H "Content-Type: application/json" --data- ...
+                                  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument : (:) [Invoke-WebRequest], ParameterBindingException
    + FullyQualifiedErrorId : CannotConvertArgumentNoMessage,Microsoft.PowerShell.Commands.InvokeWebRequestCommand
```
Ce que j'ai tenté de faire pour ne plus avoir cette erreur c'est d'envoyer le contenu du fichier avec cette commande : 
```
$bulkData = Get-Content -Path "D:\Dropbox\Julia\Dev\NoSQL\Elasticsearch_Kibana\Partie3\flights.json" -Raw
Invoke-WebRequest -Method Post -Uri "http://localhost:9200/_bulk" `
  -ContentType "application/json" `
  -Body $bulkData
```

Mais le ternimal me renvoie une nouvelle erreur : 
```
Invoke-WebRequest : Impossible de se connecter au serveur distant
Au caractère Ligne:2 : 1
+ Invoke-WebRequest -Method Post -Uri "http://localhost:9200/_bulk" `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : System.Net.WebException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand
```

Ce que je fais : je tente de relancer la comande `docker-compose up -d`.
Réponse du terminal : 
```
[+] Running 0/0
 - Container es003   Creating                                                                                                                                                                                    0.0s 
 - Container kibana  Recreate                                                                                                                                                                                    0.0s 
Error response from daemon: Conflict. The container name "/ea2a6c06b50a_kibana" is already in use by container "29add89fba33a9464738bb458153546a0dbfdf8557eac74bb3d755732f17a1d0". You have to remove (or rename) that container to be able to reuse that name.
```

J'ai l'impression que l'erreur vient du fait que mon fichier docker-compose.yml est trop semblable au premier que j'ai créé pour la partie 1 du TP. Il y a beaucoup de meme nom de variable qui fait que cela ne marche surement pas. Je crée un nouveau fichier docker-compose qui a de nouveau nom de variable pour ne pas collisionner avec le permier fichier docker-compose.yml céé.

Exécuter de nouveau la commande : `docker-compose up -d`

Réponse du terminal : 
```
[+] Running 1/0
 ✔ Network partie3_esnet_part3       Created                                                                                                                                                                     0.1s 
[+] Running 8/8ie3_esdata_part3_01"  Creating                                                                                                                                                                    0.0s 
 ✔ Network partie3_esnet_part3       Created                                                                                                                                                                     0.1s 
 ✔ Volume "partie3_esdata_part3_01"  Created                                                                                                                                                                     0.0s 
 ✔ Volume "partie3_esdata_part3_02"  Created                                                                                                                                                                     0.0s 
 ✔ Volume "partie3_esdata_part3_03"  Created                                                                                                                                                                     0.0s 
 ✔ Container es_part3_02             Started                                                                                                                                                                     0.2s 
 ✔ Container es_part3_01             Started                                                                                                                                                                     0.2s 
 ✔ Container kibana_part3            Started                                                                                                                                                                     0.2s 
 ✔ Container es_part3_03             Started      
 ```

 Maintenant je refais ma commande pour envoyer le contenu du fichier flights.json :
 ```
 $bulkData = Get-Content -Path "D:\Dropbox\Julia\Dev\NoSQL\Elasticsearch_Kibana\Partie3\flights.json" -Raw
>> Invoke-WebRequest -Method Post -Uri "http://localhost:9200/_bulk" `
>>   -ContentType "application/json" `
>>   -Body $bulkData
```

Réponse du terminal : 
```
Invoke-WebRequest : Impossible de se connecter au serveur distant
Au caractère Ligne:2 : 1
+ Invoke-WebRequest -Method Post -Uri "http://localhost:9200/_bulk" `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : System.Net.WebException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand
```