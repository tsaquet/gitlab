# GitLab & CI/CD - Travaux Pratiques

Ce dépôt contient des exercices progressifs pour apprendre Git, GitLab et le CI/CD.

## Prérequis

- Docker et Docker Compose installés
- Un terminal Linux ou macOS (ou WSL sous Windows)
- Connaissances de base en ligne de commande

## Exercices

| # | Dossier | Sujet |
|---|---------|-------|
| 01 | [01_git](./01_git) | Les bases de Git |
| 02 | [02_precommit](./02_precommit) | Pre-commit hooks et détection de secrets |
| 03 | [03_gitlab_discovery](./03_gitlab_discovery) | Découverte de GitLab (compte, SSH, 2FA) |
| 04 | [04_gitlab_install](./04_gitlab_install) | Installation de GitLab CE via Docker |
| 05 | [05_gitlab_administration](./05_gitlab_administration) | Administration de GitLab (utilisateurs, API, backup) |
| 06 | [06_docker](./06_docker) | Docker et Container Registry GitLab |
| 07 | [07_1st_pipeline](./07_1st_pipeline) | Premier pipeline CI/CD avec runners |
| 08 | [08_first_cicd](./08_first_cicd) | Première chaîne CI/CD complète |
| 09 | [09_environments](./09_environments) | Variables d'environnement et branches |
| 10 | [10_release](./10_release) | Gestion des releases (build multi-plateforme) |
| 11 | [11_access_tokens](./11_access_tokens) | Jetons d'accès et jetons à permissions fines |
| 12 | [12_cicd_components](./12_cicd_components) | Composants CI/CD et CI/CD Catalog |

Ordre conseillé pour une formation Gitlab CI/CD : 01, 03, 11, 06, 07, 08, 09, 10, 12.
Les exercices 04 et 05 (installation et administration) sont utilisés dans d'autres formations.

## Notes de version

- Depuis Gitlab 18.0, les *registration tokens* n'existent plus : un runner se crée d'abord dans l'interface, qui fournit un jeton `glrt-…` à passer à `gitlab-runner register --token`. Les exercices 07 et 08 ont été mis à jour en conséquence.
- Les runners hébergés de gitlab.com (exercice 10) nécessitent la vérification d'identité du compte (carte bancaire, sans débit).

## Licence

MIT - Voir [LICENSE](./LICENSE)
