# Main commands

## Host Logs

```bash
# Ubuntu Services KO
> sudo systemctl --failed

   # Logs de fail de l'initialisation d'un service (ici apparmor)
   > sudo systemctl status apparmor.service

# Logs kernel
> sudo nano /var/log/kern.log

# Logs autorisations
> sudo nano /var/log/auth.log

# Autres logs
> sudo dmesg
> sudo tail /var/log/syslog

# Logs auditd / https://www.digitalocean.com/community/tutorials/how-to-use-the-linux-auditing-system-on-centos-7
> sudo nano /var/log/audit/audit.log
   # Display user logins
   > sudo ausearch -m LOGIN --start today -i
   # Event related to a file
   > sudo ausearch -f /etc/DA_FILE -i
   # Generate summary report / Nombre de fois + tâches
   > sudo aureport -x --summary
   # Report Failed events > More interesting
   > sudo aureport --failed
   # Report files accessed with system calls and usernames
   > sudo aureport -f -i
   ## Analyzing a Process Using autrace
   > sudo autrace /bin/date
      # ^ gives a command, reformat it
      > sudo ausearch -p 27020 --raw | aureport -f -i

```

## Docker logs

Using syslog logging driver, logs are written in `/var/log/syslog`.

```bash
# Last docker daemon logs
> sudo cat /var/log/syslog |grep dockerd

# Last docker daemon logs with live refresh
> sudo tail -f /var/log/syslog |grep dockerd



## Logs services / stacks
# Note: In order to be able to see logs, you must have log-driver specified either as json-file or jornald
#   https://docs.docker.com/engine/reference/commandline/service_logs/#extended-description
# Debug > specify log driver when using docker service create
#   --log-driver=json-file
# (get service name)
# > sudo docker service ls
# Then use on another instance (or in detached mode)
> sudo docker service logs -f SERVICE_NAME_OR_ID
```

## Docker tests

```bash
# Create an alpine container with a volume inside the dedicated folder, go in with shell and create a file
> docker run --name test-alpine --rm -i -t -v /home/THE_DOCKER_PEON/test-volume:/home -w /home alpine /bin/ash
>> touch test
>> exit
> cd /home/THE_DOCKER_PEON/test-volume
> ll
# File should be created with dedicated namespace, cf. docker installation
#   -rw-r--r-- 1 362144 362144   10 avril  1 13:13 test

# Restart docker daemon
> sudo systemctl restart docker
# debug
> sudo systemctl status docker.service

# Inspect container health (healthchecks)
> sudo docker inspect --format='{{.State.Health.Status}}' CONTAINER_NAME
```
