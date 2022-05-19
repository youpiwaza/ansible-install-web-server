# Post install manual verification

## Logs

```bash
# Services KO
sudo systemctl --failed

   # Logs de fail de l'initialisation d'un service (ici apparmor)
   sudo systemctl status apparmor.service

# Logs kernel
sudo nano /var/log/kern.log

# Logs autorisations
sudo nano /var/log/auth.log

# Autres logs
sudo dmesg
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
```

## Security

Automatic security benchmark [Docker bench security](https://github.com/docker/docker-bench-security)

Run once **before** starting services, **AND once after**.
