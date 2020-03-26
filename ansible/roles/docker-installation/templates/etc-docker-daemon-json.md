# Comments about /etc/docker/daemon.json creation

- Cf. [Docker recommanded post installation steps](https://docs.docker.com/install/linux/linux-postinstall/)
- Cf. [Docker recommanded Run your app in production](https://docs.docker.com/get-started/orchestration/)
- Cf. [Project documentation](https://github.com/youpiwaza/ansible-install-web-server/tree/master/ansible/roles/docker-installation/tasks)
- Cf. [Docker daemon.json documentation](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)

As json typed files don't allow comments, here are explainations.

## Configure default logging driver

[Docker documentation / Logs](https://docs.docker.com/install/linux/linux-postinstall/#configure-default-logging-driver)

Explicitly specify the logging driver & configure it to prevent massive logs files.

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "5",
    "labels": "production_status",
    "env": "os,customer"
  }
}
```

## Keep containers alive during daemon downtime

[Docker documentation / Live restore](https://docs.docker.com/config/containers/live-restore/)

Enable live restore.

Don't place true between quotes : ~~"true"~~ despite ansible jinja recommandations, or **docker daemon won't reboot**.

```json
{
  "live-restore": true
}
```

## Isolate containers with a user namespace

[Docker documentation / userns-remap](https://docs.docker.com/engine/security/userns-remap/)

Create a dedicated unprivileged user *the_docker_peon* to execute docker containers in his own namespace.

Different user from *the_docker_guy* to prevent *docker* group privileges.

```json
{
  "userns-remap": "the_docker_peon:the_docker_peon"
}
```
