# 🐛 Container won't start if too many already started

.. but will start as soon as another one is shutdown.

18/05/2022

keywords : container not starting, conteneur ne demarre pas, attend qu'un autre conteneur soit arrété, cpu, ram, disponible, disponibilité, available

## Processors attribution calculs

```bash
# How many CPUs
lscpu
# [...]
CPU(s):  8
```

### CPU recap & totals

Docker CPU reservation thoughts :

- You got 1 proc : 0.5 = half, 1 = 100%
- You got 2 proc : 0.5 = half **one proc**, 1 = 1 proc, 1.5 = 1.5 proc

Objective **Reservations < 8**

Test with less reserved CPU on last wp : .25/.5 instead of 2/4

---

| *Container name* | *CPU reservation* | *CPU Limit* | *(with replicas ?)* | *CPU res* | *CPU limit* |
|---|---|---|---|---|---|
| dev_fof_db | 0.25 | .5 |   |   |   |
| dev_fof_wp | 2 | 4 |   |   |   |
| fof_db | 0.25 | .5 |   |   |   |
| fof_wp | 0.25 | .5 |   |   |   |
| lapie_db | 0.25 | .5 |   |   |   |
| lapie_wp | 2 | 4 |  |   |   |
| ald_db | 0.25 | .5 |   |   |   |
| ald_wp | 0.25 | .5 |   |   |   |
| hello | 0.25 | .5 | 2 replicas | .5 | 1 |
| test_db | 0.25 | .5 |   |   |   |
| **Sous Total** | 6 | 12 |   | 6.25 | 12.5 |
| 💩 KO test_wp | 2 | 4 |   |   |   |
| **Total** | 💥 8 | 16 |   | 💥 8.25 | 16.5 |
| 🧠 test_wp with less CPU | 0.25 | .5 |   |   |   |
| **Total** | ✅ 6.25 | 12.5 |   | ✅ 6.5 | 13 |

## The documentation is misleading

In the most SEO favorable documentation [resource_constraints#limit-a-containers-access-to-memory](https://docs.docker.com/config/containers/resource_constraints/#limit-a-containers-access-to-memory), limit is the point where container will crash/reboot, and reservation is a "soft" limit.

You must use the [docker compose documentation](https://docs.docker.com/compose/compose-file/compose-file-v3/#resources), which has same terms but different applications.

More details in the [CLI counterpart](https://docs.docker.com/engine/reference/commandline/service_create/#specify-memory-requirements-and-constraints-for-a-service---reserve-memory-and---limit-memory).

```yaml
version: "3.9"
services:
  redis:
    image: redis:alpine
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
        reservations:
          cpus: '0.25'
          memory: 20M
```

> In this general example, the redis service is constrained to use no more than 50M of memory and 0.50 (50% of a single core) of available processing time (CPU), and has 20M of memory and 0.25 CPU time **reserved** (as always available to it).

It 💩 **implicitly** 💩 says that is *reservations* requirements aren't met, **container won't start**, despite having `reservations < limit`.

## Recommandations

Have some basic math to allow reservations `~CPUs / Number of running containers` or do not set reservations if the number of containers is fluctuant.

I'd also recommand to check technologies/images requirements (theorical) through [documentation](https://wordpress.org/about/requirements/) and actual usage (real) through `docker stats`.

~WordPress + woocommerce + stress (spam F5)

- WordPress RAM : ~450mo top
- WordPress CPU : CPU reserved 2 > 90% tops
- MariaDB RAM : ~105mo top
- MariaDB CPU : CPU reserved .25 > 15% tops

I'll configure:

- reservation to idle usage
  - Or no reservations if I reach the hardware limit with multiple containers
- limit to the double of stress tests
