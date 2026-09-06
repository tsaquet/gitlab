# Les releases

## To Do

Dans ce TP, nous allons tester la capacité de Gitlab à mettre à disposition des release d'une application.

- Créez un nouveau projet, un Hello World en Golang
- Dans un stage **build** ajoutez une étape qui construit votre programme
  - Pour tester les instance runners de gitlab.com, vous pouvez différencier deux builds, un pour Windows et un pour Linux
- Dans un stage **upload** ajoutez une étape qui permet de téléverser vos exécutables dans la Package Registry
- Dans un stage **release** ajoutez une étape pour déclencher une release

## Solution

- Vous pouvez trouver les fichiers [main.go](./files/main.go) et [go.mod](./files/go.mod) qui constituent un Hello World.
- Utilisez le [.gitlab-ci.yml](./files/.gitlab-ci.yml)
- Réactivez les instance runners du projet (Settings > CI/CD > Runners) si vous les aviez désactivés : les tags `saas-linux-small-amd64` et `saas-windows-medium-amd64` les sélectionnent
  - Sur un compte gratuit, ils ne fonctionnent qu'après la vérification d'identité (carte bancaire, sans débit), et consomment les 400 compute minutes mensuelles
  - Les runners Windows sont toujours en beta et lents à démarrer
- Créez un tag (Code > Tags > New tag, ou `git tag 1.0.0 && git push --tags`)
- Regardez les builds se dérouler !
- La release apparaît dans **Deploy > Releases**, les binaires dans **Deploy > Package Registry**
- Variante : le mot-clé `release:` du `.gitlab-ci.yml` remplace l'appel direct à `release-cli` (voir l'exercice 12)
