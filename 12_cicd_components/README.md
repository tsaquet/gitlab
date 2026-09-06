# Composants CI/CD

## To Do

Reprendre le pipeline du site **nginx** (TP "Première chaîne CI/CD") et en extraire le job de build / push Docker sous forme de composant.

1. Créer un projet `ci-composants`, avec un `README.md`, déclaré comme **CI/CD Catalog resource**
2. Écrire `templates/docker-build.yml` avec les inputs `stage`, `image_name`, `tag`, `dockerfile`
3. Tester le composant depuis son propre projet (inclusion via `@$CI_COMMIT_SHA`)
4. Publier la version `1.0.0` (job de release sur tag) et la retrouver dans **Explore > CI/CD Catalog**
5. Consommer le composant en `1.0.0` depuis le projet nginx
6. Ajouter un input `push` (boolean), publier `1.1.0`, comparer `@1` et `@~latest`

## Solution

https://docs.gitlab.com/ci/components/  
https://docs.gitlab.com/ci/inputs/  
https://docs.gitlab.com/ci/components/examples/  

Les fichiers sont dans [files/ci-composants](./files/ci-composants) (le projet de composants) et [files/nginx-project](./files/nginx-project) (le consommateur).

### 1. Le projet de composants

- Créer le projet `ci-composants` (blank project, avec README)
- Le README est **obligatoire** : c'est lui qui est affiché dans le catalogue
- **Settings > General > Visibility, project features, permissions** : activer **CI/CD Catalog resource** (tout en bas). Le projet doit avoir une description.
- Le runner : soit créer un nouveau runner Docker pour ce projet (voir TP 08), soit rendre le runner existant disponible pour ce projet (**Settings > CI/CD > Runners**, sur le projet nginx : modifier le runner, puis dans `ci-composants` l'activer dans "Available project runners"). Le runner doit avoir la socket Docker.

### 2. Le composant

[templates/docker-build.yml](./files/ci-composants/templates/docker-build.yml) : un bloc `spec:` en tête de fichier, séparé du reste par `---`, puis le job.

- Sans `default`, un input est obligatoire : ici tous ont une valeur par défaut, le composant fonctionne sans aucun input
- `$[[ inputs.xxx ]]` est remplacé à la **création du pipeline**, avant toute exécution : on peut s'en servir pour le `stage` ou le nom du job, ce qu'une variable ne permet pas
- `$CI_REGISTRY_IMAGE` ne sera résolu que dans le job (c'est une variable, pas un input)
- Le nom du composant est le nom du fichier : `docker-build`

### 3. Tester

[.gitlab-ci.yml](./files/ci-composants/.gitlab-ci.yml) du projet de composants : il inclut son propre composant à la version `$CI_COMMIT_SHA`, ce qui teste toujours le commit courant.

```yaml
include:
  - component: $CI_SERVER_FQDN/$CI_PROJECT_PATH/docker-build@$CI_COMMIT_SHA
    inputs:
      image_name: $CI_REGISTRY_IMAGE/test
```

Un [Dockerfile](./files/ci-composants/Dockerfile) minimal donne quelque chose à construire. Après le push, le pipeline doit passer et l'image `…/ci-composants/test:main` apparaître dans **Deploy > Container Registry**.

Erreurs classiques :

- `component ... not found` : mauvais chemin, ou le fichier n'est pas dans `templates/`
- `Build component error: Spec must be a valid json schema` : le bloc `spec:` est mal formé (par exemple `inputs:` vide)
- `This GitLab CI configuration is invalid: ... inputs: unknown` : un input passé n'existe pas dans le `spec`

### 4. Publier

Le job `create-release` du même `.gitlab-ci.yml` ne tourne que sur un tag et utilise le mot-clé `release:`.

```bash
$ git tag 1.0.0
$ git push --tags
```

Le pipeline de tag rejoue le test, puis crée la release. Le composant est alors visible dans **Explore > CI/CD Catalog** avec son README, sa version et la liste de ses inputs (générée depuis le `spec`).

Contraintes : le tag doit être une version sémantique (`1.0.0`, pas `v1.0.0` ni `1.0`), et le projet doit être un *catalog resource* avant la release, sinon la release est créée mais le composant n'est pas publié.

### 5. Consommer

[nginx-project/.gitlab-ci.yml](./files/nginx-project/.gitlab-ci.yml) : le job `build-push` est remplacé par un `include: component:`.

```yaml
include:
  - component: gitlab.com/<namespace>/ci-composants/docker-build@1.0.0
    inputs:
      stage: build
```

Le job injecté s'appelle `build-docker`, il apparaît dans le stage `build` comme avant. Le reste du pipeline (test, deploy) ne change pas, `$IMAGE_TAG` reste cohérent car le composant utilise les mêmes valeurs par défaut (`$CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG`).

Sur une branche `feature`, l'image est taguée `feature`.

### 6. Faire évoluer

[templates/docker-build.yml.v1.1.0](./files/ci-composants/templates/docker-build.yml.v1.1.0) ajoute :

```yaml
    push:
      type: boolean
      default: true
      description: "Pousser l'image dans la registry après le build"
```

et dans le script : `- test "$[[ inputs.push ]]" = "false" || docker push ...`.

Renommer ce fichier en `docker-build.yml`, commit, tag `1.1.0`, push.

Dans le projet nginx :

| Inclusion | Après `1.1.0` | Après un futur `2.0.0` |
|---|---|---|
| `@1.0.0` | reste en 1.0.0 | reste en 1.0.0 |
| `@1` | passe en 1.1.0 | reste en 1.1.0 (dernière 1.x) |
| `@~latest` | passe en 1.1.0 | passe en 2.0.0, y compris les changements incompatibles |

En production, figer au moins la version majeure.

### Pour aller plus loin

Ajouter un composant officiel dans le projet nginx :

```yaml
include:
  - component: gitlab.com/components/secret-detection/secret-detection@~latest
```

Il ajoute un job `secret_detection` dans le stage `test` (il faut que ce stage existe, ou passer l'input `stage`). Comparer avec l'ancienne syntaxe `include: template: Jobs/Secret-Detection.gitlab-ci.yml`.
