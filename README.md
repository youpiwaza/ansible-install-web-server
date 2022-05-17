# Ansible cookbook to install my web server

Install a web server through ansible, based on personnal [researches](https://github.com/youpiwaza/notes-installation-serveur-web-docker) and [tests](https://github.com/youpiwaza/server-related-tutorials).

cf. [dedicated repo](https://github.com/youpiwaza/server-related-tutorials/tree/master/02-ansible) for ansible installation, setup and tests :

Requirements :

- [A working terminal](https://github.com/youpiwaza/install-dev-env)
- A [working SSH access](https://github.com/youpiwaza/server-related-tutorials/tree/master/02-ansible/01-configuration-ssh) through terminal (private key & ssh-agent on local machine, & public key on server)
- Be careful on your login configuration (hosts) if you edited the SSH port (default: 22)

Note: I'm letting this repo public for educationnal purposes, and obfuscate real ids files, but if you want to use it, feel free to replace *_not_so_real files with your ids :)

## Usage

Working on Windows WSL 2, personnal notes for my setup

See [how I setup my dev environnement](https://github.com/youpiwaza/install-dev-env) if you are interested.

```bash
## Access project
# cd /mnt/c/Users/Patolash/Documents/_dev/__dev_current/ansible-install-web-server/ansible/
cd /mnt/c/Users/masam/Documents/_dev/_current/ansible-install-web-server/ansible/

## Test server connexion
#   🚨  It will channge regarding project automation execution status
#       For starters it will be root on default SSH port
#       After user creation, root access will be prohibited, you'll need to connect through
#       the_builder_guy, with ssh key files, and a custom ssh port
ansible-playbook -i hosts 0-connexion-test.yml
# ansible-playbook -i hostsWithCustomSSHPort 0-connexion-test.yml


## Only once, setup user & ssh connexion
ansible-playbook -i hosts 1-first-connexion-setup.yml

# 🚨👷 You must now execute commands in generated file 'manual-commands.md'
# Add ~root ssh key to local ssh agent, cf. __root-manual-commands.md
eval `ssh-agent`
ssh-add ~/.ssh/YOUR_REMOTE_USER-ssh-key-ed25519

## Create users & change default SSH port
ansible-playbook -i hosts 2-generate-users-and-change-ssh-port.yml
# ansible-playbook -i hostsWithCustomSSHPort 2-generate-users-and-change-ssh-port.yml

# Add ~the_builder_guy ssh key to local ssh agent, cf. _the_builder_guy-manual-commands.md
ssh-add ~/.ssh/_the_builder_guy-ssh-key-ed25519

# Setup host server installation (security, docker, docker swarm)
ansible-playbook -i hostsWithCustomSSHPort 3-utils-security-docker-setup.yml

# ---

### 🛂 From there, docker stacks (docker-compose files started via swarm) will be execute in 3 parts :
##    the_builder_guy : Generate config files, update to host server
##    the_builder_guy : Generate .yml files (docker), update to host server
##    the_docker_guy  : Start or update instances (~docker run / docker stack deploy)

## Setup & start docker core services (reverse proxy, monitoring)
ansible-playbook -i hostsWithCustomSSHPort 4-setup-core-services.yml

# ---

# Setup & start a nginx server
#     🚨🔧 You need to configure vars ! (default: hello.masamune.fr)
ansible-playbook -i hostsWithCustomSSHPort 10-forge-a-nginx.yml

# Setup & start a wordpress server (server & mariadb, via bitnami image).
#     🚨🔧 You need to configure vars ! (default: test-wordpress.masamune.fr)
ansible-playbook -i hostsWithCustomSSHPort 20-forge-a-wordpress.yml

# ---

# Stop traefik service, preventing docker to auto reload it
ansible-playbook -i hostsWithCustomSSHPort 51-stop-traefik-service.yml

# Force host reboot
ansible-playbook -i hostsWithCustomSSHPort 52-force-host-reboot.yml

# ---

## Punctual tasks
# Execute punctual role/s or task/s, for admin confort ;)
ansible-playbook -i hostsWithCustomSSHPort 97-punctal.yml

# Update/Upgrade web server packages & OS, docker system prune
#  🚨 Might reboot server if needed
#  🚨 Be careful ! Removes stopped containers, dangling images, networks & volumes
ansible-playbook -i hostsWithCustomSSHPort 98-maintenance.yml

# WIP & tests, used for crafting without going through every task in a playbook
ansible-playbook -i hostsWithCustomSSHPort 99-craft-and-tests.yml
```
