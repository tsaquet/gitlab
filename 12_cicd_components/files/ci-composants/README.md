# ci-composants

Composants CI/CD de la formation Gitlab CI/CD.

## docker-build

Construit une image Docker à partir d'un Dockerfile et la pousse dans la Container Registry du projet, en s'authentifiant avec le CI job token.

```yaml
include:
  - component: $CI_SERVER_FQDN/<namespace>/ci-composants/docker-build@1.0.0
    inputs:
      stage: build
      image_name: $CI_REGISTRY_IMAGE
      tag: $CI_COMMIT_REF_SLUG
      dockerfile: Dockerfile
```

| Input | Défaut | Description |
|---|---|---|
| `stage` | `build` | Stage dans lequel placer le job |
| `image_name` | `$CI_REGISTRY_IMAGE` | Nom complet de l'image, registry incluse |
| `tag` | `$CI_COMMIT_REF_SLUG` | Tag de l'image |
| `dockerfile` | `Dockerfile` | Chemin du Dockerfile |

Prérequis : un runner avec l'exécuteur Docker et accès au démon Docker (socket montée).
