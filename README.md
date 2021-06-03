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
cd /mnt/c/Users/Patolash/Documents/_dev/ansible-install-web-server/ansible/

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
##    BUILDER_GUY : Génération des fichiers de configs, et mise en ligne sur le serveur
##    BUILDER_GUY : Génération des fichiers .yml, et mise en ligne sur le serveur
##    DOCKER_GUY  : Lancement ou mise à jour des instances (~docker run / docker stack deploy)

## Lancer le playbook de mise en place des services docker de base (reverse proxy, monitoring)
ansible-playbook -i hostsWithCustomSSHPort 4-setup-core-services.yml

# ---

# Lancer le playbook de mise en place de 2 serveurs web nginx (un classique & un avec php)
ansible-playbook -i hostsWithCustomSSHPort 10-forge-a-nginx-stack.yml
ansible-playbook -i hostsWithCustomSSHPort 11-forge-a-nginx-php-stack.yml

# Lancer le playbook de mise en place d'un wordpress. Nécessite la configuration de variables ! (defauts: test wordpress)
ansible-playbook -i hostsWithCustomSSHPort 20-forge-a-wordpress-stack.yml

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
#  /!\ Be careful ! Might include reboots !
#  /!\ Be careful ! Removes stopped containers, dangling images, networks & volumes
ansible-playbook -i hostsWithCustomSSHPort 98-maintenance.yml

