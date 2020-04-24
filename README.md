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
# > ansible-playbook -i hostsWithCustomSSHPort 2-generate-users-and-change-ssh-port.yml | sed 's/\\n/\n/g'

# Add ~the_builder_guy ssh key to local ssh agent, cf. _the_builder_guy-manual-commands.md
> ssh-add ~/.ssh/_the_builder_guy-ssh-key-ed25519

# Lancer le playbook d'installation de l'hôte (sécurité, docker, docker swarm)
> ansible-playbook -i hostsWithCustomSSHPort 3-utils-security-docker-setup.yml | sed 's/\\n/\n/g'

# Lancer le playbook de mise en place des services docker de base (traefik, container metrics & alerts)
> ansible-playbook -i hostsWithCustomSSHPort 4-docker-required-services.yml | sed 's/\\n/\n/g'
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
2. ✅ Environnement de dev propre
   1. ✅🔍 Docs
      1. [cocadmin](https://www.youtube.com/watch?v=yqLPUOsy-8M)
         1. Avoir une image docker pour faire tourner ansible (installation un poil plus complexe avec manip de docker)
         2. Utiliser docker pour monter un ubuntu (serveur) et faire tourner les scripts ansible dessus
   2. ✅ Faire tourner l'exemple
   3. ✅ Update > ansible:alpine & ubuntu:18.04
   4. ✅ Faire tourner un nginx (install via ansible) alakon sur 8080
      1. ✅ Vérif via ~~[curl](https://www.ansible.com/blog/six-ways-ansible-makes-docker-compose-better)~~ [uri](https://docs.ansible.com/ansible/latest/modules/uri_module.html)
   5. 💩 Installer docker et y monter un container nginx alakon
      1. KO / WSL + DinD
3. Mettre en place la sécurité du serveur
   1. [Doc](https://www.digitalocean.com/community/tutorials/how-to-use-ansible-to-automate-initial-server-setup-on-ubuntu-18-04)
   2. ✅ Create server repo & obfuscate vars
   3. ✅ Create a repo for Ansible role template
   4. ✅ Gestion de la connexion
      1. ✅ Création des clés SSH publiques et privées
      2. ✅ Implémentation des clés en local (.sh)
          1. ✅ Générer le fichier users/README_secret.md automatiquement, comme pour root
             1. ✅ Définition de l'agent ssh
             2. ✅ Ajout des clés
      3. ✅ Implémentation des clés sur le serveur
      4. ✅ Changement du port SSH
      5. ✅ Reconnexion avec *the_builder_guy* avec le bon port
   5. ✅ Installation des logiciels de base sur le serveur
   6. ✅ [Installation d'un utilisateur](https://www.grafikart.fr/tutoriels/ansible-753) sur le serveur et [bases SSH](https://www.grafikart.fr/tutoriels/ssh-686)
      1. ✅ Template de génération d'utilisateurs
         1. ✅ Roles différents (ex: sudo conditionnel)
      2. ✅ Retirer connexion par mots de passe
      3. ✅ Retirer connexion root
   7. 🔍 Grafikart / Mise en place d'un serveur web
      1. ✅ [Configration de VIM](https://www.grafikart.fr/tutoriels/vim-685)
      2. ✅ [CRONs](https://www.grafikart.fr/tutoriels/cron-tache-recurrente-1013)
         1. ✅ Faire un utilisateur dédié pour les crons
         2. ✅ Retirer les droits aux autres utilisateurs "cron.allow / cron.deny"
      3. ✅ [Shell](https://www.grafikart.fr/tutoriels/pimp-my-shell-750)
         1. ✅ Fix installation zsh : une pour root, une pour les autres utilisateurs
   8. [Iptables](https://www.grafikart.fr/tutoriels/iptables-694)
      1. ✅ Règles de base
      2. ✅ Autoriser [apt-get](https://www.grafikart.fr/tutoriels/iptables-694#c44945)
      3. ✅ Autoriser [monitoring ovh](https://docs.ovh.com/fr/dedicated/monitoring-ip-ovh/)
   9. ✅ [fail2ban](https://www.grafikart.fr/tutoriels/fail2ban-698)
      1. ✅ Recos grafikart
         1. ✅ fail2ban [autoriser nginx port 80](https://unihost.com/help/how-to-protect-a-server-with-fail2ban/)
         2. Autoriser les pings
         3. Autoriser le Monitoring OVH
      2. ✅ Recos archi linux
      3. ✅ fail2ban > [securité++](https://wiki.archlinux.org/index.php/Fail2ban#Service_hardening)
   10. ✅🔍 cocadmin / [sécurité serveur](https://www.youtube.com/watch?v=UmbndsZFIUE)
4. Mettre en place les utilitaires du serveur
   1. 🌱 Envoi de mail
       1. ✅🔍 Docs
         - [Ubuntu](https://help.ubuntu.com/lts/serverguide/postfix.html)
         - [Grafikart Postfix](https://www.grafikart.fr/tutoriels/postfix-sendonly-695)
         - [How to Setup Postfix as Send-only Mail Server on an Ubuntu 18.04 Dedicated Server or VPS](https://hostadvice.com/how-to/how-to-setup-postfix-as-send-only-mail-server-on-an-ubuntu-18-04-dedicated-server-or-vps/)
         - // Necessaire pour envoi de mails depuis le serveur (erreurs, logs, etc.)
      1. ✅ Config iptables pour envoi de mail approprié
      2. ✅ Test OK mais arrivée dans spams. En attendant la config > Gmail filtrer expéditeur > jamais dans spam
      3. 🌱 NDD > Ajout SPF            / Sender Policy Framework
      4. 🌱 NDD & mail() > Ajout DKIM  / DomainKeys Identified Mail
      5. 🌱 DMARC                      / Domain-Based Message Authentication, Reporting, and Conformance
      6. ✅ fail2ban config > ban_action : action_mwl (mail with logs en cas de ban)
   2. ✅♻️ Mise à l'heure du serveur
      1. ✅ Docs
         - [Ubuntu Time Synchronization](https://help.ubuntu.com/lts/serverguide/NTP.html)
         - [Tuto > Yimezones > How To Set Up Time Synchronization on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-time-synchronization-on-ubuntu-18-04) 10 juillet 2018
            - [Package timezone data tzdata](https://launchpad.net/ubuntu/+source/tzdata)
         - [Tuto > Keep Your Clock Sync with Internet Time Servers in Ubuntu 18.04](https://vitux.com/keep-your-clock-sync-with-internet-time-servers-in-ubuntu/)
         - [Ansible timezone module](https://docs.ansible.com/ansible/latest/modules/timezone_module.html)
         - [Ansible cookbook > ansible-clock](https://github.com/fabiocorneti/ansible-clock)
         - [Chrony faq](https://chrony.tuxfamily.org/faq.html)
         - [Comparaison chrony vs ntp](https://chrony.tuxfamily.org/comparison.html)
         - [timesyncd.conf](http://manpages.ubuntu.com/manpages/cosmic/man5/timesyncd.conf.5.html)
         - [keep-your-clock-sync-with-internet-time-servers-in-ubuntu](https://vitux.com/keep-your-clock-sync-with-internet-time-servers-in-ubuntu/)
      2. ✅ Configuration de la timezone > package tzdata
      3. ✅ Synchronisation automatique > package ntp
      4. ✅ Mise à jour de la timezone > commande timedatectl set-timezone
      5. ✅ Relance du service au reboot
      6. ✅ Remplacer NTP par Chrony
      7. ✅ systemctl status systemd-timesyncd > inactive dead
         1. [doc](https://unix.stackexchange.com/questions/504381/chrony-vs-systemd-timesyncd-what-are-the-differences-and-use-cases-as-ntp-cli)
      8. ✅ Firewall / Autoriser Mise à l'heure du serveur [NTP](https://www.google.com/search?q=ntp)
5. Installation de docker
   1. ✅🔍 Note: Rootless Docker
      1. is experimental
      2. features are not supported : Overlay network
         1. Nécessaire pour [réseau swarm](https://docs.docker.com/network/overlay/)
      3. Note: App armor pas dispo dans docker rootless, SEL Linux?
      4. 💩 > Choix : **Installation classique**
   2. ✅🔍 Docs
      - [Docker / Installation officielle](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository)
      - [Digital ocean > Automate Docker install on ubuntu](https://www.digitalocean.com/community/tutorials/how-to-use-ansible-to-install-and-set-up-docker-on-ubuntu-18-04)
        - [+ playbook de base <3](https://github.com/do-community/ansible-playbooks/tree/master/docker_ubuntu1804)
      - Manage docker through ansible [Ansible Docker guide](https://docs.ansible.com/ansible/latest/scenario_guides/guide_docker.html)
   3. ✅🔍 Note: Docker in Docker (DinD), pour tests en local
      1. [First old article](https://www.docker.com/blog/docker-can-now-run-within-docker/)
      2. [Updated article](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)
      3. [Alternative : Run nested docker through global daemon (share socket)](https://itnext.io/docker-in-docker-521958d34efd)
      4. [--privileged / Not in prod](https://blog.trendmicro.com/trendlabs-security-intelligence/why-running-a-privileged-container-in-docker-is-a-bad-idea/)
      5. [Official docker image](https://hub.docker.com/_/docker)
   4. ✅ Mettre en place un conteneur nginx hello world sur l'ip du serveur
      1. cf. `/ansible/roles/docker-installation/tasks/main.yml` > uncomment `include test-nginx.yml`
   5. ✅ Mettre en place un docker-compose & un swarm
      1. 🔍✅ Docs
         1. [Reco ansible](https://www.ansible.com/blog/six-ways-ansible-makes-docker-compose-better)
         2. [Ansible docker guide](https://docs.ansible.com/ansible/latest/scenario_guides/guide_docker.html)
         3. [Ansible docker_image module](https://docs.ansible.com/ansible/latest/modules/docker_image_module.html)
         4. [Ansible docker_container module](https://docs.ansible.com/ansible/latest/modules/docker_container_module.html)
         5. [Ansible docker_compose module](https://docs.ansible.com/ansible/latest/modules/docker_compose_module.html), également utilisé pour swarm
      2. ✅ Installation des [plugins recommandés](https://docs.ansible.com/ansible/latest/modules/docker_compose_module.html#requirements)
      3. ✅ Faire tourner un projet compose
         1. ✅🐛 **Attention**, si un nom de projet est spécifié au lancement, il doit également être spécifié lors de l'arrêt
      4. Faire tourner des services via swarm
         1. 🔍✅ Docs ansible
            - [Créer un swarm](https://docs.ansible.com/ansible/latest/modules/docker_swarm_module.html)
            - [Gérer les services](https://docs.ansible.com/ansible/latest/modules/docker_swarm_service_module.html)
            - [Docker stack](https://docs.ansible.com/ansible/latest/modules/docker_stack_module.html#examples)
         2. ✅ Initialiser swarm
            1. ✅ Conditionnel (pas péter prod avec tests)
         3. ✅ Lancer et tester un service
         4. ✅ Supprimer le swarm
         5. ✅ Idem `docker stack`
   6. Mettre en place la sécurité docker en vérifiant que tout roule toujours
      1. 🔍 Docs
         - ✅💚 [Hardening Docker containers, images, and host - security toolkit](https://www.stackrox.com/post/2017/08/hardening-docker-containers-and-hosts-against-vulnerabilities-a-security-toolkit/) / Survol d'un peu tout
         - ✅ iptables firewall > docker [needs update](https://github.com/nickjj/ansible-iptables/blob/master/tasks/main.yml)
         - ✅ [Docker Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/)
         - ✅ [Run your app in production](https://docs.docker.com/get-started/orchestration/)
           - ✅ [Docker security](https://docs.docker.com/engine/security/security/)
         - ✅ Security > capabilites. [Dedicated article](https://opensource.com/business/15/3/docker-security-tuning) as ansible official doc sucks
           - ✅ [Docker doc on capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) defauts & dispos
             - ✅ [Ubuntu user privileges list](https://wiki.ubuntu.com/Security/Privileges)
           - ✅ [Docker and permission management](https://blog.ippon.tech/docker-and-permission-management/)
           - ✅ [Docker security basics](https://innablr.com.au/blog/docker-security-basics/)
           - ✅ [Docker success / Security recos](https://success.docker.com/article/security-best-practices#apparmorselinux)
      2. Docker Post-installation steps for Linux
         1. ✅ [Manage Docker as a non-root user](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)
         2. ✅ Configure Docker to start on boot
         3. ✅ Proper docker [logs](https://docs.docker.com/config/containers/logging/) [configuration](https://docs.docker.com/config/containers/logging/configure/)
            1. 🔍✅ Docs
               - ✅ [Sponso / Docker logging best practices](https://www.datadoghq.com/blog/docker-logging/)
                 - Reco: json-log default driver, avec quelques options + configurer logs par défaut + UI datadog (eq. grafana)
            2. 🔍✅ Choix : Grafana (cf. [docker swarm rocks > swarmprom](https://dockerswarm.rocks/swarmprom/) )
            3. ✅ Spécifier une [taille max](https://docs.docker.com/config/containers/logging/configure/#configure-the-default-logging-driver)
               1. ✅ Note: Le fichier /etc/docker/daemon.json n'existe pas par défaut.
               2. ✅ Note: docker a **besoin de restart** pour prendre en compte la nouvelle configuration des logs
      3. ✅ Run your app in production
         1. ✅🔍 MAJ `ansible/roles/docker-installation/tasks/run-your-app-in-production.yml`
         2. ✅ Modification de la conf du *docker daemon*
            1. ✅ Template (json) + doc .MD
         3. 🔍✅ [Ubuntu server](https://help.ubuntu.com/lts/serverguide/serverguide.pdf)
         4. ✅ Remove *the_docker_peon* privileges, so he can access only his own `/home`
            1. ✅ [Ubuntu doc / Restrict /home_da_user access to only himself](https://help.ubuntu.com/lts/serverguide/serverguide.pdf#page=188)
            2. ✅ SSH prison ? No > rbash + root & docker namespace ownership
         5. 💩 Add automated restriction to docker volumes (via *the_docker_guy* ?) > volumes only mounted in *the_docker_peon* `/home`
            1. Be careful during container creation
            2. Restrict sensible mounted directories to read-only
         6. ✅ Use traditional UNIX permission checks to limit access to the control socket
            1. Default srw-rw----  1 root    docker     0 mars  31 18:32 docker.sock=
            2. Only the_builder_guy (w. sudo) & the_docker_guy ('docker' group) have access
         7. ✅ Prevent containers to acquire new privileges
            1. ~~During container creation (some --tag)~~
            2. docker daemon.json > "no-new-privileges": true
         8. ✅ Limit docker functions
            1. docker load
            2. docker pull
            3. > Rien trouvé sur le net, mais avec seccomp/apparmor/capabilites ca devrait faire le taf
         9. ✅ AppArmor
             1. 🔍✅ Docs
                1. [Ubuntu / AppArmor](https://help.ubuntu.com/lts/serverguide/serverguide.pdf#page=200)
                2. [AppArmor](https://apparmor.net/) & [Main commands](https://gitlab.com/apparmor/apparmor/-/wikis/Documentation)
             2. ✅ Install
             3. ✅ Load profile/s
                1. ✅ Nginx example profile load
             4. ✅ Apply to docker daemon / already done by docker
             5. 🔍✅ ~~[App armor recommandations & profiles](https://www.nccgroup.trust/uk/our-research/abusing-privileged-and-unprivileged-linux-containers/)~~
      4. 💩 grsec / Payant
      5. 💩 egress / Network stuff, mostly nothing concrete on google
      6. ✅ [Seccomp](https://docs.docker.com/engine/security/seccomp/)
         1. Available : yes
         2. Docker has a default profile, [good enough](https://success.docker.com/article/security-best-practices#seccomp)
            1. ✅ Récupérer le profil et le mettre sur le serveur
      7. ✅ SELinux
         1. Restricted to [other distros](https://success.docker.com/article/security-best-practices#apparmorselinux), use AppArmor for Ubuntu
            1. Is present in /etc on Ubuntu 18.04 ...
      8. ✅ [Docker_Security_Cheat_Sheet.md](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet)
      9. 🌱 [Ubuntu CVE / Common Vulnerabilities and Exposures](https://www.google.com/search?q=ubuntu+CVEs)
