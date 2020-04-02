# Comments about /etc/docker/daemon.json creation

- Cf. [Docker recommanded post installation steps](https://docs.docker.com/install/linux/linux-postinstall/)
- Cf. [Docker recommanded Run your app in production](https://docs.docker.com/get-started/orchestration/)
- Cf. [Project documentation](https://github.com/youpiwaza/ansible-install-web-server/tree/master/ansible/roles/docker-installation/tasks)
- Cf. [Docker daemon.json documentation](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)
- Cf. [Docker bench security](https://github.com/docker/docker-bench-security)

As json typed files don't allow comments, here are explainations.

## Configure default logging driver

[Docker documentation / Logs](https://docs.docker.com/install/linux/linux-postinstall/#configure-default-logging-driver)

Explicitly specify the logging driver & configure it to prevent massive logs files.

```json
# No, deprecated
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

*Update*: [Docker security bench](https://www.digitalocean.com/community/tutorials/how-to-audit-docker-host-security-with-docker-bench-for-security-on-ubuntu-16-04#step-3-%E2%80%94-correcting-docker-daemon-configuration-warnings)
 recommands centralized and remote logging (2.12), so no json-file.

It has no file size nor the other related options.

```json
{
  "log-driver": "syslog",
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

## Disable inter-container communication

[Docker security Cheat sheet #5](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-5---disable-inter-container-communication---iccfalse)

By default inter-container communication (icc) is enabled - it means that all containers can talk with each other (using docker0 bridged network).

If icc is disabled (icc=false) it is required to tell which containers can communicate using --link=CONTAINER_NAME_or_ID:ALIAS option.

```json
{
  "icc": false
}
```

## Set default ulimits to containers

- [Docker security Cheat sheet #7](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-7---limit-resources-memory-cpu-file-descriptors-processes-restarts)
- [Docker docs / ulimits](https://docs.docker.com/engine/reference/commandline/run/#set-ulimits-in-container---ulimit)
- [Oracle docs / ulimits](https://docs.oracle.com/en/operating-systems/oracle-linux/docker/ch04s16.html)

Docker defaults 64000.

```json
{
  "default-ulimits": {
    "nofile": {
        "Name": "nofile",
        "Hard": 128,
        "Soft": 256
      },
      "nproc" : {
        "Name": "nproc",
        "Hard": 32,
        "Soft": 64
      }
  },
}
```

## Set the logging level to at least INFO

- [Docker security Cheat sheet #10](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-10---set-the-logging-level-to-at-least-info)
- [Docker docs / daemon](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)

Docker default is "", set it to info.

```json
{
  "log-level": "info"
}
```

## Disable userland proxy

[Docker security bench tutorial recos](https://www.digitalocean.com/community/tutorials/how-to-audit-docker-host-security-with-docker-bench-for-security-on-ubuntu-16-04#step-3-%E2%80%94-correcting-docker-daemon-configuration-warnings)

```json
{
  "userland-proxy": false
}
```

## Prevent containers from gaining new privileges

[Docker security bench tutorial recos](https://www.digitalocean.com/community/tutorials/how-to-audit-docker-host-security-with-docker-bench-for-security-on-ubuntu-16-04#step-3-%E2%80%94-correcting-docker-daemon-configuration-warnings)

```json
{
  "no-new-privileges": true
}
```

## Enforce Docker Content Trust

Docker security bench reco > [Docker doc](https://docs.docker.com/engine/security/trust/content_trust/#enabling-dct-within-the-docker-enterprise-engine)

Only in docker EE $

```json
{
  "content-trust": {
    "mode": "enforced"
  }
}
```
