# Comments about /etc/docker/daemon.json creation

- Cf. [Docker recommanded post installation steps](https://docs.docker.com/install/linux/linux-postinstall/).
- Cf. [Project documentation](https://github.com/youpiwaza/ansible-install-web-server/tree/master/ansible/roles/docker-installation/tasks).
- Cf. [Docker daemon.json documentation](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file).

As json typed files don't allow comments, here are explainations.

## Configure default logging driver

[Docker documentation](https://docs.docker.com/install/linux/linux-postinstall/#configure-default-logging-driver)

Explicitly specify the logging driver & configure it to prevent massive logs files

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
