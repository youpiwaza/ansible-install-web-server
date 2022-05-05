# Users

In order to improve security & architecture, creating dedicated users with specific roles.

Password connexion will be desactivated, and replace with SSH key/pass phrases.

## Users roles

### root

Basic user at installation, must be desactivated for security purposes.

Will be replaced with a new user who can sudo (the_builder_guy).

### the_builder_guy

Is replacing root, for setup purposes.

### the_cron_guy

Will manage repeated/planned CRON tasks, and only him.

### the_docker_guy

Will manage everything related to Docker, and added to the docker group.

Be carefull, as [this group](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user) grants a lot of priviledges.

## Sftp users

Some of the tasks present in this folder are also re-used to create sftp users, so take that in consideration before editing the role :)

cf. playbooks & roles:

- 94-create-standalone-sftp-user.yml
- 95-remove-sftp-user.yml
- 10-forge-a-nginx.yml
- 20-forge-a-wordpress.yml
- roles/sftp-create-user
- roles/sftp-remove-user
