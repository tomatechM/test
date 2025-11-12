# HW5_Mohamed_Mehdi_Atmani\_Matricule_20218934


**Name:** Mohamed Mehdi Atmani

**Matricule \#:** 20218934

**Please write your short answers below and include this as part of your gradescope submission.**

**The title of the report should be HW5_{Your_Name}\_{Matricule_\#}**


## Question 2
* 2 images : l’image "base" et l’image "target" construite à partir de la base.
* Non. Si l’image base n’a pas changé, on n’a pas besoin de la reconstruire — on peut réutiliser l’image base déjà poussée dans l’Artifact Registry. Reconstruire systématiquement la base gaspille du temps car les couches identiques pourraient être réutilisées.
* Utiliser le cache de couches : pré‑télécharger (docker pull) l’image base avant de construire l’image target pour que Docker puisse réutiliser les couches locales ; n’exécuter la construction/push de la base que si ses dépendances ont changé.
    
## Question 4

* Principes pour éviter le téléchargement : 
    - Inclure le modèle dans l'image (télécharger pendant le build Docker) — startup très rapide ; build + image plus lourds.
    - Pré-construire une image "base" qui contient le modèle et la pousser (réutiliser cette base pour toutes les apps). Réduit les rebuilds du modèle.

* Impacts : 
    - Télécharger pendant le BUILD (Dockerfile RUN) : augmente le temps de build et la taille de l’image + coût de stockage/transfer, mais supprime le coût au démarrage (Cloud Run cold start). Recommandé si vous voulez des réponses rapides en production.
    - Pré-construire et pousser une image base : accélère les builds ultérieurs (cache) mais nécessite une rebuild/push quand le modèle change.

## Question 5

* Avantages : 
    - Séparation des responsabilités : front et back ont des préoccupations différentes, facilite le développement et la maintenance.
    - Déploiement indépendant : on peut mettre à jour le frontend sans toucher au backend (et vice‑versa), donc cycles de release plus courts.
    -Isolation des pannes et sécurité : un bug côté frontend n’écrase pas le backend; on peut appliquer des méthodes d’accès différentes.

* Désavantages : 
    - chaque requête frontend→backend ajoute overhead réseau (surtout en cold‑start).
    - faut gérer versions compatibles (contracts/API) entre front et back; déploiements non coordonnés peuvent casser l’app.
    - plusieurs services actifs va causer plus d’instances facturées.

## Question 7

* Pour v1, ça pose peu de problème :
    - v1 utilise un seul modèle fixe. Donc, le charger dans une variable globale au démarrage est simple et efficace.

* Pour v2, ça pose problème :
    * Dans v2, on peut choisir différents modèles par utilisateur. Cependant, si on garde un seul ```model``` global :
        - Les requêtes de différents utilisateurs peuvent utiliser le mauvais modèle.
        - Il faudrait recharger le modèle à chaque requête, donc un coût élevé.

* Ce qu'il faut modifier :
    - Dans le frontend : Envoyer le choix du modèle avec chaque requête ou sauvegarder le choix dans la session et l'inclure dans les requêtes
    - Dans le backend : Il suffit de ne pas utiliser un unique ```model``` pour tous les utilisateurs. Plus précisémment, on peut déployer un service différent par modèle.

## Question 8

* Le code v3 introduit une passerelle API entre le frontend et les backends de modèles
    - Le frontend v3 appelle des routes du type /model/{id}/predict et /models
    - Le backend v3 expose /model/<id>/predict et /models et peut charger un modèle paramétré (MODEL_NAME)
    - La passerelle doit recevoir les requêtes clients et les router vers le bon service modèle

* En quoi c'est différent : 
    - v1/v2 : frontend appelle directement un backend unique (v1 charge un modèle fixe ; v2 change le modèle global via PUT).
    - v3 : séparation explicite, rôle « gateway » vs « services modèles » — découplage, possibilité de multiples backends spécialisés avec un routage dynamique.

* Avantages :
    - Isolation et scalabilité par composant
    - Déploiements indépendants.

* Inconvénients :
    - Plus complexe et plus de code
    - Plus de latence

## Question 9

* Avantages : 
    - La taille du modèle est réduite, ainsi, moins d’espace disque et de bande passante pour pull/push
    - Meilleure latence et débit surtout sur CPU et edge car on a besoin de moins de mémoire vive et le traffic mémoire diminue
    - Exécution plus rapide

* Inconvénients : 
    - Il peut y avoir une perte de précision 
    - Plus complèxe vu qu'il faut ajouter plus étapes

* Sources :
    - [IMB - Quantization overview](https://www.ibm.com/think/topics/quantization)
    - [PyTorch - Quantization](https://pytorch.org/docs/stable/quantization.html)
