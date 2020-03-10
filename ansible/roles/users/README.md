# Users

In order to improve security & architecture, creating dedicated users with specific roles.

Password connexion will be desactivated, and replace with SSH key/pass phrases.

## Users roles

### root

Basic user at installation, must be desactivated for security purposes.

Will be replaced with a new user who can sudo.

### the builder

Is replacing root, for setup purposes.

### the CRON guy

Will manage repeated/planned CRON tasks, and only him.

### the Docker guy

Will manage everything related to Docker.

## Create a SSh key

How to manually/locally create a SSH key for a user.

TODO: automate.

```bash
# Local / WSL
> ssh-keygen -f ~/.ssh/MY_USER-ssh-key-ed25519 -a 100 -t ed25519 -C "my@email.com"
# pass phrase / https://passwordsgenerator.net/ / 64 chars string number special chars
> aSecretPassPhrase

# Ajouter à l'agent SSH local
> eval `ssh-agent`
> ssh-add ~/.ssh/MY_USER-ssh-key-ed25519
# pass phrase
```
