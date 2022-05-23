# Shame

TODO: one day

1. Sftp > connect users through ssh keyz & passphrase
   1. Couldn't make it to work
      1. docker_guy > ne peut pas bind des fichiers (clés ssh) depuis l'hôte afin de les injecter dans le conteneur
      2. docker_peon > peut bind mais ne peux pas lancer le sftp, pas les droits
      3. >> Ne pas bind, named volume pour les clés.. > Tjr ko ca écrase sshd_config + usine a gaz
      4. Putain de non root user
