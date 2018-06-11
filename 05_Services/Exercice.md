# Exercice : Les Services 

Dans les parties précedentes nous avons lancé des ReplicatSets et des Deployments (qui eux-mêmes s'appuis sur des ReplicatSets ) pour executer les Pods de containers qui constituent les composants de l'application. 

Dans cet exercice nous allons __mettre en ligne l'application APP__ en utilisant les Services Kubernetes pour exposer chaque Pod : 
- Sur Internet pour le Pod "frontend", 
- En interne pour les Pods "auth" et "hello". 

A la fin de cet exercice nous aurons 
- Ecrit les fichier de configuration des Services 
- ..
- ..


## Introduction 

Nous avons vu lors des exercices précédents que chaque Pod possède une adresse IP. 

1. Rappeler la commande pour obtenir l'adresse IP d'un POD 

Il s'agit ici de décrire les mécanismes mis en oeuvre lors du lancement de chaque deployment afin de mettre en evidence la nécéssité des Services Kubernetes. 

![POD](https://github.com/Treeptik/training-k8s-resources/blob/master/05_Services/images/Treeptik-training-k8s-exo5-workflow_pod.jpg?raw=true "POD")

- 1 L'utililsateur invoque kubectl pour lancer un deployment via un fichier de configuration,
- 2 kubectl transmet la requete à l'API kube-apiserver
- 3 kube-apiserver sauvegarde les informations liées au Pod à deployer dans etcd
- 4 kube-scheduler recherche (dans etcd) si des Pods correpondant sont déjà lancés : il n'en trouve pas. 
- 5 kube-scheduler décide sur quel Node le Pod sera lancé. Pour cela kube-scheduler se base :  
- 5.1 : sur les spécifications du Pod lui même - par exemple si un container du Pod a besoin de disque SSD pour son volume persistant, kube-scheduler selectionnera un Node avec des disques SSD. 
- 5.2 : sur les Pods déjà existants dans le cluster : y a t'il des affinités avec le Pods à lancer ? Etc etc. kube-scheduler est "topology-aware"
- 6 kube-scheduler demande à kube-api de lancer le Pod - sur le Node 2 par exemple 
- 7 kube-apiserver invoque kubelet et kube-proxy sur le Node 2 : 
- 7.1 kubelet pour lancer le Pod de container : 
- 7.1.1 - kubelet demande à Docker d'executer le(s) container(e) du Pod 
- 7.1.2 - Docker lance le(s) container(s) 
- 7.2 kube-proxy pour la partie (proxy-)networking :
- 7.2.1 - kube-proxy récupère l'adresse IP du container 
- 7.2.2 - kube-proxy utilise netflilter pour authoriser le forward des flux vers cet IP en invoquant le set de commandes iptables. 

Le Pod est mainteant lancé avec un adresse IP. Afin de maintenir l'état décrit dans le Deployement 

8 - kube-apiserver commande à kube-controller-manger de lancer un Deployement Controler : une boucle qui va s'assurer que le Deployment reste tel que décrit dans 


Finalement, 


