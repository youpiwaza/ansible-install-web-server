# Identifiants pour le site hey.fr

Généré automatiquement via ansible, cf. [github repo](https://github.com/youpiwaza/ansible-install-web-server/tree/master/ansible/roles/stack-web-wordpress--generate).

## Project

| Variable | Value | Notes |
|-|-|-|
| client_name | masamune |  |
| labels_prefix | fr.masamune.test-wordpress | Docker recommanded notation: `extension.site.subdomain(.SOME-LABEL)`, cf. [docker doc](https://docs.docker.com/config/labels-custom-metadata/#key-format-recommendations) |
| maintainer | masamune.code@gmail.com |  |
| name | test wordpress |  |
| type | test |  |

## Traefik

| Variable | Value | Notes |
|-|-|-|
| public_uri | test-wordpress.masamune.fr | Ensure DNS are set accordingly to point towards host IP. This will point towards 'https://test-wordpress.masamune.fr/' |
| uid | testWordpress__masamune__fr | For router & middleware names generation. Recommanded notation: `subDomain__domain__ext` |

## SFTP

| Variable | Value | Notes |
|-|-|-|
| SFTP_USER | test-wordpress--masamune--fr |  |
| SFTP_PASSWORD | y5TwS4QbBS3s0qrrncl!OuhzOjtrPgJV |  |
| SFTP_PORT | 22200 |  |

## Database

| Variable | Value | Notes |
|-|-|-|
| image | bitnami/mariadb:10.3 |  |
| MARIADB_DATABASE | bitnami_wordpress_drdS87QVpvADFw5W |  |
| MARIADB_PASSWORD | G6Sn3VaLqf2DDL9W |  |
| MARIADB_USER | yAKqwcWYAr9kzLdn |  |
| MARIADB_ROOT_PASSWORD | F9pXgdTAwcWSxH4WnZYex3pLJmR947VZ |  |
| MARIADB_ROOT_USER | root_F5g8H7F |  |

## Wordpress

| Variable | Value | Notes |
|-|-|-|
| image | bitnami/wordpress:5 |  |
| WORDPRESS_TABLE_PREFIX | wp_PL9ZFfYp_ |  |
| WORDPRESS_BLOG_NAME | Sponge Bob's blog |  |
| WORDPRESS_EMAIL | masamune.code@gmail.com |  |
| WORDPRESS_FIRST_NAME | Bob |  |
| WORDPRESS_FIRST_NAME | Sponge |  |
| WORDPRESS_USERNAME | ndj4E2rDbVeRkEHHcu9X7me3asqTNFmzwjKAhU9LbqpMb8Wbcf | 50 chars length / NO SPECIAL CHARS, tends to not match when container is up |
| WORDPRESS_PASSWORD | ndj4E2r!bVeRkE?Hcu8%7me3as^TNFmzwjKAh)U9Lbqp&b8Wbcf | 50 chars length / Wordpress recommandations : `! ? % ^ & ) /`, No no `$ ' "` |
