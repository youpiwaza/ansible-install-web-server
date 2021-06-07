# Users

In order to improve security & architecture, creating dedicated users with specific roles.

Password connexion will be desactivated, and replace with SSH key/pass phrases.

## Users roles

### root

Basic user at installation, must be desactivated for security purposes.

Will be replaced with a new user who can sudo (the builder guy).

### the builder guy

Is replacing root, for setup purposes.

### the CRON guy

Will manage repeated/planned CRON tasks, and only him.

### the docker_guy

Will manage everything related to Docker, and added to the docker group.

Be carefull, as [this group](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user) grants a lot of priviledges.