# WIP & tests
ansible-playbook -i hostsWithCustomSSHPort 99-craft-and-tests.yml
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
4. ✅ Mettre en place les utilitaires du serveur
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
5. ✅ Installation de docker
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
      10. Automatic security benchmark [Docker bench security](https://github.com/docker/docker-bench-security)
          1. ✅ [Aide pour les correctifs](https://www.digitalocean.com/community/tutorials/how-to-audit-docker-host-security-with-docker-bench-for-security-on-ubuntu-16-04)
          2. ✅ [+1](https://www.digitalocean.com/community/tutorials/7-security-measures-to-protect-your-servers)
          3. ✅ auditd installation & configuration
          4. ✅ Standalone containers recos (docker run)
             1. ✅ [Labels on containers](https://docs.docker.com/engine/reference/commandline/run/#set-metadata-on-container--l---label---label-file)
             2. ✅ [Labels best practices](https://docs.docker.com/config/labels-custom-metadata/)
                1. Authors of third-party tools should prefix each label key with the reverse DNS notation of a domain they own, such as com.example.some-label.
             3. Use label files ? No > Maintenance in 1 place + ansible's docker_container doesn't support
                1. ✅ Client
                2. ✅ Project
                3. ✅ Date
                4. ✅ Type = test/dev/prod
             4. ✅ Label on networks
             5. ✅ Label on volumes
          5. ✅ Check and clean TODO_shame for security stuff
          6. ✅ Via ansible
          7. ✅ Ansible task template > Utiliser [module_defaults](https://docs.ansible.com/ansible/latest/user_guide/playbooks_module_defaults.html)
             1. ✅ cf. server-related-tutorials/02-ansible/13-ansible-task-template-w-module-defaults
          8. ✅ Compose containers recos (docker compose)
          9. ✅ Via ansible
          10. Maj anciens exemples
              1. ✅ Dossier des projets (docker_peon)
              2. ✅ Curated dcompose
          11. ⏩ Services recos (docker swarm & docker stack)
              1. Tester service simple (1 image)
                 1. ✅ Docker Security Bench > Ligne de commande
                 2. 🔍 Docs
                    1. ✅ [Swarm tutorial](https://docs.docker.com/engine/swarm/swarm-tutorial/)
                    2. ✅ [Swarm admin guide](https://docs.docker.com/engine/swarm/admin_guide/)
                    3. ✅ [Ansible docker swarm service](https://docs.ansible.com/ansible/latest/modules/docker_swarm_service_module.html)
                 3. Docs recos > Ligne de commande
                    1. ✅ Replicas & [max replicas](https://docs.docker.com/engine/reference/commandline/service_create/#specify-maximum-replicas-per-node---replicas-max-per-node)
                    2. ✅ [Update behavior](https://docs.docker.com/engine/swarm/services/#configure-a-services-update-behavior)
                    3. ✅ [Rollback behavior](https://docs.docker.com/engine/swarm/services/#roll-back-to-the-previous-version-of-a-service)
                    4. ✅ [Labels](https://docs.docker.com/engine/reference/commandline/service_create/#set-metadata-on-a-service--l---label)
                    5. ✅ [Check all options](https://docs.docker.com/engine/reference/commandline/service_create/#usage)
                 4. ✅ [Ansible](https://docs.ansible.com/ansible/latest/modules/docker_swarm_service_module.html)
              2. ✅ Tester service composé (stack) + network
                 1. 🔍 Docs
                    1. ✅ [Tuto](https://docs.docker.com/engine/swarm/stack-deploy/#set-up-a-docker-registry)
                    2. ✅ [stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/)
                    3. ✅ [The Difference Between Docker Compose And Docker Stack](https://vsupalov.com/difference-docker-compose-and-docker-stack/)
                       1. ✅ [Différences between compose & stack > chercher 'ignore' dans la page](https://docs.docker.com/compose/compose-file/)
                       2. ✅ [Dstack deploy not available stuff](https://docs.docker.com/compose/compose-file/#not-supported-for-docker-stack-deploy)
                       3. ✅💥 NOTE IMPORTANTE: 1 seul fichier compose pour dev et prod : Dcompose pour dev et Dstack pour prod
                    4. [Ansible stack](https://docs.ansible.com/ansible/latest/modules/docker_stack_module.html)
                 2. ✅ ~ Ligne de commande > Modification du fichier [docker-compose du projet dédié](https://github.com/youpiwaza/docker-compose-curated-example/blob/master/docker-compose.yml)
                 3. ✅ Ansible
   7. 🌱 Clean syslogs > sudo nano /var/log/syslog
      1. Network related
         1. 💩 systemd-udevd[1004]: link_config: autonegotiation is unset or enabled
            1. [How to troubleshoot an ethernet interface on a Ubuntu Server with ethtool](https://www.techrepublic.com/article/how-to-troubleshoot-an-ethernet-interface-ubuntu-server-with-ethtool/)
         2. 💩 docker link_config: could not get ethtool features for
            1. Seems unresolved, don't wanna mess networks with ~patches
            2. [systemd github issue comment 1](https://github.com/systemd/systemd/issues/3374)
            3. [systemd github issue comment 2](https://github.com/systemd/systemd/issues/3374#issuecomment-288882355)
         3. 💩 Could not set offload features of vx-001018-y49o8: No such device
         4. 💩 systemd-udevd[1526]: Could not generate persistent MAC address for vetha2cadaf: No such file or directory
      2. ~✅ audit: backlog limit exceeded
         1. [Augment audit limit](https://serverfault.com/questions/352281/server-locking-up-var-log-messages-reports-backlog-limit-exceeded)
         2. Investigate what is generating so much logs (systemd > network BS)
            1. `sudo aureport --start today --event --summary -i`
               - total  type
               - ======================
               - 34844  SYSCALL
   8. ✅ Update to [Ubuntu 20](https://ubuntu.com/blog/ubuntu-20-04-lts-arrives)
      1. ✅ Check n' fix if needed
   9. ✅🎉🎉🎉 Close this fucking topic
6. ✅ Clean ansible before pursuing
   1. Refacto variables
      1. ✅ Déplacers tours les vars/main_not_so_real.yml dans */defaults/main.yml
         1. ✅ Si vars === not_so_real > supprimer vars (garder uniquement pour données persos)
      2. ✅ Préfixer les variables (éviter les conflits + config depuis la racine)
         1. ✅ Rendre global si besoin pour éviter les répétitions
         2. ✅ Enlever les prefixes "my" et autres trucs inutiles
      3. ✅ Fichier à la racine avec valeurs par défaut, toutes commentées
      4. 🌱 Chargement de mes variables réelles depuis repo privé
   2. 🌱 Lint users
      1. Replace {{ users.0.name }} & {{ users.2.name }} par les vrais users
         1. Rechercher {{ users.
      2. Créer liste dynamique populée à partir des utilisateurs dédiés
      3. ansible\roles\docker-installation\templates\etc-docker-daemon-json.j2
   3. ✅ Décommenter tout
   4. ✅ Réinstallation complète pour vérifier le déroulement complet + Fix si besoin
   5. 🌱 Ubuntu reco : Canonical Livepatch is available for installation.
       - Reduce system reboots and improve kernel security. [Activate at](https://ubuntu.com/livepatch)
       - Not available (yet) for Ubuntu 20.04 ?
       - Pay for business use > Physical server essential > 225$ >.>
7. ✅ Créer 2 playboks supplémentaires
   1. ✅ 98-Maintenance    > Opération usuelles, ~os & package update, docker system prune
      1. Piocher dans les rôles d'installation
   2. ✅ 99-Crafts & tests > Playbook dédié à la création, pour éviter de tout commenter/décommenter/oublier
   3. ✅ 97-Punctal-task   > Playbook pour éxécuter une (ou plusieurs) tâches ponctuelles
8. ✅ Installer les containers de l'architecture de base via ansible
   1. ✅ Mettre en place les noms de domaine pour les tests & services publics de base
      1. test           .DOMAIN.COM   // basic nginx
      2. test-wordpress .DOMAIN.COM   // basic nginx
      3. traefik        .DOMAIN.COM   // traefik dashboard
      4. prometheus     .DOMAIN.COM   // metrics database
      5. grafana        .DOMAIN.COM   // visualize metrics
      6. alertmanager   .DOMAIN.COM   // alerts dispatcher
      7. unsee          .DOMAIN.COM   // alert manager dashboard
   2. ✅ Informer l'utilisateur via debug, en fin de playbook 3.
   3. ✅ docker maintenance
      1. System prune
      2. Images removal
   4. ✅ Initialiser la stack
   5. ✅ Vérifier que l'utilisateur the_docker_guy peut lancer des stacks
      3. ✅ Utiliser la version curated
      4. ✅ Resoudre les problèmes de droits
      5. ✅ Cleaner et mettre en place (99e playbook > 4eme playbook)
   6. ✅✅✅✅✅✅✅✅✅✅✅✅✅✅✅✅ Reverse Proxy ✅✅✅✅✅✅✅✅✅✅✅✅✅
      6. Installation de [traefik pour Docker](https://docs.traefik.io/providers/docker/)
         1. ✅ Docs
            1. [Traefik > Docker swarm discovery](https://docs.traefik.io/providers/docker/)
            2. [Traefik > Docker swarm routing](https://docs.traefik.io/routing/providers/docker/)
            3. [Traefik > Docker basic example](https://docs.traefik.io/user-guides/docker-compose/basic-example/)
            4. [Traefik > Docker TLS example](https://docs.traefik.io/user-guides/docker-compose/acme-tls/)
            5. [Traefik v2.2 notes](https://containo.us/blog/traefik-2-2-ingress/)
         2. Exemples en local
            1. ✅ Base stacks traefik + hello world
            2. Linted
               1. ✅ Toutes la doc
               2. 🐛✅ Problème avec sous domaines > Ressources statiques non trouvées (image tutum)
               3. ✅ Socket > [conteneur proxy pour accès à la socket](https://chriswiegman.com/2019/11/protecting-your-docker-socket-with-traefik-2/)
            3. ✅ Curated > Add my Dcompose recos (DBS, Labels, etc.)
            4. ✅ En faire des projets docker compose pour utilisation avec stack
               1. ✅ Ajout de l'utilisateur
               2. ✅ +1 pour site de test
            5. ✅ Tests en ligne , cf. server-related-tutorials/01-docker/04-my-tests/09-traefik-curated/06-prod-traefik-curated
               1. ✅ Faire marcher, déjà
               2. ✅ Linter README & versionner tests cancer
               3. ✅ Faire marcher, déjà
                  1. Bad gateway 502 / connection refused / connect: permission denied / mes couilles
                     1. Rajouter au service Traefik
            6. 🌱 Résoudre les éventuels problèmes dans les logs
            7. ✅ Alpha reorder
            8. ✅ Comments
            9. ✅ Proper renaming
               1. ✅ Nomenclature ports exterieurs services (pas de doublons) / Regarder pour gestion automatique
                  1. Pas besoin de spécifier explicitement le port de sortie
               2. ✅ Nomenclature clients pour services et autres conneries traefik
                  1. Tester conflits de noms si services muliples
                  2. traefik_1            | {"level":"error","msg":"Router defined multiple times with different configurations in [hello-helloworld-ziecuama7f13gx12pg8vh11jt helloDeux-helloworld-g79k1r85gyzcuxv3v33k7lwnq]","providerName":"docker","routerName":"helloworld","time":"2020-05-08T14:37:31Z"}
                  3. > Cf. nomenclature
            10. ✅ Minor linting/tweaks
                1. ✅ Force bridge driver for socket network
                2. ✅ socket volume > force read only
                3. ✅ Activer l'encryptage du réseau d'accès à la socket [bret fisher stack example](https://github.com/BretFisher/dogvscat/blob/master/stack-proxy-global.yml)
                4. ✅ Lancer traefik as read only, cf bret ^
                5. ✅ Cap drop all + Cap_ADD "CAP_NET_BIND_SERVICE"
                6. ✅ Specific user > docker peon
                   1. command traefik error: error while building entryPoint web: error preparing server: error opening listener: listen tcp :80: bind: permission denied
                   2. // Specific unprivileged user needs access to ports < 1024
                     - sysctls:
                       - net.ipv4.ip_unprivileged_port_start: 0
                7. ✅ Traefik stats > Stats collection is disabled. Help us improve Traefik by turning this feature on :). More details [here](https://docs.traefik.io/contributing/data-collection/)
            11. ✅ Résoudre problèmes divers
                1. ✅ healthcheck traefik > OK direct
                2. ✅ "traefik.http.routers.helloworld.entrypoints=web" ???
                   1. WARN > No entryPoint defined for this router, using the default one(s) instead: [web]
                   2. Vérifier pour https
                   3. > Plus de trace dans les logs
            12. ✅ Rajouter mes recos de sécurité
                1. ✅ Proxy
                2. ✅ Traefik
                3. ✅ Tests hello
            13. 🌱 Répliques
                1. ✅ Tests hello
                2. ✅ Proxy
                3. ❌ Traefik / Published port 80 can be allocated to one container only (traefik + replicas = 2 containers)
                   1. TODO: Fix ?
            14. ✅ Test avec 2 services
            15. ✅ Test sur sous dossier
            16. ✅ Gestion des logs traefik (json + volumes > fichiers sur host)
                1. Docs
                    1. [Official docs](https://docs.traefik.io/observability/logs/)
                    2. [exemple](https://community.containo.us/t/502-bad-gateway-solved/2947)
                    3. [Access logs](https://docs.traefik.io/observability/access-logs/)
                2. ~~/var/log/*~~
                3. Traefik's container > /home/traefik.log
                4. Stored inside a named volume 'logs-traefik' in /home/traefik.log
            17. ✅ [Manage access logs](https://docs.traefik.io/observability/access-logs/)
            18. ✅ HTTPS stuff
                 1. Docs
                    1. [Let's encrypt](https://letsencrypt.org/fr/docs/)
                    2. [Traefik > Automated](https://docs.traefik.io/https/acme/)
                    3. [Traefik > TLS > Routers](https://docs.traefik.io/routing/routers/#tls)
                    4. 💚 [Traefik HTTPS tutorial](https://containo.us/blog/traefik-2-0-docker-101-fc2893944b9d/#i-need-https)
                    5. 💚 [Another tutorial](https://chriswiegman.com/2019/10/serving-your-docker-apps-with-https-and-traefik-2/)
                 2. ✅ Enable in traefik container
                 3. ✅ Enable on stack ~[hello https://test.masamune.fr/](https://test.masamune.fr/)
                 4. ✅ Automatic redirect http to https
                 5. ✅ Switch from Let's encrypt staging (for test purposes)
                    1. 🎉 Works
                 6. 🌱 Implement 1 [certificate per domain (for all sub domains)](https://docs.traefik.io/https/acme/#domain-definition), cf. SANs (Subject Alternative Name)
                    1. Need main DOMAIN.com to point toward the same server
                 7. 🌱 Test on helloDeux & HelloSub
            19. ✅ basic auth
            20. 💩 💩Traefik💩 💩dashboard💩
                1. KO AF, nothing works (routes / https / /dashboard)
                2. The documentation and examples are pure garbage and fucking waste of time
            21. 💩 Problème heure logs traefik (-2)
                1. [Dispo en 1.7](https://docs.traefik.io/v1.7/configuration/logs/#time-zones)
                2. Mais pas en [v2+](https://docs.traefik.io/observability/logs/)
            22. 🌱 Traefik [log rotation](https://docs.traefik.io/observability/logs/#log-rotation)
            23. ✅ Récupérer nomenclature server-related-tutorials/01-docker/04-my-tests/09-traefik-curated/Nomenclatures.md
                1. ✅ Fixer arborescence prod ( docker_peon/core/ )
         3. ✅ Vérifier que l'accès est bien bloqué en ligne [http://localhost:2375/version](http://localhost:2375/version) avec l'IP du serveur
         4. ✅ Tester routing via 3 conteneurs alakon > test.DOMAIN.COM, grafana.DOMAIN.COM & test.DOMAIN.COM/sub
            1. ✅ Lint user & attention network prefixe + "core-"
            2. ✅ Fix tutum/helloworld (nginx & phpfpm) massive BS
               1. nginx internal port 8080
               2. Unbinding ports
               3. Proper volumes + rights
               4. config instead of bind
               5. etc. etc., cf. server-related-tutorials/01-docker/04-my-tests/09-traefik-curated/11-prod-hello-curated/08-hello-stack-curated-comments/README.md
         5. ✅ Host / Automation via ansible
            1. ✅ Role > traefik install
            2. ✅ Role > traefik init
            3. ✅ Role > Nginx custom conf install
            4. ✅ Role > tutum test install
            5. ✅ Role > tutum test init & start
            6. 🌱🚑 Role > tutum test stop

### Docker security benchmark checklist

Depending on the use of docker/compose/service/stack, then all used with ansible

Legend:
✅                    / Standalone containers (docker run) OK
✅✅                  / run + Ansible
✅✅✅               / Compose containers
✅✅✅✅             / Compose + Ansible
✅✅✅✅✅          / Services
✅✅✅✅✅✅       / Services + Ansible
✅✅✅✅✅✅✅    / Stack
✅✅✅✅✅✅✅✅  / Stack + Ansible

- ✅✅✅✅✅❌✅✅ [Forcer l'utilisateur](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-2---set-a-user)
  - [Voir n°2](https://snyk.io/blog/10-docker-image-security-best-practices/)
  - Can't use a specific user for nginx on port 80 as it requires unprivileged ports & sysctl param does not exist in ansible docker_swarm_service
- ✅✅✅✅❌❌❌❌ [No new privileges flag](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-4---add-no-new-privileges-flag)
  - --security-opt=no-new-privileges, [see](https://www.stackrox.com/post/2017/08/hardening-docker-containers-and-hosts-against-vulnerabilities-a-security-toolkit/#restrict-a-container-from-acquiring-new-privileges)
  - Also in daemon.json
- ✅✅✅✅✅✅✅✅ [Ajouter des labels](https://docs.docker.com/config/labels-custom-metadata/)
  - ✅✅✅✅✅✅✅✅ And to networks
  - ✅✅🌱🌱🌱🌱🌱🌱 And to volumes
- ✅✅✅✅✅✅✅✅ Start containers automatically
- Resources allocations, explications sur la [doc Dcompose](https://docs.docker.com/compose/compose-file/#resources) uniquement
  - ✅✅❌❌✅✅✅✅ Memory soft & hard limit, disable swap
  - ✅✅❌❌🏷️🏷️✅✅ CPU shares
  - ✅✅❌❌❌❌❌❌ Restrict process (pids limit)
  - *Note: Not available for docker-compose in v3. Only v2 or swarm/stack ?*
- ✅✅✅✅✅✅✅✅ Working directory
- ✅✅✅✅✅✅✅✅ Volumes, mounted in /home/the_docker_peon/
  - ✅✅✅✅✅✅✅✅ [read-only flag](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-8---set-filesystem-and-volumes-to-read-only)
- 🌱🌱🌱🌱🌱🌱🌱🌱 No vars or ENV > use secrets or config
  - Specific stuff for [Dstack secrets](https://docs.docker.com/compose/compose-file/#secrets)
- ✅✅✅✅✅✅✅✅ Health checks
- ✅✅✅✅❌❌❌❌ [capabilities drop all, puis autoriser celles nécessaires](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-3---limit-capabilities-grant-only-specific-capabilities-needed-by-a-container)
  - [Docker doc on capabilities](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)
    - deny all “mount” operations;
    - deny access to raw sockets (to prevent packet spoofing);
    - deny access to some filesystem operations, like creating new device nodes, changing the owner of files, or altering attributes (including the immutable flag);
    - deny module loading;
    - and many others.
    - De manière générale, à l'intérieur du conteneur, pas droits pour ssh, cron, logs, hardware, network, NETCAT
  - [Docker security tuning](https://opensource.com/business/15/3/docker-security-tuning)
- ✅✅✅✅❌❌❌❌ [AppArmor > Profil de sécurité](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md#rule-6---use-linux-security-module-seccomp-apparmor-or-selinux)
  - default docker profile : --security-opt apparmor=docker-default
  - Generated new profile with [apparmor tools](https://github.com/docker/labs/tree/master/security/apparmor#step-5-extra-for-experts)
  - Load them thanks to `/ansible/roles/security-apparmor/tasks/main.yml`
  - Use with `docker run --security-opt "apparmor=docker-nginx"` where docker-nginx is the wanted profile
- ✅✅✅✅❌❌❌❌ Seccomp / Utiliser le profile par defaut [seccomp](https://docs.docker.com/engine/security/seccomp/)
  - --security-opt seccomp=/etc/docker/seccomp-profiles/default-docker-profile.json
- Specific to swarm
  - 🔒🔒🔒🔒✅✅🏷️✅ Replicas
    - docker stack deploy > max 2 replicas or services keeps up & down...
  - 🔒🔒🔒🔒✅❌❌❌ [max replicas](https://docs.docker.com/engine/reference/commandline/service_create/)
    - max replicas not available in ansible docker_swarm_service, new from docker-compose 3.8
    - Needs server update (docker ce ?) and docker-compose.yml spec update (3.7 > 3.8) in order to work
  - #specify-maximum-replicas-per-node---replicas-max-per-node)
  - 🔒🔒🔒🔒✅✅✅✅ [Update behavior](https://docs.docker.com/engine/swarm/services/#configure-a-services-update-behavior)
  - 🔒🔒🔒🔒✅✅✅✅ [Rollback behavior](https://docs.docker.com/engine/swarm/services/#roll-back-to-the-previous-version-of-a-service)
  - 🔒🔒🔒🔒✅✅✅✅ [Labels](https://docs.docker.com/engine/reference/commandline/service_create/#set-metadata-on-a-service--l---label)
