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
- en interne pour les composants "auth" et "hello". 

l'Application APP ne sera donc pas encore en mesure recevoir et de traiter les requêtes utlisateurs : cette thématique sera traitée dans les parties suivantes. 
  
## Introduction : 

Le système Kubernetes a connaissance du "status" du Pod, comme nous l'avons vu dans l'exercice 1, mais ne sait pas si ce status est le bon pour accomplir la fonction applicative. 

Il est donc necessaire d'expliquer à Kubernetes, en mode déclaratif, de quelle façon on souhaite que les Pods evoluent ensemble : on parle d'orchestration des Pods. Une fois l'orchestration décrite, il est aussi nécessaire de contrôler que les Pods continuent à fournir le service applicatif attendu. Kubernetes utilise des composants programmables dédiés à ces tâches : Les "Controllers". Les ReplicatSet et les Deployments en sont des exemples.  


## ReplicaSet Kubernetes

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

Comme tous les objets de l'API Kubernetes, un ReplicaSet est defini avec les champs **apiVersion**, **kind**, et **metadata**. A cela on ajoute le champs **spec** qui décrit les spécifications du status d'un Pod à atteindre et à maintenir lors de l'orchestration. 

- Une spécificité du controler ReplicatSet est le champs **.spec.selector.**. Le ReplicatSet gère les Pods dont le label correspond au selector défini dans ce champs.
- Le champ .spec.template décrit le template d'un Pod, qui est encapsulé dans la configuration du ReplicaSet. On remarque qu'il s'agit du même shéma que le fichier de configuration d'un Pod. En particulier on retrouve le champ **.spec.template.metadata.labels** qui sera comparé à **.spec.selector.** . Si ces deux champs ne sont pas identiques l'API rejetera la configuration. 


1. Donner le nombre de POD qui sera lancé lors de la creation du ReplicaSet avec ce fichier de configuration
2. Trouver la commande kubectl pour créer ce ReplicatSet. 
3. Quelle Image Docker est utilisée ? 
4. Quel sont les labels associés au Pod qui sera orchestré par ce ReplicaSet ? 
5. Quel est le selector pour ce ReplicaSet ? A comparer avec la réponse du 4. 
6. Ce ReplicatSet est-il destiné à la Production ? 


### Configuration du ReplicaSet pour le Pod "auth"

On rappelle le fichier de configuration du Pod "auth"

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
- Le container exposera sur le port en 80/HTTP
- Le squellette de fichier de configuration est donné ci-après 

7. Compléter le fichier de configuration du ReplicatSet

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

8. Creér le ReplicatSet avec le fichier de configuration précedent 
9. Quel est le resultat de la commande : `kubectl get pods`
10. Quel est le resultat de la commande : `kubectl get replicasets`

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
- Le container exposera sur le port 80/HTTP
 
11. Ecrire le fichier de configuration du ReplicatSet
12. Creér le ReplicatSet avec le fichier de configuration précedent 
13. Quel est le resultat de la commande : `kubectl get pods`
14. Quel est le resultat de la commande : `kubectl get replicasets`
