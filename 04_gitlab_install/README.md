https://docs.gitlab.com/ee/install/docker.html

docker save gitlab/gitlab-ee  | gzip > gitlab-ee_docker_image.tar.gz
docker load < gitlab-ee_docker_image.tar.gz

root@gitlab:/assets# gitlab-ctl service-list
alertmanager*
gitaly*
gitlab-exporter*
gitlab-workhorse*
grafana*
logrotate*
nginx*
postgres-exporter*
postgresql*
prometheus*
puma*
redis*
redis-exporter*
sidekiq*
sshd*
root@gitlab:/assets# gitlab-ctl status
run: alertmanager: (pid 1482) 1193s; run: log: (pid 1248) 1237s
run: gitaly: (pid 1493) 1192s; run: log: (pid 534) 1323s
run: gitlab-exporter: (pid 1446) 1195s; run: log: (pid 1101) 1255s
run: gitlab-workhorse: (pid 1430) 1195s; run: log: (pid 924) 1268s
run: grafana: (pid 1586) 1192s; run: log: (pid 1393) 1205s
run: logrotate: (pid 475) 1336s; run: log: (pid 483) 1335s
run: nginx: (pid 939) 1264s; run: log: (pid 953) 1263s
run: postgres-exporter: (pid 1503) 1192s; run: log: (pid 1301) 1231s
run: postgresql: (pid 651) 1318s; run: log: (pid 710) 1315s
run: prometheus: (pid 1455) 1194s; run: log: (pid 1219) 1243s
run: puma: (pid 838) 1282s; run: log: (pid 845) 1281s
run: redis: (pid 491) 1330s; run: log: (pid 499) 1329s
run: redis-exporter: (pid 1448) 1194s; run: log: (pid 1186) 1249s
run: sidekiq: (pid 857) 1276s; run: log: (pid 868) 1274s
run: sshd: (pid 35) 1351s; run: log: (pid 34) 1351s

docker inspect gitlab => See CMD ie /assets/wrapper and read the shell script

Logs : see GITLAB_HOME/logs folder

root@gitlab:/# gitlab-ctl restart nginx
ok: run: nginx: (pid 2269) 0s
root@gitlab:/# gitlab-ctl status nginx
run: nginx: (pid 2269) 11s; run: log: (pid 949) 409s

Stop 

root@gitlab:/# gitlab-ctl stop
ok: down: alertmanager: 0s, normally up
ok: down: gitaly: 1s, normally up
ok: down: gitlab-exporter: 1s, normally up
ok: down: gitlab-workhorse: 0s, normally up
ok: down: grafana: 0s, normally up
ok: down: logrotate: 1s, normally up
ok: down: nginx: 0s, normally up
ok: down: postgres-exporter: 1s, normally up
ok: down: postgresql: 0s, normally up
ok: down: prometheus: 1s, normally up
ok: down: puma: 0s, normally up
ok: down: redis: 0s, normally up
ok: down: redis-exporter: 1s, normally up
ok: down: sidekiq: 0s, normally up
ok: down: sshd: 1s, normally up

Check healthchek

"Healthcheck": {
                "Test": [
                    "CMD-SHELL",
                    "/opt/gitlab/bin/gitlab-healthcheck --fail --max-time 10"
                ],
                "Interval": 60000000000,
                "Timeout": 30000000000,
                "Retries": 5
            },


root@gitlab:/# gitlab-ctl start
ok: run: alertmanager: (pid 2493) 1s
ok: run: gitaly: (pid 2506) 0s
ok: run: gitlab-exporter: (pid 2527) 1s
ok: run: gitlab-workhorse: (pid 2529) 0s
ok: run: grafana: (pid 2541) 0s
ok: run: logrotate: (pid 2555) 1s
ok: run: nginx: (pid 2561) 0s
ok: run: postgres-exporter: (pid 2579) 1s
ok: run: postgresql: (pid 2586) 0s
ok: run: prometheus: (pid 2671) 1s
ok: run: puma: (pid 2686) 0s
ok: run: redis: (pid 2691) 0s
ok: run: redis-exporter: (pid 2697) 1s
ok: run: sidekiq: (pid 2704) 0s
ok: run: sshd: (pid 2712) 1s
