# Shame

TODO: one day

1. Sftp > connect users through ssh keyz & passphrase
   1. Couldn't make it to work
      1. docker_guy > ne peut pas bind des fichiers (clés ssh) depuis l'hôte afin de les injecter dans le conteneur
      2. docker_peon > peut bind mais ne peux pas lancer le sftp, pas les droits
      3. >> Ne pas bind, named volume pour les clés.. > Tjr ko ca écrase sshd_config + usine a gaz
      4. Putain de non root user

## End of developement

Too much hard work, too complex, frustrating

### Leftovers

### Serveur

#### 🚀 Terminer les bases (sftp, adminer, mails) + dev/prod + Lapie secrets

1. ✅ Rôle accès sftp
   1. 📌 Tests
      1. 🔍 Image serveur sftp avec volumes & clés ssh [atmoz](https://hub.docker.com/r/atmoz/sftp)
      2. ✅ Test sans volume
      3. 🐛✅ Test avec volume named volume
         1. ✅ Ajuster le répertoire courant, à cause de la chroot prison > `/home/user/sub/`
         2. ✅ Ajuster user:group cast
         3. ✅ Volume > `readonly=false`
         4. ✅ Attention aux droits des fichiers `wp-config.php` en 440 > Pas modifiable sans ajuster les droits avant
      4. 💩 Tester avec clés ssh
         1. 💡 Pas de docker secret sur les conteneurs pour les tests > adapter lors du passage en service
         2. 💩 Rôle génération de clés ssh
            1. ✅ Link to clients generation vars
            2. ✅ Generate ssh passphrase
            3. ✅ Generate ssh key in docker_peon/clients/folder
            4. ✅ Local gathering of both private & public + generated/
            5. 📌💩 Tests
               1. docker_guy > ne peut pas bind des fichiers (clés ssh) afin de les injecter dans le conteneur
               2. docker_peon > peut bind mais ne peux pas lancer le sftp
               3. >> Ne pas bind, named volume pour les clés.. > Tjr ko ca écrase sshd_config + usine a gaz
               4. 💩 Connexion via mots de passe oseb plein le cul
   2. ✅🧽 Supprimer les anciens tests/roles
      1. ✅ Utilisateurs ubuntu de test sudo deluser
      2. ✅ Playbooks 94/95
      3. ✅ Rôles sftp*
      4. ✅ `/nomenclature-and-folder-tree.md` + docker_peon/
      5. ✅ Rôle de forge > ~vars
      6. ✅ `ansible/roles/stack-web-wordpress--generate-playbooks/vars` > sftp: ssh_passphrase
      7. ✅ host > `/home/the_docker_peon/clients`
      8. ✅ host > `/home/the_docker_peon/tests`
      9. ✅ Ranger docs tests
   3. ✅ Automatiser forge
      1. ✅ wordpress-files volume
         1. ✅ Maj doc identifiants client
      2. ✅🐛 Tester avec le même port de sortie sur plusieurs conteneurs
         1. ✅ Nope > Port unique
2. 🚀 Rôle accès bdd
   1. [Conteneur](https://hub.docker.com/_/adminer)
3. Gestion des mails propre
    1. Connexion au serveur SMPT du serveur ? cf. utils-emails
    2. [Conteneur postfix ?](https://hub.docker.com/_/postfixadmin)
    3. Ajout SPF/DKIM/DMARC
    4. Maj lapie & nonore
4. ⏳ Lapie HMAC auto
    1. Lapie kek.php (Crédit agricole > génération d'un fichier kek.php à la racine lors de l'insertion de clé HMAC > détruit au reboot du conteneur)
    2. cf. config ? `ansible/generated/tests/masamune/hello--masamune--fr/stack/hello--masamune--fr---nginx--generated.yml`

#### Faire la mise à jour du serveur vers ubuntu 22

1. Backups sites clients
   1. sophie berberian
   2. champagne didier lapie
   3. ald infographie
2. ⏳ Email clients interruption de service
    1. ✅ template
3. Mettre à jour le serveur
    1. Forcer redémarrage > 52
    2. Maintenance > 98
4. Vérifications sites clients toujours en place
   1. Restaurer backup si besoin

#### Passer Tutum en Nginx + php basique

1. Renommer forge a nginx en forge tutum, puis faire un playbook dédié nginx
    1. Faire conteneur en local pour tester installation & configuration simple
       1. Récupérer config faite pour cryptor
    2. Serveur normal & serveur php basique pour les sites non wp
    3. Opti [Nginx](https://hub.docker.com/_/nginx) / test-nginx.masamune.fr
       1. 🔍 [Video configuration](https://www.youtube.com/watch?v=C5kMgshNc6g)
       2. Tune server for [nginx performance](https://www.nginx.com/blog/10-tips-for-10x-application-performance/)
       3. [+1](https://blog.monitis.com/blog/6-best-practices-for-optimizing-your-nginx-performance/)

#### Installer le monitoring

Commencer via 99 crafts puis MOD: 4-setup-core-services.yml

1. Installer webmin ou **netdata** plutot que grafana & autres
   1. Swarm WebUi Portainer
      1. [doc](https://portainer.readthedocs.io/en/stable/deployment.html)
      2. [DS rocks](https://dockerswarm.rocks/portainer/)
   2. Tester en local
2. Alerte si CPU/RAM > 75%
3. Alerte si space disque libre < 20%

#### Gestion dev/prod

1. Prévoir dev & prod > 1 seul script mais url change, même users & pass
    1. Réutiliser `/generated/vars` files ? (WP)
    2. Utiliser docker-compose.override.yml ? [Bonnes pratiques docker/compose](https://nickjanetakis.com/blog/best-practices-around-production-ready-web-apps-with-docker-compose)
       1. Variables d'environnement dans DC
    3. Check ansible > vars d'environnement afin de maj dev. ou prod
    4. Gestion dev/prod : 1 seul fichier
    5. ENV vars ++
    6. Volumes en fonction de l'environnement ¤_¤

#### Cleaner avant de poursuivre le projet

1. Tester Nginx + WordPress, sans bitnami, [cf.](https://wordpress.org/support/article/nginx/)
   1. Voir pour virer bitnami en vrai c'est juste pas clair au niveau de la gestion
   2. Voir pour ajouter REDIS > meilleures perfs si grosses données ? existe en image docker
2. ansible template, au lieu de remplacer variables > [block_start_string](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html#parameter-block_start_string)
3. Opti script maintenance
    1. Ajouter maj OMZ ZSH
4. wp > identifiants > ajouter url wp-admin
5. `/home/singed_the_docker_peon_9f3eqk4s9/configs/masamune/hello--masamune--fr` wtf is that
6. Process récurrent de Maj des images clients

#### Suite Serveur / post migration

1. Activer le [Debug Mode de docker](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)
2. ⏳📌 `/var/log/auth.log` > [audit: backlog limit exceeded](https://aws.amazon.com/fr/premiumsupport/knowledge-center/troubleshoot-audit-backlog-errors-ec2/)
   1. Augmentation de la valeur de 1280 à 8192 via `sudo nano /etc/audit/rules.d/audit.rules PUIS sudo service auditd restart VERIF sudo auditctl -s`
   2. Maj de `/roles/docker-installation/tasks/docker-bench-security.yml` &`/roles/docker-installation/templates/auditd-audit.rules.j2`
3. Gestion des backups
    1. Ajout au CRON
    2. Envoi vers serveur de backup + rotation/sauvegarde incrémentielle
4. Checker ce qui prend de la place sur le disque ~80Go ? 13% de 450 > `docker system df -v` ; cf. backup des volumes
   1. 💥 Tâche ponctuelle clean logs (en attendant automatisation > traefik.log > 200514-traefik.log)
5. Ansible convenience
    1. Clean templating, variable [deprecated ansible_managed](https://docs.ansible.com/ansible/2.4/intro_configuration.html#ansible-managed)
        1. [?](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#default-managed-str)
    2. [ansible prompt](https://docs.ansible.com/ansible/latest/user_guide/playbooks_prompts.html)
6. Containers [Healtcheck](https://blog.sixeyed.com/docker-healthchecks-why-not-to-use-curl-or-iwr/)
7. Pagespeed optimisations w. vanilla websites
8. Tester conteneurs de serveurs (facilité/stabilité/vitesse/http3)
    1. Need modules de cache php activés
    2. Nginx
    3. [Apache](https://hub.docker.com/_/httpd) / test-httpd.masamune.fr
    4. [Caddy](https://hub.docker.com/_/caddy) / test-caddy.masamune.fr
    5. Litespeed : [open](https://hub.docker.com/r/litespeedtech/openlitespeed) / [payant ?](https://hub.docker.com/r/litespeedtech/litespeed)
       1. 2-3 trucs/plugins a regarder en plus pour WP : [doc](https://www.litespeedtech.com/open-source) & [site dédié](https://lscache.io/)
       2. test-litespeed.masamune.fr
    6. 🌱 Chaque serveur > Tester WP (install via wp-cli ?)
9. [Varnish cache](https://varnish-cache.org/)
10. Docker certification [175€](https://success.docker.com/certification)
11. Veille securité
    1. [Docker Production Best Practices from Bret Fisher at DockerCon](https://www.youtube.com/watch?v=V4f_sHTzvCI)
    2. [Container security free pdf](http://containersecurity.tech/)

---
