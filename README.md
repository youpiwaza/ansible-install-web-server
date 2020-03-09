# Ansible cookbook to install my web server

Install a web server through ansible, based on personnal [researches](https://github.com/youpiwaza/notes-installation-serveur-web-docker) and [tests](https://github.com/youpiwaza/server-related-tutorials).

cf. [dedicated repo](https://github.com/youpiwaza/server-related-tutorials/tree/master/02-ansible) for ansible installation, setup and tests :

Requirements :

- A working terminal
- A working SSH access through terminal (private key & ssh-agent on local machine, & public key on server)

Note: I'm letting this repo public for educationnal purposes, and obfuscate real ids files, but if you want to use it, feel free to replace *_not_so_real files with your ids :)

## Usage

Working on Windows WSL, personnal notes for my setup

```bash
# WSL
# Reco SSH, si ssh configuré correctement
> ssh awesome
# Pass phrase

# Acceder au projet
> cd ~/../c/Users/Patolash/Documents/_dev/ansible-install-web-server/ansible
# Configurer l'agent SSH en local
> eval `ssh-agent`
> ssh-add ~/.ssh/masamune-ssh-key-ed25519-yay

# Lancer le playbook
> ansible-playbook -i hosts playbook.yml | sed 's/\\n/\n/g'
```
