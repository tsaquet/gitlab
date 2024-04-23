# Les environnements

## To Do

Dans ce TP, nous allons tester la capacité de Gitlab à mettre à disposition des release d'une application.

- Créez un nouveau projet, un Hello World en Golang
- Dans un stage **build** ajoutez une étape qui construit votre programme
  - Pour tester les shared runner, vous pouvez différencier deux builds, un pour Windows et un pour Linux
- Dans un stage **upload** ajoutez une étape qui permet de téléverser vos exécutables dans la Package Registry
- Dans un stage **release** ajoutez une étape pour déclencher une release

## Solution

- Vous pouvez trouver les fichiers [main.go](./files/main.go) et [go.mod](./files/go.mod) qui constituent un Hello World.
- Utilisez le [.gitlab-ci.yml](./files/.gitlab-ci.yml)
- Créez un tag
- Regardez les builds se dérouler !
