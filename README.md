# nextcloud_docker
Nextcloud in docker-compose, with nginx reverse-proxy

## 1. Modify docker-compose.yml
This docker-compose.yml is modified version from [Docker hub - Nextcloud](https://hub.docker.com/_/nextcloud), with fix from [this issue](https://github.com/nextcloud/docker/issues/1536).

```
version: '2'

volumes:
  nextcloud:
  db:

services:
  db:
    image: mariadb:10.5
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=MODIFY_ME
      - MYSQL_PASSWORD=MODIFY_ME
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud
    restart: always
    ports:
      - 26321:80
    links:
      - db
    volumes:
      - ./nextcloud:/var/www/html
    environment:
      - MYSQL_PASSWORD=MODIFY_ME
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db
```

## 2. Run docker and configure nextcloud
With configured reverse proxy on nginx, enter your domain and finish configuration (admin account).

## 3. Fix localhost redirect problem
With fix from [here](https://help.nextcloud.com/t/nextcloud-docker-redirects-to-localhost/50128/2).

```
vim nextcloud/config/config.php
+  'overwritehost'     => 'yourdomain.tld',
+  'overwriteprotocol' => 'https',
```
