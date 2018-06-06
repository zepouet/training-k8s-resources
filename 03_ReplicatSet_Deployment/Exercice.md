# Exercice : Préparer le déploiement d'une Application 

Dans cet exercice nous allons créer des fichiers de configuraton de type "ReplicaSet" "Deployment" pour chaque service de l'application  "App, A sample 12 Facter Application", publiée par Kelsey Hightower (Google). 

`https://github.com/kelseyhightower/app`



A la fin de l'exercice nous aurons : 
- Ecrit les fichiers de configuration pour "auth" et "hello" ainsi que celui du frontend Nginx "frontend"
- Crée les : ReplicaSet et Deployment pour chaque composant applicatif
- Abordé l'orchestration, en particulier la scalabilité sur Kubernetes.

![Application APP](https://github.com/Treeptik/training-k8s-resources/blob/master/03_ReplicatSet_Deployment/images/Treeptik-training-k8s-exo3-1.jpg?raw=true "Application APP")


A la fin de l'exercice l'Application APP ne sera pas encore utilisable. Les différentes parties ne seront pas exposées : 
- sur Internet pour le frontend, 
- en interne pour les composants "auth" et "hello" 

l'Application APP ne sera donc pas encore en mesure recevoir et de traiter la requête utlisateur : cette thématique sera traitée dans les parties suivantes. 
  
## Introduction : 

Que se passe-til lorsque un POD s'éteint, par exemple si le Node éxécutant le POD tombe en panne ? 
Le système Kubernetes a connaissance du "status" du Pod, comme nous l'avons vu dans l'exercice 1, mais ne sait pas si ce status est le bon pour accomplir la fonction applicative. 

Il est donc necessaire d'expliquer à Kubernetes, en mode déclaratif, de quelle façon on souhaite que les Pods evoluent ensemble : on parle d'orchestration des Pods. Une fois l'orchestration décrite il est aussi néssaire de controler que les Pods continuent à fournir le service applicatif attendu. Kubernetes utilise des composants programmables dédié à ces tâches : Les "Controllers". Les ReplicatSet et les Deployments en sont des exemples.  


## ReplicaSet 

### Etude d'un fichier de configuration de ReplicatSet

On se donne le fichier suivant : 

```
apiVersion: extensions/v1beta1
 kind: ReplicaSet
 metadata:
   name: soaktestrs
 spec:
   replicas: 3
   selector:
     matchLabels:
       app: soaktestrs
   template:
     metadata:
       labels:
         app: soaktestrs
         environment: dev
     spec:
       containers:
       - name: soaktestrs
         image: nickchase/soaktest
         ports:
         - containerPort: 80
```

1. Quel est le nombre de POD qui sera lancé lors de la creation du ReplicaSet avec ce fichier de configuration
2. Trouver la commande kubectl pour créer ce ReplicatSet. 
3. Quelle Image Docker est uitlisée ? 
5. Ce ReplicatSet est-il destiné à la Production ? 


### Configuration du ReplicaSet pour le Pod "auth"

On rappelle le fichier de configuration du POd "auth"

```
apiVersion: v1
kind: Pod
metadata:
  name: auth
  labels:
    app: auth
    track: stable_1.0.0
spec:
  containers:
    - name: auth
      image: kelseyhightower/auth:1.0.0
      ports:
        - name: http
          containerPort: 8080
      resources:
        limits:
          cpu: 0.2
          memory: "10Mi"
```


Le ReplicatSet orchestrera 
- Un replica de 3 POD "auth"
- Chaque POD étant composé de 1 container Docker dont l'image est kelseyhightower/auth:2.0.0
- Le container exposera le port en 80/HTTP
- Le squellette de fichier de configuration est donné ci-après 

6. Compléter le fichier de configuration du ReplicatSet

```
apiVersion: extensions/v1beta1
kind: 
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
        app: auth
        track: stable_2.0.0
    spec:
      containers:
        - name: auth
          image: 
          ports:
            - name: http
              containerPort: 
          resources:
            limits:
              cpu: 0.2
              memory: "10Mi"
```

7. Creér le ReplicatSet avec le fichier de configuration précedent 
8. Quel est le resultat de la commande : `kubectl get pods`
9. Quel est le resultat de la commande : `kubectl get replicasets`

### Configuration du ReplicaSet pour le Pod "hello"

On rappelle le fichier de configuration du Pod "hello"

```
apiVersion: v1
kind: Pod
metadata:
  name: hello
  labels:
    app: hello
    track: stable_1.0.0
spec:
  containers:
    - name: hello
      image: kelseyhightower/hello:1.0.0
      ports:
        - name: http
          containerPort: 80
      resources:
        limits:
          cpu: 0.3
          memory: "50Mi"
```


Le ReplicatSet orchestrera 
- Un replica de 3 POD "hello"
- Chaque POD étant composé de 1 container Docker dont l'image est kelseyhightower/hello:2.0.0
- Le container exposera le port 80/HTTP
 
10. Ecrire le fichier de configuration du ReplicatSet
11. Creér le ReplicatSet avec le fichier de configuration précedent 
12. Quel est le resultat de la commande : `kubectl get pods`
13. Quel est le resultat de la commande : `kubectl get replicasets`
