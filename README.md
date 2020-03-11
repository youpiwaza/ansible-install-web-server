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
# Une seule fois, lancer le playbook pour configurer la première connexion
> ansible-playbook -i hosts first-connexion-setup.yml | sed 's/\\n/\n/g'
# Executer les commandes dans le fichier généré 'manual-commands.md'

# Acceder au projet
> cd ~/../c/Users/Patolash/Documents/_dev/ansible-install-web-server/ansible
# Cf. manual-commands.md
> eval `ssh-agent`
> ssh-add ~/.ssh/YOUR_REMOTE_USER-ssh-key-ed25519

# Lancer le playbook d'installation
> ansible-playbook -i hosts playbook.yml | sed 's/\\n/\n/g'
```
