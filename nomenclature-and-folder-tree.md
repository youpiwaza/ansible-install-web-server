# Nomenclature and folder tree

How to name and store stuff in order for everything to stay lint and clear.

## Basics

Differentiate core services from (client) specific websites.

All the core stuff will be prefixed with "core".

### Dashed & Uri notations

In order to stay homogenous & crystal clear, dash notation will be used this way :

- One dash to separate words, eg. masamune-website
- Two dashes to separate subdomains, extensions, eg. dev--masamune-website--fr
- Three dashes to separate context, eg. volume---dev--masamune-website--fr---wordpress-files

Do not use underscores.

## Folder tree

### Administration & core

- When naming files & folders, use a dash notation, ex: "some-stuff.md".
- Everything regarding **server** tests & maintenance will be set in "/home/the_builder_guy" folder.
- Everything regarding **docker** tests & maintenance will be set in "/home/the_docker_guy" folder.
- Everything regarding **docker containers** execution will be set in "/home/the_docker_peon" folder.
- Everything concerning the core service will be set in "/home/the_docker_peon/core/TYPE-OF-SERVICE/SERVICE/".
- Basic configuration examples (~nginx) will be set in each project. Orignials configs file will be stored as template, in this repositories.
  - This will allow the execution of generic tests
  - Naming convention: TYPE---THE-SUB--THE-DOMAIN--EXT---STACK-TYPE--DESC
    - Ex: test---test-wordpress--masamune--fr---wordpress--files
- All services & stack will be generated locally and uploaded to the server, with an extra timed version for modification history.
  - Example of files concerned for core service / traefik
    - Sources files will be .j2 templates:    `ansible/roles/core-reverse-proxy-traefik--generate/templates/traefik.j2`
      - It's recommanded to manually make an history, or at least comment :)
    - Local / Generated file:                 `ansible/generated/core/reverse-proxy/traefik/traefik--generated.yml`
    - Local / History / Generated file:       `ansible/generated/core/reverse-proxy/traefik/traefik--generated-2021-06-03--11h48m36s.yml`
    - Server / Generated file:                `/home/THE_DOCKER_PEON/core/reverse-proxy/traefik/traefik--generated.yml`
      - ^ This is the one actually **deployed**
      - ^ This allow to update the running stacks through the same files (consecutive deploys), and always know which one is running
    - Server / History / Generated file:      `/home/THE_DOCKER_PEON/core/reverse-proxy/traefik/traefik--generated-{{ currentDateTime }}.yml`
  - Regarding clients stacks : 2 parts : Generate the playbooks generator, then run those new playbooks
    - Example with wordpress
      - Run the `ansible/20-forge-a-wordpress-stack.yml` playbook, which uses for vars
        - `roles/stack-web-wordpress--generate/vars/tests/masamune/test-wordpress--masamune--fr/test-wordpress--masamune--fr---vars.yml`
      - It will generate the example playbook `ansible/generated/tests/masamune/test-wordpress--masamune--fr/200---test-wordpress--masamune--fr---forge-wordpress-stack-generated.yml`
        - which will, when run, generate, upload & deploy a dedicated wordpress stack
        - cf. `ansible/generated/tests/masamune/test-wordpress--masamune--fr/test-wordpress--masamune--fr---wordpress--generated.yml`

### Clients

- Clients will have a dedicated folder, containing a dedicated folder for every client and inside, a dedicated fodler for every project.
- A project name will be, if possible, the uri of the website (with subdomain and extension), else the project name (ex: tests/labs on subdomains..).

### Example folder tree

#### Local

Clients' crafted/generated stuff, must be backed-up in a private git repository

