# Docker commands templates

With all flags related to security measures. See docker bench security > /ansible/roles/docker-installation/tasks/docker-bench-security.yml

See ansible/roles/docker-installation/tasks/test-alpine-curated-container.yml for the ansible syntax.

## Docker run commands

```bash
# Don't run on docker default bridge network, create a custom one
> docker network create --driver bridge custom-bridge

# Create an Alpine container
> docker run \
  --cap-drop=ALL \
  --cpu-shares=1024 \
  --health-cmd='stat /etc/passwd || exit 1' \
  --health-interval=10s \
  --health-retries=3 \
  --health-start-period=0s \
  --health-timeout=10s \
  -i \
  --memory-reservation="128m" \
  --memory="256m" \
  --memory-swap -1 \
  --name test-alpine \
  --network custom-bridge \
  --pids-limit 200 \
  --read-only \
  --restart=on-failure:5 \
  --security-opt="no-new-privileges:true" \
  --security-opt apparmor=docker-default \
  --security-opt seccomp=/etc/docker/seccomp-profiles/default-docker-profile.json \
  -t \
  --user 1003:1003 \
  -v /home/the_docker_peon/tests-volume:/home:ro \
  -w /home \
  alpine \
  /bin/ash

# Create a Nginx container
# Need container's /etc/nginx/nginx.conf to be overriden:
#   cf. ansible/roles/docker-installation/templates/nginx-custom-user.j2
# You can use the dedicated role for installation tests:
#   cf. ansible/roles/docker-installation/tasks/test-nginx-curated-container.yml

# Note: Don't run it on port 80, prefer > 1024 then rertoute through Traefik
# LATER: Fix default apparmor nginx profile
> docker run \
  --cap-drop=ALL \
  --cpu-shares=1024 \
  --health-cmd='stat /etc/nginx/nginx.conf || exit 1' \
  --health-interval=10s \
  --health-retries=3 \
  --health-start-period=0s \
  --health-timeout=10s \
  -i \
  --memory-reservation="128m" \
  --memory="256m" \
  --memory-swap -1 \
  --name test-nginx \
  --network custom-bridge \
  -p 80:80 \
  --pids-limit 200 \
  --restart=on-failure:5 \
  --security-opt="no-new-privileges:true" \
  --security-opt apparmor=docker-default \
  --security-opt seccomp=/etc/docker/seccomp-profiles/default-docker-profile.json \
  --sysctl net.ipv4.ip_unprivileged_port_start=0 \
  -t \
  --user 1003:1003 \
  -v /home/the_docker_peon/tests-volume/nginx/nginx.conf:/etc/nginx/nginx.conf:ro \
  -v /home/the_docker_peon/tests-volume/nginx/content/test-min-static-site/site:/usr/share/nginx/html:ro \
  -w /usr/share/nginx/html \
  nginx:alpine
```

## Volumes backup

