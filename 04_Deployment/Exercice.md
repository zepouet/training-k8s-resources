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

Les pratiques DevOps,CI/CD, impliquent de pouvoir déployer de nouvelles versions applicatives à la volée c'est à dire sans coupure de service. 

L'objet Deployement se positionne pour répondre à ce besoin, de pouvoir mettre à jour une stack applicative en adoptant le point de vue suivant : 
- La stack applicative est composées de Pods 
- L'update de la stack applicative consiste à l'update des containers des Pods 
- Un ReplicatSet supervise les Pods 
- L'Objet Deployement pilote le ReplicatSet des Pods - notamment pour et pendant la mise à jour de la stack applicative. Cette partie est importante : le Deployment a besoin de son ReplicatSet pour opérer car il s'appuit dessus pour les appels aux objets de l'API de Kubernetes. 

En definisant un object Deployment avec son fichier de configuration .yaml, on définit l'état du Pod à atteindre en particulier au niveau de versionning. Le  Deployment assure la transition vers le nouvel état de telle manière à ne pas couper le service applicatif. 

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

Comme tous les objets de l'API Kubernetes, un Deployment est defini avec les champs **apiVersion**, **kind**, et **metadata**. A cela on ajoute le champs **spec** qui décrit les spécifications du status d'un Pod à atteindre et à maintenir. 

- Une spécificité du controler ReplicatSet est le champs **.spec.selector.**. Le ReplicatSet gère les Pods dont le label correspond au selector défini dans ce champs.
- Le champ **.spec.template** décrit le template d'un Pod, qui est encapsulé dans la configuration du ReplicaSet. On remarque qu'il s'agit du même shéma que le fichier de configuration d'un Pod. En particulier on retrouve le champ **.spec.template.metadata.labels** qui sera comparé à **.spec.selector.** . Si ces deux champs ne sont pas identiques l'API rejetera la configuration. 


1. Donner le nombre de POD qui sera lancé lors de la creation du ReplicaSet avec ce fichier de configuration
2. Trouver la commande kubectl pour créer ce ReplicatSet. 
3. Quelle Image Docker est utilisée ? 
4. Quel sont les labels associés au Pod qui sera orchestré par ce ReplicaSet ? 
5. Quel est le selector pour ce ReplicaSet ? A comparer avec la réponse du 4. 
6. Ce ReplicatSet est-il destiné à la Production ? 


### Configuration du ReplicaSet pour le Pod "auth"

Le ReplicatSet orchestrera :
- Un replica de 3 PODs "auth"

On rappelle que 
- Dans le fichier de configuration d'un ReplicatSet, le champ **.spec.template** décrit le template d'un Pod

le fichier de configuration du Pod "auth" est disponible ici 

`https://github.com/Treeptik/training-k8s-resources/blob/master/02_Pods/sources/auth_pod.yaml`


7. En vous basant sur les données précédentes, completez le fichier de configuration du ReplicatSet pour le Pod "auth"

```
apiVersion: extensions/v1beta1
kind: ReplicatSet
metadata:
  name: auth
spec:
  replicas: 
  selector:
    matchLabels:
      app: auth
  template:
    metadata:
      labels:
        app: 
        track:
    spec:
      containers:
        - name: 
          image: 
          ports:
            - name: 
              containerPort: 
          resources:
            limits:
              cpu: 
              memory: 
```

8. Creér le ReplicatSet avec le fichier de configuration complété précedement  
9. Quel est le resultat de la commande : `kubectl get pods`
10. Quel est le resultat de la commande : `kubectl get replicasets`

### Configuration des ReplicaSets pour le Pod "hello" et le Pod "frontend"

Les deux ReplicatSets pour les Pods "hello" et le Pod "frontend" orchestrerons :
- Un replica de 3 PODs. 

le fichier de configuration du __Pod "auth"__ ,construit dans l'exercice précédent, est disponible ici :

`https://github.com/Treeptik/training-k8s-resources/blob/master/02_Pods/sources/auth_pod.yaml`

le fichier de configuration du __Pod "frontend"__ est donné ici :

`https://github.com/Treeptik/training-k8s-resources/blob/master/03_ReplicatSet_Deployment/sources/frontend_pod.yaml`

Vous pourrez remarquer des déclarations supplementaires dans le fichier de configuration du Pod "frontend" : le template du Pod décrit un volume persistant permettant de stocker 
- Le fichier de configuration de nginx pour ecouter sur le port 443 avec une couche SSL/TLS
- Les certificats SSL/TLS ( ! dangeureux sur un dépot public.. ! )

Cette partie sera détaillée dans la suite de la Formation 
 
11. Ecrire le fichier de configuration du ReplicatSet pour les Pods "hello" et le Pod "frontend"
12. Creér le ReplicatSet avec le fichier de configuration précedent 
13. Quel est le resultat de la commande : `kubectl get pods`
14. Quel est le resultat de la commande : `kubectl describe rs/hello` && `kubectl describe rs/frontend`


## Deployment Kubernetes

### Etude d'un fichier de configuration de Deployment



On se donne le fichier suivant :

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: soaktest
spec:
  replicas: 5
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



### Configuration des Deployments pour les Pod "auth", "hello" et "frontend"




//Jeudi AM : finir deployment + commencer 04_Services
//Jeudi PM ; finir 04_Services et commencer 06_Orchestration 
//Vendredi : finir 06_Orchestration
// Next week : 05_Stockage + tests


