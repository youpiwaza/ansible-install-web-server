# Post install verifications

```bash
# Is aby services KO ?
> systemctl --failed

   # If needed, deeper logs of down services (here 'apparmor')
   > systemctl status apparmor.service

# Logs kernel
> sudo nano /var/log/kern.log

# Other logs
> dmesg
```