- Based on [server-related-tutorials repo > /01-docker/03-develop-with-docker/02-volumes/README.md](https://github.com/youpiwaza/server-related-tutorials/tree/master/01-docker/03-develop-with-docker/02-volumes)
  - **More details, tests & walkthrough** in the same folder/notes-problemes-et-tests-backup-docker-volumes.md.
- [Doc docker backup volumes](https://docs.docker.com/storage/volumes/#backup-restore-or-migrate-data-volumes)
- [Doc tar](https://doc.ubuntu-fr.org/tar)

### Preparation & debug

It might be usefull (at least when doing manually) to inspect a container's volume.

Use an interactive temp container binded to the desired volume (or use `volume-from` to target a specific container).

```bash
## Get volume name
docker volume ls

## Refer to container initialisation (~.yml in DOCKER_PEON > clients or core to get info on containers volume dedicated folders)
# docker run --rm -i -t  \
#   --mount source=VOLUME_TO_INSPECT_NAME,destination=/FOLDER_IN_VOLUME_TO_INSPECT \
#   -w /FOLDER_IN_VOLUME_TO_INSPECT \
#   alpine:latest \
#   /bin/ash

## Example
docker run --rm -i -t  \
  --mount source=test-helloDeux-logs,destination=/home/volumeContent \
  -w /home/volumeContent \
  alpine:latest \
  /bin/ash

## Also possible to simply docker exec if you got the container name
docker ps
docker exec -it CONTAINER_NAME_OR_ID bash
# ~alpine
docker exec -it CONTAINER_NAME_OR_ID bin/ash
```

### Create a backup

Logic:

1. Run a temporary container to connect both the volume to save and a temp volume binded to the host.
2. Make an archive from the volume's content to save, and store it in the temp volume
3. Through the bind, the archive will be on the host
4. Remove both temp container & volume

```bash
## Create volume archive on host
##    Note: '--userns=host' is mandatory to remove user mapping to docker_peon (defined in docker_daemon.json)
##    Note: the 'backup/' folder is a temp folder, and isn't relevant, but MUST be different from the folder from the volume to backup
##    Use with DOCKER_GUY, or BUILDER_GUY (with sudo)
# docker run --rm -i -t  \
#   --mount source=VOLUME_TO_BACKUP_NAME,destination=/FOLDER_IN_VOLUME_TO_BACKUP \
#   --mount type=bind,source=/HOST_FOLDER_WHERE_TO_STORE_THE_ARCHIVE,destination=/backup \
#   --userns=host \
#   -w /home/backup \
#   alpine:latest \
#   tar -cvf /backup/SAVE_NAME.tar "/FOLDER_IN_VOLUME_TO_BACKUP"

## Example
## $(pwd) is current directory, be sure to have mandatory rights (eg: be in /home_DOCKER_GUY/)
docker run --rm -i -t  \
  --mount source=test-helloDeux-logs,destination=/home/volumeContent \
  --mount type=bind,source=$(pwd),destination=/backup \
  --userns=host \
  -w /home/backup \
  alpine:latest \
  tar -cvf /backup/test-example---backup---$(date +%Y-%m-%d--%Hh%Mm%Ss).tar "/home/volumeContent"
## Assuming we are in /home/DOCKER_GUY/tests/backups-volumes, this will generate:
##    /home/DOCKER_GUY/tests/backups-volumes/test-example---backup---2021-05-28--07h27m40s.tar
```

#### Test/Debug archive

🚨  Be careful with --strip XXX which removes XXX surrounding folders. If mounted volume got a tree folder (ex: /var/log/) this can be problematic.
    Files & folder won't be restored to the good place. It's better off to not use it (.. and don't use a working directory with the temp container)

```bash
## Untar, without surronding /home folder
tar -xvf test-example---backup---2021-05-28--07h27m40s.tar --strip 1
# home/volumeContent/
# home/volumeContent/nginx/
# home/volumeContent/nginx/error.log
# home/volumeContent/nginx/access.log
# home/volumeContent/newContent.txt
# home/volumeContent/php-fpm.log

ls -la
# drwxr-xr-x 3 DOCKER_GUY DOCKER_GUY  4096 mai   27 11:41 volumeContent
```

### Restore a volume

Re-inject untared archive into an existing volume.

Note : This will replace existing files but **not erase new files**.
       Best practice would be to create a new volume, populate it with the backup and remap the container accordingly.
       This also allow the previous volume (..and datas) to persist.

```bash
##    Note: the 'backup/' folder is a temp folder, and isn't relevant, but MUST be different from the folder from the volume to restore
# docker run --rm -i -t  \
#   --mount source=VOLUME_TO_RESTORE_NAME,destination=/FOLDER_IN_VOLUME_TO_RESTORE \
#   --mount type=bind,source=/HOST_SAVE_ARCHIVE_LOCATION,destination=/home/backup \
#   --userns=host \
#   alpine:latest \
#   tar -xvf /home/backup/SAVE_NAME.tar

## Don't use working directory unless you use --strip XXX # depending on the tree folder. It's simplier to replace from /
#   -w /FOLDER_IN_VOLUME_TO_RESTORE \
#   tar -xvf /home/backup/SAVE_NAME.tar --strip 1


## Example
docker run --rm -i -t  \
  --mount source=test-helloDeux-logs,destination=/home/volumeContent \
  --mount type=bind,source=$(pwd),destination=/home/backup \
  --userns=host \
  alpine:latest \
  tar -xvf /home/backup/updated-volume.tar
```

## Docker compose commands

See [dedicated repo](https://github.com/youpiwaza/docker-compose-curated-example).

## Docker swarm commands

See ansible/roles/docker-swarm-installation/tasks/test-curated-swarm-service.yml
