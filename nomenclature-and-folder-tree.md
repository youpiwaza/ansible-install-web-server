# Nomenclature and folder tree

How to name and store stuff in order for everything to stay lint and clear.

## Basics

Differentiate core services from (client) specific websites.

All the core stuff will be prefixed with "core".

## Folder tree

When naming files & folders, use a dash notation, ex: "some-stuff.md".

Everything regarding servers tests & maintenance will be set in "/home/the_builder_guy" folder.

Everything regarding docker tests & maintenance will be set in "/home/the_docker_guy" folder.

Everything regarding docker containers execution will be set in "/home/the_docker_peon" folder.

Everything concerning the core service will be set in "/home/the_docker_peon/core/TYPE-OF-SERVICE/SERVICE/".

Services template will not be stored on the host, but in the ansible repository.

Clients will have a dedicated folder, containing a dedicated folder for every client and inside, a dedicated fodler for every project.

A project name will be, if possible, the uri of the website (with subdomain and extension), else the project name.

Example folder tree:

- /home
  - /the_builder_guy
    - /tests
      - /some-server-test
  - /the_docker_guy
    - /tests
      - /some-docker-test
  - /the_docker_peon
    - /core
      - /reverse-proxy
        - /traefik
    - /clients
      - /spongebob
        - /spongebob-com            # spongebob.com
          - main.yml
          - agenda.yml              # Sub folder dedicated container > spongebob.com/agenda/
        - /sub-spongebob-com        # sub.spongebob.com
          - main.yml
        - /mini-website-preview
          - main.yml

## Naming conventions

### Networks

TYPE-TECHNO/SUB_CLIENT_URI-[DESCRIPTION]

Examples:

- core-traefik-public
- dev-spongebob_com
- prod-spongebob_com
- prod-sub_spongebob_com

#### Network recommandations

Containers will access the internet through the overlay attachable "core-traefik-public" network.

- Be sure to connect only required containers (~webserver:80 & :443)
- Use a private network for project containers' connections.

### Named volumes

TYPE-TECHNO/SUB_CLIENT_URI-DESCRIPTION

Examples:

- core-traefik-logs
- dev-spongebob_com-database
- prod-spongebob_com-database
- prod-sub_spongebob_com-database

As much as possible, datas will be stored in a /home/STUFF folder, to facilitate rights managements.

Ex: for traefik logs, they are stored in /home/logs/traefik.log in the core-traefik-logs named volume.

#### Volumes recommandations

- Do not use volumes for containers configurations, use config.
- Named volumes are for website content & tmp files only.
  - Website content should be independant from container's image, to facilitate updates.
- Used named volumes only with stacks
  - bind volumes are a security risk
  - anonymous volumes are destroyed when/if stacks are shutdown (manually or failures)