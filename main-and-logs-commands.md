# Main commands

## Host Logs

```bash
# Ubuntu Services KO
sudo systemctl --failed

   # Logs de fail de l'initialisation d'un service (ici apparmor)
   sudo systemctl status apparmor.service

# Logs kernel
sudo tail -f /var/log/kern.log

# Logs autorisations
sudo tail -f /var/log/auth.log

# Autres logs
sudo dmesg
sudo tail /var/log/syslog

# Logs auditd / https://www.digitalocean.com/community/tutorials/how-to-use-the-linux-auditing-system-on-centos-7
sudo nano /var/log/audit/audit.log
   # Display user logins
   sudo ausearch -m LOGIN --start today -i
   # Event related to a file
   sudo ausearch -f /etc/DA_FILE -i
   # Generate summary report / Nombre de fois + tâches
   sudo aureport -x --summary
   # Report Failed events > More interesting
   sudo aureport --failed
   # Report files accessed with system calls and usernames
   sudo aureport -f -i
   ## Analyzing a Process Using autrace
   sudo autrace /bin/date
      # ^ gives a command, reformat it
      sudo ausearch -p 27020 --raw | aureport -f -i

## OS upgrade / do-realease-upgrade package
#     https://wiki.ubuntu.com/DebuggingUpdateManager
# If you already got a SSH connexion & want to see progress, as Ansible doesn't show
# .. in a different terminal
sudo tail -f /var/log/dist-upgrade/main.log
# sudo tail -f /var/log/dist-upgrade/apt.log
# sudo tail -f /var/log/dist-upgrade/term.log

# Récap utilisation de cpu/mémoire
ps -eo pmem,pcpu,rss,vsize,args | sort -k 1 -r | less



# 🚥 Traefik, accès aux logs
#           ..depuis les logs conteneur
docker logs -f core---traefik_traefik_1

# 🚥 Traefik, afficher les logs en direct
#           ..extraire du conteneur
#           Ne pas tail -f, cela fonctionne mais pas de sortie, ~bash planté
docker run                                                              \
      --mount type=volume,source=core---traefik--logs,target=/home/logs \
      --name temp-check-traefik-logs                                    \
      --read-only                                                       \
      --rm                                                              \
      -w /home/logs                                                     \
      alpine                                                            \
      tail traefik-debug.log

#           ..depuis l'intérieur du conteneur
docker run                                                              \
      -i                                                                \
      --mount type=volume,source=core---traefik--logs,target=/home/logs \
      --name temp-check-traefik-logs                                    \
      --read-only                                                       \
      --rm                                                              \
      -t                                                                \
      -w /home/logs                                                     \
      alpine                                                            \
      /bin/ash
>> tail -f traefik-debug.log
>> exit

# Arrêter le conteneur, sera détruit via --rm
docker stop temp-check-traefik-logs

# 🚥 Traefik, accès aux certificats https
docker run                                                                          \
      -i                                                                            \
      --mount type=volume,source=core---traefik--https-certificates,target=/home    \
      --name temp-check-traefik-certificates                                        \
      --read-only                                                                   \
      --rm                                                                          \
      -t                                                                            \
      -w /home                                                                      \
      alpine                                                                        \
      /bin/ash

> cat https/acme.json
```

## Docker logs

Using syslog logging driver, logs are written in `/var/log/syslog`.

```bash
# Last docker daemon logs
sudo cat /var/log/syslog |grep dockerd

# Last docker daemon logs with live refresh
sudo tail -f /var/log/syslog |grep dockerd



## Logs containers / services / stacks
# Note: In order to be able to see logs, you must have log-driver specified either as json-file or jornald
#   https://docs.docker.com/engine/reference/commandline/service_logs/#extended-description
# Debug > specify log driver when using docker service create
#   --log-driver=json-file
# (get service name)
# docker service ls
# Then use on another instance (or in detached mode)
docker logs -f CONTAINER_NAME_OR_ID
docker service logs -f SERVICE_NAME_OR_ID

# Accès direct au conteneur
docker exec -it CONTAINER_NAME_OR_ID bash
# ~alpine
docker exec -it CONTAINER_NAME_OR_ID bin/ash
# with user
docker exec -it -u THE_USER CONTAINER_NAME_OR_ID bin/ash
```

## Docker tests

```bash
# Create an alpine container with a volume inside the dedicated folder, go in with shell and create a file
docker run --name test-alpine --rm -i -t -v /home/THE_DOCKER_PEON/test-volume:/home -w /home alpine /bin/ash
>> touch test
>> exit
cd /home/THE_DOCKER_PEON/test-volume
ll
# File should be created with dedicated namespace, cf. docker installation
#   -rw-r--r-- 1 362144 362144   10 avril  1 13:13 test

# Restart docker daemon
sudo systemctl restart docker
# debug
sudo systemctl status docker.service

# Inspect container health (healthchecks)
sudo docker inspect --format='{{.State.Health.Status}}' CONTAINER_NAME
```

### Clean ps

cf. [docker ps --format](https://stackoverflow.com/a/49973096/12026487)

```bash
## Triées par Status (date de fonctionnement asc = kikaplanté en dernier)
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
## image & taille
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Size}}"

## Triées par noms
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}" | (read -r; printf "%s\n" "$REPLY"; sort -k 1 )

## Sur une page (meilleure visibilité mais pas d'historique)
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}" | (read -r; printf "%s\n" "$REPLY"; sort -k 1 ) | less -S
```

## Performances, conso CPU/RAM/Disk

```bash
## Analyses consos CPU/RAM
# Tous les process
ps -eo pmem,pcpu,rss,vsize,args | sort -k 1 -r | less

# Tous les conteneurs
sudo docker stats

# Un conteneur
sudo docker stats CONTAINER_NAME_OR_ID

# Espace occupé sur le disque dur (images, repos, volumes, docker builds)
docker system df -v
```
