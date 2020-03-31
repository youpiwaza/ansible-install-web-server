# Main log commands

```bash
# Services KO
> systemctl --failed

   # Logs de fail de l'initialisation d'un service (ici apparmor)
   > systemctl status apparmor.service

# Logs kernel
> sudo nano /var/log/kern.log

# Autres logs
> dmesg
```
