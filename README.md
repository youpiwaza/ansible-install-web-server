# Ansible cookbook to install my web server

Install a web server through ansible, based on personnal [researches](https://github.com/youpiwaza/notes-installation-serveur-web-docker) and [tests](https://github.com/youpiwaza/server-related-tutorials).

cf. [dedicated repo](https://github.com/youpiwaza/server-related-tutorials/tree/master/02-ansible) for ansible installation, setup and tests :

Requirements :

- [A working terminal](https://github.com/youpiwaza/install-dev-env)
- A [working SSH access](https://github.com/youpiwaza/server-related-tutorials/tree/master/02-ansible/01-configuration-ssh) through terminal (private key & ssh-agent on local machine, & public key on server)
- Be careful on your login configuration (hosts) if you edited the SSH port (default: 22)

Note: I'm letting this repo public for educationnal purposes, and obfuscate real ids files, but if you want to use it, feel free to replace *_not_so_real files with your ids :)

## Usage

Working on Windows WSL, personnal notes for my setup

```bash
# Acceder au projet
# cd /mnt/c/Users/Patolash/Documents/_dev/__dev_current/ansible-install-web-server/ansible/
cd /mnt/c/Users/masam/Documents/_dev/_current/ansible-install-web-server/ansible/

# Tester la connexion au serveur
#     🚨 Checker le rôle en fonction de l'avancement (~root/user, port ssh classique ou custom)
# ansible-playbook -i hosts 0-connexion-test.yml
ansible-playbook -i hostsWithCustomSSHPort 0-connexion-test.yml


# Une seule fois, lancer le playbook pour configurer la première connexion
ansible-playbook -i hosts 1-first-connexion-setup.yml

# Executer les commandes dans le fichier généré 'manual-commands.md'
# Add ~root ssh key to local ssh agent, cf. __root-manual-commands.md
eval `ssh-agent`
ssh-add ~/.ssh/YOUR_REMOTE_USER-ssh-key-ed25519

# Lancer le playbook de création des utilisateurs et changement du port SSH
ansible-playbook -i hosts 2-generate-users-and-change-ssh-port.yml
# ansible-playbook -i hostsWithCustomSSHPort 2-generate-users-and-change-ssh-port.yml

# Add ~the_builder_guy ssh key to local ssh agent, cf. _the_builder_guy-manual-commands.md
ssh-add ~/.ssh/_the_builder_guy-ssh-key-ed25519

# Lancer le playbook d'installation de l'hôte (sécurité, docker, docker swarm)
ansible-playbook -i hostsWithCustomSSHPort 3-utils-security-docker-setup.yml

# ---

### 🛂 A partir de la, les stack (fichiers docker-compose lancés via swarm) seront composés de 3 parties :
##    the_builder_guy : Génération des fichiers de configs, et mise en ligne sur le serveur
##    the_builder_guy : Génération des fichiers .yml, et mise en ligne sur le serveur
##    the_docker_guy  : Lancement ou mise à jour des instances (~docker run / docker stack deploy)

## Lancer le playbook de mise en place des services docker de base (reverse proxy, monitoring)
ansible-playbook -i hostsWithCustomSSHPort 4-setup-core-services.yml

# ---

# Lancer le playbook de génération d'un playbook de mise en place d'un serveur nginx
#     🔧 Nécessite la configuration de variables ! (défaut: hello.masamune.fr)
ansible-playbook -i hostsWithCustomSSHPort 10-forge-a-nginx.yml

# Lancer le playbook de génération d'un playbook de mise en place d'une base wordpress (serveur & mariadb, via image bitnami).
#     🔧 Nécessite la configuration de variables ! (défaut: test-wordpress.masamune.fr)
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

# WIP & tests
ansible-playbook -i hostsWithCustomSSHPort 99-craft-and-tests.yml
```
