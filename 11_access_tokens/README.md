# Les jetons d'accès

## To Do

- Créer un jeton d'accès personnel **à permissions fines** :
  - limité au projet créé dans le TP "Gitlab discovery"
  - autorisant uniquement la lecture des issues et la lecture / écriture du dépôt
  - expirant dans une semaine
- Avec `curl` et ce jeton, exécuter 2 appels à l'API REST :
  - lister les issues du projet
  - essayer de créer un nouveau groupe "Mon groupe" : que se passe-t-il ?
- Utiliser ce même jeton comme mot de passe pour un `git push` en HTTPS
- Créer un **Project Access Token** (rôle Developer, scope `read_registry`) et vérifier qu'il permet un `docker login`
- Regarder la colonne **Last used**
- Faire tourner (rotate) le jeton personnel via l'API, vérifier que l'ancien ne fonctionne plus
- Révoquer le Project Access Token

## Solution

https://docs.gitlab.com/security/tokens/  
https://docs.gitlab.com/auth/tokens/fine_grained_access_tokens/  
https://docs.gitlab.com/user/profile/personal_access_tokens/  
https://docs.gitlab.com/user/project/settings/project_access_tokens/  

### Le jeton personnel à permissions fines

Avatar > **Edit profile** > **Access tokens** > **Add new token**, puis choisir le type **fine-grained** (si le choix n'est pas proposé, l'instance n'a pas encore la fonctionnalité : utiliser un jeton classique avec les scopes `read_api` et `write_repository`).

- Token name : `tp-tokens`
- Expiration date : dans 7 jours (une date est obligatoire depuis Gitlab 16.0)
- Where : **Selected projects and groups**, choisir le projet du TP discovery
- Permissions :
  - Issues : *read*
  - Repository : *read* et *write*

Le jeton commence par `glpat-` et n'est affiché qu'une fois : le stocker dans une variable d'environnement.

```bash
$ export TOKEN=glpat-xxxxxxxxxxxxxxxxxxxx
$ export PROJECT=<namespace>%2F<project>     # le chemin du projet, encodé (/ devient %2F), ou son id numérique

# Qui suis-je ? (marche avec tout jeton)
$ curl -s --header "PRIVATE-TOKEN: $TOKEN" "https://gitlab.com/api/v4/user" | jq '.username'

# Lister les issues du projet : OK
$ curl -s --header "PRIVATE-TOKEN: $TOKEN" "https://gitlab.com/api/v4/projects/$PROJECT/issues" | jq '.[].title'
"Ma première issue"

# Créer un groupe : refusé, le jeton n'a pas cette permission
$ curl -s --request POST --header "PRIVATE-TOKEN: $TOKEN" --header "Content-Type: application/json" \
     --data '{"path": "mon-groupe", "name": "Mon groupe"}' "https://gitlab.com/api/v4/groups"
{"message":"403 Forbidden"}

# Avec un jeton classique de scope "api", le même appel aurait réussi : c'est tout l'intérêt des jetons fins.
```

Le même jeton sert de mot de passe pour git en HTTPS (le mot de passe du compte est refusé, surtout avec la 2FA) :

```bash
$ git clone https://gitlab.com/<namespace>/<project>.git
Username: <username>
Password: glpat-xxxxxxxxxxxxxxxxxxxx

$ echo "token test" >> README.md && git commit -am "Test push with a fine-grained token" && git push
# Pour ne pas retaper le jeton : git config --global credential.helper store (en clair !) ou un credential manager
```

### Le Project Access Token

Dans le projet : **Settings > Access tokens > Add new token**

- Token name : `registry-reader`
- Role : Developer
- Scopes : `read_registry`

Un utilisateur "bot" `project_<id>_bot_<hash>` apparaît dans **Manage > Members** : c'est lui qui porte le jeton, il survit au départ de celui qui l'a créé.

```bash
$ docker login registry.gitlab.com -u registry-reader -p glpat-yyyyyyyyyyyyyyyyyyyy
Login Succeeded

# En lecture seule : un push est refusé
$ docker push registry.gitlab.com/<namespace>/<project>/test
denied: requested access to the resource is denied
```

Le nom d'utilisateur est indifférent pour un jeton (n'importe quelle valeur non vide fonctionne), c'est le jeton qui identifie.

### Last used

Retourner dans la liste des jetons : la colonne **Last used** indique la date du dernier appel. Un jeton jamais utilisé depuis longtemps est un jeton à révoquer.

### Rotation

```bash
# Le nouveau jeton est renvoyé, l'ancien est révoqué immédiatement. La date d'expiration repart pour une semaine.
$ curl -s --request POST --header "PRIVATE-TOKEN: $TOKEN" \
     "https://gitlab.com/api/v4/personal_access_tokens/self/rotate" | jq '.token'
"glpat-zzzzzzzzzzzzzzzzzzzz"

# L'ancien ne fonctionne plus
$ curl -s --header "PRIVATE-TOKEN: $TOKEN" "https://gitlab.com/api/v4/user"
{"message":"401 Unauthorized"}

$ export TOKEN=glpat-zzzzzzzzzzzzzzzzzzzz
```

La rotation existe aussi pour les project / group access tokens (`/projects/:id/access_tokens/:token_id/rotate`).

### Révocation

**Settings > Access tokens**, bouton **Revoke** sur `registry-reader`. Le `docker login` échoue ensuite avec `unauthorized`.

### Pour aller plus loin : le CI job token

**Settings > CI/CD > Job token permissions** : la liste des projets dont les pipelines ont le droit d'accéder à ce projet avec leur `$CI_JOB_TOKEN`.
Depuis Gitlab 18.0 cette liste est obligatoire : par défaut seul le projet lui-même y figure. C'est ce jeton qui sert dans le TP "Première chaîne CI/CD" pour pousser dans la registry, sans rien créer.
