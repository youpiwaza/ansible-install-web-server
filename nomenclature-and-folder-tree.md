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
  - Example of files concerned for traefik
    - Sources files will be .j2 templates:    `ansible/roles/core-reverse-proxy-traefik--generate/templates/traefik.j2`
      - It's recommanded to manually make an history, or at least comment :)
    - Local / Generated file:                 `ansible/generated/core/reverse-proxy/traefik/traefik--generated.yml`
    - Local / History / Generated file:       `ansible/generated/core/reverse-proxy/traefik/traefik--generated-2021-06-03--11h48m36s.yml`
    - Server / Generated file:                `/home/DOCKER_PEON/core/reverse-proxy/traefik/traefik--generated.yml`
      - ^ This is the one actually **deployed**
      - ^ This allow to update the running stacks through the same files (consecutive deploys), and always know which one is running
    - Server / History / Generated file:      `/home/DOCKER_PEON/core/reverse-proxy/traefik/traefik--generated-{{ currentDateTime }}.yml`

### Clients

- Clients will have a dedicated folder, containing a dedicated folder for every client and inside, a dedicated fodler for every project.
- A project name will be, if possible, the uri of the website (with subdomain and extension), else the project name (ex: tests/labs on subdomains..).

### Example folder tree

- /home
  - /the-builder-guy
    - /tests
      - /some-server-test
  - /the-docker-guy
    - /backups
      - /volumes
        - /YEAR
        - /2021
          - /TYPE
          - /clients
            - /CLIENT-NAME
            - /masamune
              - /DASHED-URI
              - /dev--masamune--fr
                - VOLUME-NAME---backup---$(date +%Y-%m-%d--%Hh%Mm%Ss).tar
                - client---dev--masamune--fr---wordpress--db---backup---2021-05-27--11h23m57s.tar
          - /core
            - /traefik
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
