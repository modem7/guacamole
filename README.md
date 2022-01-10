[![Docker Pulls](https://img.shields.io/docker/pulls/modem7/guacamole)](https://hub.docker.com/r/modem7/dnscrypt-proxy) 
![Docker Image Size (tag)](https://img.shields.io/docker/image-size/modem7/guacamole/latest) 
[![Build Status](https://drone.modem7.com/api/badges/modem7/guacamole/status.svg)](https://drone.modem7.com/modem7/guacamole) 
[![GitHub last commit](https://img.shields.io/github/last-commit/modem7/guacamole)](https://github.com/modem7/guacamole)

## Guacamole all in one image with Powerline and Powerline fonts for Oh-My-ZSH
Based on https://github.com/abesnier/docker-guacamole

![image](https://user-images.githubusercontent.com/4349962/137602364-3f32811b-093a-4d8b-bbde-7c6c86fdbbf3.png)

**:construction: This is a fork of oznu/docker-guacamole, updated to tomcat9.0.56 (guacamole is not compatible with tomcat10), postgresql 13, guacamole 1.4.0, and s6_overlay 2.2.**

**There is a bug when updating from oznu's container (running guacamole 1.2.0 and postgresql 9) to this one. If something does not work, delete the /config folder and relaunch the container. Be careful, by doing so, you will lose your previous setup. I am trying to find a solution still.** 

# What's new

2022-01-03 - updated to version 1.4.0


# Available tags
| Tag | Description |
| :----: | --- |
| latest | Based on tomcat:9.0.56-jre11 |
| 1.4.0 | Based on tomcat:9.0.56-jre11 |
| 1.3.0 | Based on tomcat:9.0.56-jre11 |

# Docker Guacamole

A Docker Container for [Apache Guacamole](https://guacamole.apache.org/), a client-less remote desktop gateway. It supports standard protocols like VNC, RDP, and SSH over HTML5.

[![IMAGE ALT TEXT](http://img.youtube.com/vi/esgaHNRxdhY/0.jpg)](http://www.youtube.com/watch?v=esgaHNRxdhY "Guacamole 0.9.4 Demo")

This container runs the guacamole web client, the guacd server and a postgres database.

## Usage

```shell
docker run \
  -p 8080:8080 \
  -v </path/to/config>:/config \
  modem7/guacamole
```

## Parameters

The parameters are split into two halves, separated by a colon, the left hand side representing the host and the right the container side.

* `-p 8080:8080` - Binds the service to port 8080 on the Docker host, **required**
* `-v /config` - The config and database location, **required**
* `-e EXTENSIONS` - See below for details.

## Enabling Extensions

Extensions can be enabled using the `-e EXTENSIONS` variable. Multiple extensions can be enabled using a comma separated list without spaces.

For example:

```shell
docker run \
  -p 8080:8080 \
  -v </path/to/config>:/config \
  -e "EXTENSIONS=auth-ldap,auth-duo"
  modem7/guacamole
```

Currently the available extensions are:

* [1.3.0] [1.4.0] auth-ldap - [LDAP Authentication](https://guacamole.apache.org/doc/gug/ldap-auth.html)
* [1.3.0] [1.4.0] auth-duo - [Duo two-factor authentication](https://guacamole.apache.org/doc/gug/duo-auth.html)
* [1.3.0] [1.4.0] auth-header - [HTTP header authentication](https://guacamole.apache.org/doc/gug/header-auth.html)
* [1.3.0] [1.4.0] auth-cas - [CAS Authentication](https://guacamole.apache.org/doc/gug/cas-auth.html)
* [1.3.0] [1.4.0] auth-openid - [OpenID Connect authentication](https://guacamole.apache.org/doc/gug/openid-auth.html)
* [1.3.0] [1.4.0] auth-totp - [TOTP two-factor authentication](https://guacamole.apache.org/doc/gug/totp-auth.html)
* [1.3.0] [1.4.0] auth-quickconnect - [Ad-hoc connections extension](https://guacamole.apache.org/doc/gug/adhoc-connections.html)
* [1.3.0] [1.4.0] auth-saml - [SAML Authentication](https://guacamole.apache.org/doc/gug/saml-auth.html)
* [1.4.0] auth-sso - SSO Authentication metapackage, contains classes for CAS, OpenID and SAML authentication (see links above)
* [1.4.0] auth-json - [Encrypted JSON Authentication](https://guacamole.apache.org/doc/gug/json-auth.html)




You should only enable the extensions you require, if an extensions is not configured correctly in the `guacamole.properties` file it may prevent the system from loading. See the [official documentation](https://guacamole.apache.org/doc/gug/) for more details.

## Default User

The default username is `guacadmin` with password `guacadmin`.

## Windows-based Docker Hosts

Mapped volumes behave differently when running Docker for Windows and you may encounter some issues with PostgreSQL file system permissions. To avoid these issues, and still retain your config between container upgrades and recreation, you can use the local volume driver, as shown in the `docker-compose.yml` example below. When using this setup be careful to gracefully stop the container or data may be lost.

```yml
version: "3"
services:
  guacamole:
    image: modem7/guacamole
    container_name: guacamole
    volumes:
      - postgres:/config
    ports:
      - 8080:8080
volumes:
  postgres:
    driver: local
```

## Something's not working, what to do?

Well, I must admit, I managed to break a few things here and there nevertheless...
The easiest way to correct issues is to stop the container, delete the config folder, and restart the container.

But this has the side effect of deleting your stored users and connections as well.

You can backup and restore the database with the following command (assuming your container is named `guacamole`):

Backup: `docker exec -it guacamole bash -c "pg_dump -U guacamole -F t guacamole_db > guacamole_db_backup.tar"`

This creates a `guacamole_db_backup.tar` in your `config` directory that you need to save somewhere esle.

Now stop the container, delete the config folder, and restart the container.

To restore the database, copy the backup file in your mounted config folder, and run `docker exec -it guacamole bash -c "pg_restore -d guacamole_db guacamole_db_backup.tar -c -U guacamole"`. You can now login to Guacamole with your user data and should find your connections as you left them.


## License

Copyright (C) 2017-2020 oznu

Copyright (C) 2021 abesnier

Maintainer (C) 2022 modem7

Apache Guacamole is released under the Apache License version 2.0.

Extensions uses third-party modules. To consult the licensing for each module, download the extension from https://guacamole.apache.org/releases/1.4.0/, extract it, and check the content of the `bundled` directory.

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the [GNU General Public License](./LICENSE) for more details.
