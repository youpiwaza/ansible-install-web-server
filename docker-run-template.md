# Docker run template

With all flags related to security measures. See docker bench security > /ansible/roles/docker-installation/tasks/docker-bench-security.yml

See ansible/roles/docker-installation/tasks/test-alpine-curated-container.yml for the ansible syntax.

## Commands

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
  -v /home/the_docker_peon/tests-volume/nginx/content:/usr/share/nginx/html:ro \
  -w /usr/share/nginx/html \
  nginx:alpine
```
