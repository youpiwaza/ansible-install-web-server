# Ansible cookbook to install my web server

Install a web server through ansible, based on personnal [researches](https://github.com/youpiwaza/notes-installation-serveur-web-docker) and [tests](https://github.com/youpiwaza/server-related-tutorials).

cf. [dedicated repo](https://github.com/youpiwaza/server-related-tutorials/tree/master/02-ansible) for ansible installation, setup and tests :

Requirements :

- A working terminal
- A working SSH access through terminal (private key & ssh-agent on local machine, & public key on server)
- Be careful on your login configuration (hosts) if you edited the SSH port (default: 22)

Note: I'm letting this repo public for educationnal purposes, and obfuscate real ids files, but if you want to use it, feel free to replace *_not_so_real files with your ids :)

## Usage

Working on Windows WSL, personnal notes for my setup

```bash
# Acceder au projet
> cd ~/../c/Users/Patolash/Documents/_dev/ansible-install-web-server/ansible

# Une seule fois, lancer le playbook pour configurer la première connexion
> ansible-playbook -i hosts 1-first-connexion-setup.yml | sed 's/\\n/\n/g'
# Executer les commandes dans le fichier généré 'manual-commands.md'

# Add ~root ssh key to local ssh agent, cf. __root-manual-commands.md
> eval `ssh-agent`
> ssh-add ~/.ssh/YOUR_REMOTE_USER-ssh-key-ed25519

# Lancer le playbook de création des utilisateurs et changement du port SSH
> ansible-playbook -i hosts 2-generate-users-and-change-ssh-port.yml | sed 's/\\n/\n/g'

# Add ~the_buildedr_guy ssh key to local ssh agent, cf. _the_buildedr_guy-manual-commands.md
> ssh-add ~/.ssh/_the_buildedr_guy-ssh-key-ed25519

# Lancer le playbook d'installation
> ansible-playbook -i hostsWithCustomSSHPort 3-playbook.yml | sed 's/\\n/\n/g'
```
