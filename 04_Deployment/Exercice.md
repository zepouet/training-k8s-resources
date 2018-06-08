# Exercice : le Controleur Deployment

Dans cet exercice nous allons créer des fichiers de configuraton de type "Deployment" pour chaque service de l'application  "App, A sample 12 Facter Application", publiée par Kelsey Hightower (Google). 

`https://github.com/kelseyhightower/app`


A la fin de l'exercice nous aurons : 
- Ecrit les fichiers de configuration pour "auth" et "hello" ainsi que celui du frontend Nginx "frontend"
- Crée le Deployments pour tous les composants le l'application APP dans sa version 1.0.0
- Scalé le Deployment de l'application APP version 1.0.0 
- Mettre a jour - update - de la version de l'Application APP dans sa version 2.0.0 

![Application APP](https://github.com/Treeptik/training-k8s-resources/blob/master/04_Deployment/images/Treeptik-training-k8s-exo4-1.jpg?raw=true "Application APP")


Les différentes parties ne seront pas exposées : 
- sur Internet pour le frontend, 
- en interne pour les composants "auth" et "hello". 

L'Application APP n'est pas encore en mesure reçevoir et de traiter les requêtes utlisateurs : cette thématique sera abordée dans la partie dédiée aux Services.  

  
## Introduction : 

Les pratiques DevOps impliquent de pouvoir déployer de nouvelles versions applicatives à la volée c'est à dire sans coupure de service. 

L'objet Deployement se positionne pour répondre à ce besoin, en adoptant le point de vue suivant : 
- La stack applicative est composées de Pods, 
- L'update de la stack applicative consiste à l'update des containers des Pods, 
- Un ReplicatSet supervise les Pods, 
- L'Objet Deployement pilote le ReplicatSet des Pods - notamment pendant la mise à jour de la stack applicative. Cette partie est importante : le Deployment utilise son ReplicatSet pour transmettre les ordres à l'API Kubernetes. 

En definisant un objet Deployment avec son fichier de configuration .yaml, on définit l'état du Pod à atteindre, en particulier au niveau de versionning. Le  Deployment assure la transition vers le nouvel état de telle manière à ne pas couper le service applicatif. 

Grâce à l'objet Deployement on peut par exemple : 
- Créer from scracth une stack applicative dans sa version 1
- Scaler le deployment version 1 pour s'adapter à la charge applicative. 
- Mettre à jour cette stack applicative dans une version 2 
- Mettre en pause / reprendre un deployement.... 


### Etude d'un fichier de configuration de Deployment

On se donne le fichier suivant :

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: soaktest
spec:
  replicas: 5
  selector:
    matchLabels:
      app: soaktest
  template:
    metadata:
      labels:
        app: soaktest
    spec:
      containers:
      - name: soaktest
        image: nickchase/soaktest
        ports:
        - containerPort: 80
```

Comme tous les objets de l'API Kubernetes, un Deployment est defini avec les champs **apiVersion**, **kind**, et **metadata**. A cela on ajoute le champs **spec** qui décrit les spécifications d'un Pod à déployer. 

- On retrouve le champs **.spec.selector.** introduit avec l'objet ReplicatSet. Ce champ sera transmit au ReplicatSet associé à ce Deployment. 
- Comme pour le ReplicaSet la bonne pratique est d'utiliser le "Label Selector" et de le préférer aux "Labels" simple présent dans le champs **.spec.template.metadata.labels** . 
- Enfin, l'objet Deployement est à préférer au ReplicatSet car il permet de gérer le cycle de vie de l'application au sein de l'usine logielle : update, rollback de versions... 

1. Donner le nombre de POD qui sera lancé lors de la creation du Deployement avec ce fichier de configuration
2. Trouver la commande kubectl pour créer ce Deployement. 
3. Quelle Image Docker est utilisée  ? 
4. Quel sont les labels associés au Pod qui sera orchestré par ce Deployement ? 
5. Quel est le selector pour ce Deployement ? A comparer avec la réponse du 4. 


### Configuration du Deployement pour les Pods "auth", "hello" et "frontend". 

Pour construire les fichiers de configuration des 3 Deployements nous allons nous appuyer sur les fichiers de configuration des Pod "auth", "hello" et "frontend". 
Ces fichiers on été explicité dans l'exercice 03_ReplicatSet

le fichier de configuration du __Pod "auth"__ est disponible ici 

`https://github.com/Treeptik/training-k8s-resources/blob/master/04_auth/sources/auth_pod.yaml`

le fichier de configuration du __Pod "hello"__ ,construit dans l'exercice précédent, est disponible ici :

`https://github.com/Treeptik/training-k8s-resources/blob/master/03_ReplicatSet/sources/hello_pod.yaml`

le fichier de configuration du __Pod "frontend"__ est donné ici :

`https://github.com/Treeptik/training-k8s-resources/blob/master/03_ReplicatSet/sources/frontend_pod.yaml`

Nous avons ajouté des déclarations supplementaires dans le fichier de configuration du Pod "frontend" : le template du Pod décrit un volume persistant permettant de stocker 
- Le fichier de configuration de nginx pour ecouter sur le port 443 avec une couche SSL/TLS
- Les certificats SSL/TLS ( ! Dépot public ! )

Cette partie sera détaillée dans la suite de la Formation.  

Les ReplicatSets orchestrerons pour commencer:
- Un replica de 1 POD "frontend"
- Un replica de 1 POD "auth"
- Un replica de 1 POD "hello"

On rappelle que 
- Dans le fichier de configuration d'un ReplicatSet, le champ **.spec.template** décrit le template d'un Pod (celui décrit dans le fichier de configuration du Pod)


7. Contruire le fichier de configuration du ReplicatSet pour le Pod "auth"
8. Contruire le fichier de configuration du ReplicatSet pour le Pod "hello"
9. Contruire le fichier de configuration du ReplicatSet pour le Pod "frontend"

10. Creér les 3 ReplicatSets avec les fichiers de configurations complétés précedement.   

11. Quel est le resultat de la commande : `kubectl get pods`
12. Quel est le resultat de la commande : `kubectl get replicasets`

13. Quel est les resultats des commandes : `kubectl describe rs/auth` && `kubectl describe rs/hello` && `kubectl describe rs/frontend`


### Scalabilité 

Il est facile de re-dimensionner à la volée (scale-up ou scale-down) un ReplicatSet. 
Puisque les ReplicatSets ont été crée en utilisant des fichiers de configuration il s'agit de mettre à jour le champ **.spec.replicas** avec la nouvelle valeur cible. 

Les ReplicatSets seront scalés tels que :
- Un replica de 5 POD "frontend"
- Un replica de 5 POD "auth"
- Un replica de 5 POD "hello"

14. Mettre à jour les fichiers de Configurations pour les 3 ReplicatSets
15. Que faut il faire pour que les nouvelles valeures cibles de replicas soient prises en compte ? 
16. Decrivez précisement les nouveaux groupes de PODs : quel est le nom des pods, sur quels noeuds sont-ils executés ?  
17. A votre avis, que ce passe t'il si un Node tombe en panne ? //

__Perpectives__ : 

Cette approche de la scalabilité est limitée : même si le système Kubernetes se charge de toutes les opérations au niveau du cluster pour re-dimensionner à la volée un ReplicatSet de Pod(s), il faut qu'un opérateur, en mode déclaratif, intervienne manuellement pour renseigner les nouveaux fichiers de configuration 

Les __Horizontal Pod Autoscaler (HPA)__ est une ressource de l'API qui permet de re-dimmensionner un ReplicaSet ( ou un Deployment ) de Pods en fonction de l'utilisation d'une métrique. Par défaut, le re-dimmensionnement en fonction de l'utilisation du CPU est implémentée dans la version autoscaling/v1 de Kubernetes. La prise en charge de "customs metrics" est disponible dans la version autoscaling/v2beta1 de l'API. 


