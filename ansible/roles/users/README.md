# Create a SSh key

```bash
# Local / WSL
> ssh-keygen -f ~/.ssh/MY_USER-ssh-key-ed25519 -a 100 -t ed25519 -C "my@email.com"
# pass phrase / https://passwordsgenerator.net/
> aSecretPassPhrase

# Ajouter à l'agent SSH local
> eval `ssh-agent`
> ssh-add ~/.ssh/MY_USER-ssh-key-ed25519
# pass phrase
```
