# Ma première chaîne CD !

## To Do

- Créer un nouveau projet appelé tp_8 dans votre instance gitlab locale
- Créer un conteneur Docker nginx qui affiche une page index contenant le message de votre choix
- Créer un pipeline Gitlab (ie un .gitlab-ci.yml) en 3 stages qui :  
  - dans un stage tests, vérifie la présence du fichier index.html
  - dans un stage build, build l'image Docker et la push dans la registry du repository
  - dans un stage livraison, déploie cette image (uniquement sur la branche main)


## Solution

https://docs.gitlab.com/ee/user/packages/container_registry/index.html#build-and-push-by-using-gitlab-cicd  
https://docs.gitlab.com/ee/ci/variables/predefined_variables.html  
https://docs.gitlab.com/ee/ci/yaml/yaml_optimization.html#yaml-anchors-for-scripts  


L'ensemble des fichiers contenus dans le dossier files/
