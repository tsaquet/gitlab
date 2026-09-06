# Ma première chaîne CD !

## To Do

- Créer un nouveau projet appelé tp_8 (sur gitlab.com ou dans votre instance locale)
- Créer un runner "Docker" pour ce projet (Settings > CI/CD > Runners > New project runner, tag `conteneur`), depuis votre poste via un conteneur Docker par exemple
- Créer un conteneur Docker nginx qui affiche une page index contenant le message de votre choix
- Créer un pipeline Gitlab (ie un .gitlab-ci.yml) en 3 stages qui :  
  - dans un stage tests, vérifie la présence du fichier index.html
  - dans un stage build, build l'image Docker et la push dans la registry du repository
  - dans un stage livraison, déploie cette image (uniquement sur la branche main)


## Solution

https://docs.gitlab.com/user/packages/container_registry/build_and_push_images/#use-gitlab-cicd  
https://docs.gitlab.com/ci/variables/predefined_variables/  
https://docs.gitlab.com/ci/yaml/yaml_optimization/  

L'ensemble des fichiers contenus dans le dossier files/

- Le runner Docker

Il doit pouvoir lancer `docker build` : le plus simple est de monter la socket Docker de l'hôte dans les jobs (voir `volumes` dans [config.toml](./files/config.toml)).
Le jeton `glrt-…` de `config.toml` est celui fourni par l'interface lors de la création du runner.

```bash
docker run -d --name docker-runner --restart unless-stopped \
  -v gitlab-runner-docker:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  gitlab/gitlab-runner:ubuntu
docker exec -it docker-runner gitlab-runner register --non-interactive \
  --url https://gitlab.com --token glrt-xxxxxxxxxxxxxxxxxxxx \
  --executor docker --docker-image docker:cli \
  --docker-volumes /var/run/docker.sock:/var/run/docker.sock \
  --name docker-runner
```

- L'authentification à la registry

Le job utilise `$CI_REGISTRY_USER` / `$CI_REGISTRY_PASSWORD`, c'est-à-dire le **CI job token** du pipeline : aucun jeton à créer, il n'a accès qu'au projet courant et expire avec le job.

- Le déploiement

Le conteneur est lancé sur la machine qui porte le runner (via la socket Docker). `docker rm -f` avant `docker run` permet de relancer le job.
`rules` remplace `only` (déprécié).

