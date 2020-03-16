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

# Add ~the_builder_guy ssh key to local ssh agent, cf. _the_builder_guy-manual-commands.md
> ssh-add ~/.ssh/_the_builder_guy-ssh-key-ed25519

# Lancer le playbook d'installation
> ansible-playbook -i hostsWithCustomSSHPort 3-playbook.yml | sed 's/\\n/\n/g'
```

## Plan de route

Copier/coller des TODOs effectuées, pour avoir la logique de l'installation avec moins de jargon technique, et avec les priorités.

Mettre en place le nouveau serveur

1. Tutoriels Ansible
   1. ✅ Prise en main ansible, repomper commandes grafikart ♻️ Grafikart Ansible + doc
   2. ✅ Rangement en recette clean (arborescence) > Création des roles
      1. ✅ Lancer un service après installation
      2. ✅ Créer des dossiers & fichiers
      3. ✅ Modifier une ligne dans un fichier (.. de configuration)
      4. ✅ Importer du contenu depuis un repo
   3. ✅ Accès machine locale
   4. ✅ Multiples hosts
2. Mettre en place la sécurité du serveur
   1. ✅ Create server repo & obfuscate vars
   2. ✅ Create a repo for Ansible role template
   3. ✅ Gestion de la connexion
      1. ✅ Création des clés SSH publiques et privées
      2. ✅ Implémentation des clés en local (.sh)
          1. ✅ Générer le fichier users/README_secret.md automatiquement, comme pour root
             1. ✅ Définition de l'agent ssh
             2. ✅ Ajout des clés
      3. ✅ Implémentation des clés sur le serveur
      4. ✅ Changement du port SSH
      5. ✅ Reconnexion avec *the_builder_guy* avec le bon port
   4. ✅ Installation des logiciels de base sur le serveur
   5. ✅ [Installation d'un utilisateur](https://www.grafikart.fr/tutoriels/ansible-753) sur le serveur et [bases SSH](https://www.grafikart.fr/tutoriels/ssh-686)
      1. ✅ Template de génération d'utilisateurs
         1. ✅ Roles différents (ex: sudo conditionnel)
      2. ✅ Retirer connexion par mots de passe
      3. ✅ Retirer connexion root
   6. 🔍 Grafikart / Mise en place d'un serveur web
      1. ✅ [Configration de VIM](https://www.grafikart.fr/tutoriels/vim-685)
      2. ✅ [CRONs](https://www.grafikart.fr/tutoriels/cron-tache-recurrente-1013)
         1. ✅ Faire un utilisateur dédié pour les crons
         2. ✅ Retirer les droits aux autres utilisateurs "cron.allow / cron.deny"
      3. ✅ [Shell](https://www.grafikart.fr/tutoriels/pimp-my-shell-750)
         1. ✅ Fix installation zsh : une pour root, une pour les autres utilisateurs
   7. [Iptables](https://www.grafikart.fr/tutoriels/iptables-694)
      1. ✅ Règles de base
      2. ✅ Autoriser [apt-get](https://www.grafikart.fr/tutoriels/iptables-694#c44945)
      3. ✅ Autoriser [monitoring ovh](https://docs.ovh.com/fr/dedicated/monitoring-ip-ovh/)
   8. 🚧♻️ Mise à l'heure du serveur
      1. ✅ Docs
         - [Ubuntu Time Synchronization](https://help.ubuntu.com/lts/serverguide/NTP.html)
         - [Tuto > Yimezones > How To Set Up Time Synchronization on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-time-synchronization-on-ubuntu-18-04) 10 juillet 2018
            - [Package timezone data tzdata](https://launchpad.net/ubuntu/+source/tzdata)
         - [Tuto > Keep Your Clock Sync with Internet Time Servers in Ubuntu 18.04](https://vitux.com/keep-your-clock-sync-with-internet-time-servers-in-ubuntu/)
         - [Ansible timezone module](https://docs.ansible.com/ansible/latest/modules/timezone_module.html)
         - [Ansible cookbook > ansible-clock](https://github.com/fabiocorneti/ansible-clock)
      2. ✅ Configuration de la timezone > package tzdata
      3. ✅ Synchronisation automatique > package ntp
      4. ✅ Mise à jour de la timezone > commande timedatectl set-timezone
      5. ✅ Relance du service au reboot
   9. ✅ [fail2ban](https://www.grafikart.fr/tutoriels/fail2ban-698)
      1. ✅ Recos grafikart
         1. ✅ fail2ban [autoriser nginx port 80](https://unihost.com/help/how-to-protect-a-server-with-fail2ban/)
         2. Autoriser les pings
         3. Autoriser le Monitoring OVH
      2. ✅ Recos archi linux
      3. ✅ fail2ban > [securité++](https://wiki.archlinux.org/index.php/Fail2ban#Service_hardening)
