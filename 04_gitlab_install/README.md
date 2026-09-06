# Installation of Gitlab CE Omnibus

## To Do

- Installer Docker
- Installer & démarrer Gitlab via docker
- Installer & démarrer Gitlab via docker compose
- Créer le compte d'administration

Bonus: docker inspect gitlab => See CMD ie /assets/wrapper, find & read the shell script to understand how Gitlab Omnibus is launched

The image can take a long long time to pull.  
If so, ask your teacher the .tar.gz of the docker image on USB stick. 

Reminders:
docker save gitlab/gitlab-ce | gzip > gitlab-ce_docker_image.tar.gz   
docker load < gitlab-ce_docker_image.tar.gz  

## Solution

- Installer Docker 

Se référer aux TPs Docker

- Installer & démarrer Gitlab via Docker

https://docs.gitlab.com/install/docker/

```bash
export GITLAB_HOME=$HOME/gitlab
docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 2222:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ce:latest

# First start takes several minutes, wait for "healthy":
docker ps

# Edit /etc/hosts to add gitlab.example.com
$ cat /etc/hosts
127.0.0.1  localhost  gitlab.example.com

# Get the root pwd (the file is deleted 24 hours after the first reconfigure!)
docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password

# Go to http://gitlab.example.com:80 in your browser
Login: root
Password: the password you just found

# Change the password (or reset it from the CLI if the file is gone)
docker exec -it gitlab gitlab-rake "gitlab:password:reset[root]"
```

- Installer & démarrer Gitlab via docker compose

```bash
$ cd files
$ docker compose up -d

# Get the root pwd
docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password

# Go to http://gitlab.example.com:80 in your browser
Login: root
Password: the password you just found

# Change the password
```
