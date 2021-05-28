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
- Basic configuration examples (~nginx) will be set in "/home/the_docker_peon/config/TYPE-OF-SERVICE/SERVICE/" in addition to ansible repo.
  - This will allow the execution of generic tests
  - Naming convention: CONCERNED-IMAGE---DESCRIPTION---ORIGINAL_FILENAME
    - Ex: tutum---customUser-p8080-php---nginx.conf
- Services templates (ex: traefik.yml) will not be stored on the host, but in the ansible repository.
  - ex: ansible/roles/core-install-reverse-proxy/templates/traefik.j2

### Clients

- Clients will have a dedicated folder, containing a dedicated folder for every client and inside, a dedicated fodler for every project.
- A project name will be, if possible, the uri of the website (with subdomain and extension), else the project name (ex: tests/labs on subdomains..).

### Example folder tree

- /home
  - /the-builder-guy
    - /tests
      - /some-server-test
  - /the-docker-guy
    - /tests
      - /some-docker-test
  - /the-docker-peon
    - /clients
      - /spongebob
        - /spongebob--com                     # spongebob.com
          - wordpress-stack--generated.yml
          - agenda.yml                        # Sub folder dedicated container > spongebob.com/agenda/
        - /sub--spongebob--com                # sub.spongebob.com
          - main.yml
        - ~~/mini-website-preview~~           # Always use Uri notation, reflecting https access ; if not applicable go local
          - main.yml
    - /config
      - /webserver
        - /nginx
          - nginx---custom-user-p8080---nginx.conf
          - tutum---custom-user-p8080-php---nginx.conf
    - /core
      - /reverse-proxy
        - /traefik
          - traefik.yml

## Naming conventions

### Networks

TYPE---SUBDOMAIN--CLIENT-URI--EXT[---DESCRIPTION]

Examples:

- core---traefik-public
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

TYPE---SUB--CLIENT-URI--EXT---TECHNO-DESCRIPTION

Examples:

- core---traefik-logs
- client---dev--spongebob--com---wordpress-database
- client---spongebob--com---wordpress-files
- client---sub--spongebob-other-website--com---wordpress-database

Ex: for traefik logs, they are stored in /home/logs/traefik.log in the core---traefik-logs named volume.

Note: Folders populated in named volume are described in relative docker-compose .yml files, used to start the container.

#### Volumes recommandations

- Do not use volumes for containers configurations, use **config**, cf. [dedicated tests](https://github.com/youpiwaza/server-related-tutorials/blob/master/01-docker/04-my-tests/09-traefik-curated/11-prod-hello-curated/08-hello-stack-curated-comments/README-use-docker-config.md).
- Named volumes are for website contents : project files & clients datas
  - Website content should be independant from container's image, to facilitate updates.
  - **DO NOT mount /tmp/ folders**, are they can provoke conflicts between replicas.
- Used named volumes with stacks
  - bind volumes are a security risk
  - anonymous volumes are destroyed when/if stacks are shutdown (manually or failures)