- /ansible
  - /stack-web-nginx--generate-playbooks/vars/*
  - /stack-web-wordpress--generate-playbooks/vars/*

Which allow the generation of

- /generated
  - **Nginx example**
  - /TYPE/CLIENT/DASHED-URI
    - /history
      - /sub-folders-*
        - *-generated-2021-06-03--11h48m36s.wtv
    - /stack
      - DASHED-URI---nginx--generated.yml
      - DASHED-URI---nginx--generated.conf
    - README-generated.md
    - 100---DASHED-URI---nginx-stack--start--generated.yml // Generate or update, then deploy
    - 100---DASHED-URI---nginx-stack--stop--generated.yml // Stop, preventing auto-reboot
    - 100---DASHED-URI---nginx-stack--uninstall--generated.yml // Stop, then remove volumes
    - 100---DASHED-URI---nginx-stack--backup-volumes--generated.yml // Make an archive file (.tar) on the server (extract docker named volumes to host)

  - **Wordpress example**
  - /TYPE/CLIENT/DASHED-URI
    - /history
      - /sub-folders-*
        - *-generated-2021-06-03--11h48m36s.wtv
    - /stack
      - DASHED-URI---wordpress--generated.yml
    - /vars-files
      - WORDPRESS_USERNAME-generated.txt
    - DASHED-URI---ids-generated.md
    - DASHED-URI---README-generated.md
    - 200---DASHED-URI---forge-wordpress-stack--generated.yml

#### Server

- /home
  - /the-builder-guy
    - /tests
      - /some-server-test
    - /backups
      - /volumes
        - /YEAR
        - /2021
          - /TYPE
          - /clients
          - /manual-backups
            - /CLIENT-NAME
            - /masamune
              - /DASHED-URI
              - /dev--masamune--fr
                - VOLUME-NAME---backup---$(date +%Y-%m-%d--%Hh%Mm%Ss).tar
                - client---dev--masamune--fr---wordpress--db---backup---2021-05-27--11h23m57s.tar
                - test---hello--masamune--fr---nginx--logs---backup---latest.tar
          - /core
            - /traefik
          - /manual-backups
            - /VOLUME-NAME
              - VOLUME-NAME---backup---$(date +%Y-%m-%d--%Hh%Mm%Ss).tar
          - /tests
            - /wtv
    - /tests
      - /some-docker-test
  - /the-docker-peon
    - /clients
      - /spongebob
        - /spongebob--com                     # spongebob.com
          - client---spongebob--com---wordpress--generated.yml
          - client---spongebob--com---wordpress--generated-2021-06-03--11h48m36s.yml
          - client---spongebob--com---wordpress--generated.conf
          - client---spongebob--com---wordpress--generated-2021-06-03--11h48m36s.conf
          - agenda.yml                        # Sub folder dedicated container > spongebob.com/agenda/
        - /sub--spongebob--com                # sub.spongebob.com
          - client---sub--spongebob--com---wordpress--generated.yml
          - client---sub--spongebob--com---wordpress--generated-2021-06-03--11h48m36s.yml
        - ~~/mini-website-preview~~           # Always use Uri notation, reflecting https access ; if not applicable go local
          - main.yml
        - _websites_files_sftp_access_chroot_prison/    # Multiples chroot prison root, cf. sftp access.                    / root:root 755
          - /CLIENT-NAME---DASHED-URI                   # 1 chroot prison AND 1 sftp user per site                          / root:root 755
          - /bob---spongebob--com                       # chroot prison for bob's spongebob.com. root folder is mandatory   / root:root 755
            - /.cache/                                  # mandatory                                                         / bob:bob 700
            - /.ssh/                                    # mandatory                                                         / bob:bob 700
            - /README.md                                # root:root 311 -rw-r--r--
            - /my-stuff                                 # user dedicated folder                                             / bob:bob 700
              - /README.md                              # root:root 311 -rw-r--r-- > Ask for temp container for sftp
              - /configs                                # docker bind config accessible through temp containers ?
              - /volume1                                # docker bind volumes accessible through temp containers
              - /volume2                                # docker bind volumes accessible through temp containers
          -/bob---sub--bob--com
    - /core
      - /reverse-proxy
        - /traefik
          - traefik--generated.yml
          - traefik--generated-2021-06-03--11h48m36s.yml

## Naming conventions

### Networks

TYPE---THE-SUBDOMAIN--THE-DOMAIN--EXT[---DESCRIPTION]

Examples:

- core---traefik-public--access-internet
- client---dev--spongebob--com
- client---spongebob--com
- client---sub--spongebob-other-website--com

#### Network recommandations

Containers will access the internet through the overlay attachable "core---traefik-public" network.

- Be sure to connect only required containers ports (~webserver:80 & :443)
- Use a private network for inner project containers' connections.

### Project & datas storages

~~As much as possible, datas will be stored in a /home/STUFF folder, to facilitate rights managements.~~

No, for security reasons, named volumes will be used (automaticcaly stored in /docker/ folder..).

Access to data will be available through

- Manual access:
  - `docker exec bash` into the container
  - `docker run` a temp container with a temp volume binded to host
- Auto/client access
  - `docker run` a temp container with a temp volume binded to host's DOCKER_PEON folder, allowing SSH restricted access

See the [documentation](./main-and-logs-commands.md) for more examples.

---

Clients project will be splitted in 3:

- Generic images of technology used (ex: nginx, wordpress, ..)
- Client/project files, stored on github/gitlab
  - Injected in projects' dedicated named volumes according to needs, through ansible
- Clients generated datas (uploads, database contents)
  - Stored in named volumes
    - Will accessed by temporaries sftp user + dedicated container with bind volumes

#### Named volumes

TYPE---THE-SUBDOMAIN--THE-DOMAIN--EXT---TECHNO-DESCRIPTION

Examples:

- core---traefik--logs
- client---dev--spongebob--com---wordpress-database
- client---spongebob--com---wordpress-files
- client---sub--spongebob-other-website--com---wordpress-database

Ex: for traefik logs, they are stored in /home/logs/traefik.log in the core---traefik-logs named volume.

Note: Folders populated in named volume are described in relative docker-compose .yml files, used to start the container.

#### Volumes recommandations

- Do not use volumes for containers configurations, use **config**, cf. [dedicated tests](https://github.com/youpiwaza/server-related-tutorials/blob/master/01-docker/04-my-tests/09-traefik-curated/11-prod-hello-curated/08-hello-stack-curated-comments/README-use-docker-config.md).
- Named volumes are for website contents : project files & clients datas
  - Website content should be independant from container's image, to facilitate updates.
  - **DO NOT mount /generated/ folders**, are they can provoke conflicts between replicas.
- Used named volumes with stacks
  - bind volumes are a security risk
  - anonymous volumes are destroyed when/if stacks are shutdown (manually or failures)

#### SFTP access

As datas are stored in named volumes, and users should still have access to their datas, here's the plan:

- Each website will have a dedicated sftp (only) user
- This user will be restricted to it's own website folder (cf. the_docker_peon/clients/_websites_files_sftp_access_chroot_prison/CLIENT-NAME---DASHED-URI/ )
- In this folder, temp containers will mount bind volumes, granting access to named volumes
