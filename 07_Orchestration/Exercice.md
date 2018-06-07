# Exercice : Les PODs en ligne de commande avec le CLI "kubectl"  

Dans cet exercice nous allons manipuler un composant central de Kubernetes : le POD, avec le CLI "kubectl".

__Les - Vos - Applications sont au coeur de Kubernetes__.

Chaque fonction de l'application (Frontend, Backend... ) peut avantageusement utiliser la containeurisation - avec Docker par exemple. 

C'est là que le système Kubernetes rentre en scène. Son rôle: piloter harmonieusement l'ensemble les containers formant l'application. 
Pour maintenir le cap tout au long du cycle de vie de l'application, Kubernetes utilise un composant logique : le POD - aussi appelé "charge de travail applicative" ou "application workload". Le fait que ces PODs soient une abstraction logicielle permet le pilotage programmatique et donc la fourniture des capacités d'automatisation et d'orchestration tout au long du cycle de vie de l'application. Ceci en parfaite correspondance avec les bonnes pratiques DevOps. 

Cet exercice se focalise sur le set de commandes *kubectl* et nous soumettrons directement via le CLI les options a passer au système K8s. 
Plus tard nous envisagerons le provisionning d'Applications, qui consiste à automatiser le déploiement des PODs et des objects nécessaires à leur orchestration, au travers de fichiers de configurations .yaml . 


## Objectifs 

A la fin de l'exercice nous aurons déployé __en ligne de commande via *kubectl*__ une application simple telle que :

<a href="https://github.com/Treeptik/training-k8s-by-treeptik/tree/laurent-branch/tmp_exos/hello-node/images/Treeptik-training-k8s-exo1.jpg" target="_blank"><img src="https://github.com/Treeptik/training-k8s-by-treeptik/tree/laurent-branch/tmp_exos/hello-node/images/Treeptik-training-k8s-exo1.jpg" 
alt="Architecture Cible de l'exercice" width="240" height="180" border="10" /></a>

La charge de travail déployée via un POD est conteneurisé en utilisant Docker. Kubernetes sera en mesure de provisionner plusieurs replicas. L'image du container est disponible sur :

`/chemin/du/repository `
A définir. 

Sources : 

`https://github.com/Treeptik/training-k8s-by-treeptik/tree/laurent-branch/tmp_exos/hello-node/sources`


## 1 - Cluster et Creation du POD 

En vous aidant de la completion venant avec CLI et de la documentation de *kubetcl* :
 
- Se connecter au Cluster K8s de votre environnement de lab provisionné précedemment. 
1. Que donne le résultat des commandes 

`kubectl cluster-info`
`kubectl cluster-info dump`

2. Quel est le résultat de la commande ? Commentez : 
 
`kubectl get pods `

3. Quel est le namespace par default ? Créer un Namespace dédié à l'exercice : "exercice1". La suite de l'exercice se déroulera dans ce nouveau namespace. 
4. *lancer* un POD avec *kubectl* : 
* nom du POD : hello-world 
* image : hub.docker/.../hello-node:v1 ( à déterminer)
* port : 8080

La sortie de la commande devrait afficher : 

`deployment "hello-node" created `

Kubernetes a crée automatiquement un "Deployment". Cet objet, qui sera etudié plus tard, est utilisé pour orchestrer le POD hello-world tout au long de son cycle de vie : create, scale... .

5. Quel est le résultat de la commande : 
 
`kubectl get pods `

Etudions les informations données par la sortie de ces commandes ? En particulier : 

6. Identifier la Colonne "NAME", à votre avis que représente la première ligne *hello-node-.....* ?
7. Identifier la Colonne "READY", à votre avis à quoi correspondent les chiffres : "1/1" ? 

- Le Status du POD est normalement en mode '"Running". En vous aidant de la documentation (trouvée sur Internet)
8. Trouver la liste de tous les *status* possible dans le cycle de vie d'un POD. 
9. Trouver la commande qui affiche les logs temps reels du pod "hello-node"
10. Est-il possible de voir les logs du conteneur executé dans le pod "hello-node" - comment ? Trouver la commande. 


## 2 - Scalabilité sur Kubernetes.  

Le provisionning à la demande est une fonctionnalité clef de K8s. Il est facile de scaler son application - et de fournir la capacité juste nécessaire - pour répondre aux requêtes utilisateurs en fontion des pics de charge. L'approche de K8s est déclarative : il s'agit de "déclarer" le nombre d'instance que l'on souhaite avoir en tout temps. 

11. Scaler l'application de 1 à 5 replicas 
12. Quel est le composant qui gère les replicas ? 

- Obtenir la descprition du nouveau Deployement en tapant la commande : 

`kubectl get deployment ` 

13. Quelle est la signification des colonnes "DESIRED", "CURRENT", "UP-TO-DATE", "AVAILABLE"
14. Obtenir la liste de toutes les instances du POD.  





