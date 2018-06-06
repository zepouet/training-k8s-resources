# Exercice : Deployer une Application 

Dans cet exercice nous allons créer des fichiers de "Deployment" pour chaque service de l'application  "App, A sample 12 Facter Application", publiée par Kelsey Hightower (Google). 

`https://github.com/kelseyhightower/app`

![Application APP](https://github.com/Treeptik/training-k8s-by-treeptik/blob/laurent-branch/tmp_exos/02_Pods%20/images/Treeptik-training-k8s-exo2-app.jpg?raw=true "Application APP")

Elle est composée : 

- d'un module d'authentification JWT : "auth" 

Image docker ( fournie en deux versions ) : `kelseyhightower/auth` 

- d'un module "hello" - un serveur web qui une fois l'étape d'authentification passée renvoit "hello" 

Image docker ( fournie en deux versions ) : `kelseyhightower/hello`  

- A ces deux modules, se rajoute un frontend Nginx qui route le traffic aux composants "auth" et "hello"


A la fin de l'exercice nous aurons : 
- Ecrit les fichiers de configuration de chaque Pod correspondant à "auth" et "hello". 
- Lancer les Pods. 
- Inspecter les Pods en utilisant **kubectl** .





