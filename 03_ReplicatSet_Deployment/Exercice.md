# Exercice : Préparer le déploiement d'une Application 

Dans cet exercice nous allons créer des fichiers de configuraton de type "ReplicaSet" "Deployment" pour chaque service de l'application  "App, A sample 12 Facter Application", publiée par Kelsey Hightower (Google). 

`https://github.com/kelseyhightower/app`



A la fin de l'exercice nous aurons : 
- Ecrit les fichiers de configuration pour "auth" et "hello" ainsi que celui du frontend Nginx "frontend"
- Crée les : ReplicaSet et Deployment 
- Abordé l'orchestration, en particulier la scalabilité sur Kubernetes.

![Application APP](https://github.com/Treeptik/training-k8s-resources/blob/master/03_ReplicatSet_Deployment/images/Treeptik-training-k8s-exo3-1.jpg?raw=true "Application APP")



A la fin de l'exercice l'Application APP ne sera pas encore utilisable. Les différentes parties ne seront pas exposées : 
- sur internet pour le frontend, 
- en interne pour les composants "auth" et "hello" 

l'Application APP ne sera pas encore en mesure recevoir et de traiter la requête utlisateur : cette thématique sera traitée dans les parties suivantes. 
  


spec:
   replicas: 3
   selector:
     matchExpressions:
      - {key: app, operator: In, values: [soaktestrs, soaktestrs, soaktest]}
      - {key: teir, operator: NotIn, values: [production]}
  template:
     metadata: