# Docker Privatebin

## Official informations

Next is according to [https://privatebin.info](https://privatebin.info) :

PrivateBin is a minimalist, open source online [pastebin](https://en.wikipedia.org/wiki/Pastebin) where the server has zero knowledge of pasted data.

Data is encrypted/decrypted in the browser using 256bit AES in [Galois Counter mode](https://en.wikipedia.org/wiki/Galois/Counter_Mode).

This is a fork of ZeroBin, originally developed by [Sébastien Sauvage](https://github.com/sebsauvage/ZeroBin). It was refactored to allow easier and cleaner extensions and has now much more features than the original. It is however still fully compatible to the original ZeroBin 0.19 data storage scheme. Therefore such installations can be upgraded to this fork without losing any data.

-----

## Introduction

Privatebin image was built to be lighter than possible. This image could be provide a fully functional PrivateBin app to easy and safer share your text content.

This could work in 3 database mode :

- sqlite : Store shared data into sqlite3 database
- mysql : Store shared data into MySQL (or MariaDB) database
- filesystem : Store shared data (encrypted) directly of filesystem.

This image run with [nginx](https://nginx.org/) and [php-fpm](https://php-fpm.org) in version 7.

## Image information

### Dockerfile

You could find **Dockerfile** used to create image [here](https://github.com/SillyWhale/privatebin/blob/master/Dockerfile).

### Base image

PrivateBin image was basically deployed on [Alpine Linux](https://alpinelinux.org/) official image.  
This choice was done to get a light images for a light application.

### Network Listening

PrivateBin is launched by default in image to listen on :

- Network address : **0.0.0.0**
- Network port : **80**

See [environment section](#environment) to change this.

### Volumes

PrivateBin is configured in standard to use filesystem storage to be totaly autonomous.  
Data are stored into **/privatebin-data** folder into image.

If you wouldn't use database storage we incite you to map volume before first image start to keep persistent datas out of container.

This permit you to keep out of container your documentation content.

### Databases

If you use databases to store shared data you'll could be in two cases.

SQLite3 Databases store data into **/privatebin-data/db.sq3** and we incite you to map persistent storage for this DB.

MySQL (or MariaDB) and we need to provide external MySQL server or specific MySQL container to store data. If you'll be use this, please refer to [Usage](#usage).

## Usage

### Direct run

#### Filesystem storage

```bash
docker run -p 80:80 \
        -v ${PWD}/privatebin-data:/privatebin-data \
        sillywhale/privatebin:latest
```

#### SQLite3 storage

```bash
docker run -p 80:80 \
        -e PB_DB='sqlite'
        -v ${PWD}/privatebin-data:/privatebin-data \
        sillywhale/privatebin:latest
```

#### MariaDB storage

Run MariaDB container

```bash
docker run --name privatebindb \
            -v ${PWD}/mariadb-data:/var/lib/mysql \
            -e MYSQL_ROOT_PASSWORD=rootpassword \
            -e MYSQL_DATABASE=privatebin \
            -e MYSQL_USER=privatebin \
            -e MYSQL_PASSWORD=privatebinpassword \
            mariadb:latest
```

Next, run privatebin container

```bash
docker run -p 80:80 \
        -l privatebindb:privatebindb \
        -e PB_DB='mysql' \
        -e PB_MYSQL_DB_HOS='privatebindb' \
        -e PB_MYSQL_DB_NAME='privatebin' \
        -e PB_MYSQL_DB_USERNAME='privatebin' \
        -e PB_MYSQL_DB_PASSWORD='privatebinpassword' \
        -v ${PWD}/privatebin-data:/privatebin-data \
        sillywhale/privatebin:latest
```

### Docker Compose

You could use docker-compose.yml file provided in [repository](https://github.com/SillyWhale/privatebin.git) :

#### docker-compose-filesystem.yml

```docker
version: '3.6'

service:
  privatebinapp:
    container_name: privatebin
    image: sillywhale/privatebin:latest
    ports:
      - 80:80
    volumes:
      - ${PWD}/privatebin-data:/privatebin-data
    restart: on-failure
```

You just do launch in repository folder :

```bash
ln -s docker-compose-filesystem.yml docker-compose.yml
docker-compose up -d
```

#### docker-compose-sqlite3.yml

```docker
version: '3.6'

service:
  privatebinapp:
    container_name: privatebin
    image: sillywhale/privatebin:latest
    ports:
      - 80:80
    volumes:
      - ${PWD}/privatebin-data:/privatebin-data
    environment:
      - PB_DB='sqlite'
    restart: on-failure
```

You just do launch in repository folder :

```bash
ln -s docker-compose-sqlite3.yml docker-compose.yml
docker-compose up -d
```

#### docker-compose-mariadb.yml

```docker
version: '3.6'

service:
  privatebindb:
    container_name: db
    image: mariadb:latest
    volumes:
      - ${PWD}/mariadb-data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - MYSQL_DATABASE=privatebin
      - MYSQL_USER=privatebin
      - MYSQL_PASSWORD=privatebinpassword

  privatebinapp:
    container_name: app
    image: sillywhale/privatebin:latest
    ports:
      - 80:80
    volumes:
      - ${PWD}/privatebin-data:/privatebin-data
    environment:
      - PB_DB='mysql'
      - PB_MYSQL_DB_HOS='privatebindb'
      - PB_MYSQL_DB_NAME='privatebin'
      - PB_MYSQL_DB_USERNAME='privatebin'
      - PB_MYSQL_DB_PASSWORD='privatebinpassword'
    depends_on:
      - privatebindb
    restart: on-failure
```

You just do launch in repository folder :

```bash
ln -s docker-compose-mariadb.yml docker-compose.yml
docker-compose up -d
```

## Environment

| Title | Default value | Action |
| :---: | :-----------: | :----- |
| PB_DISCUTION | true | Activate Discution |
| PB_OPEN_DISCUTION | false | Open discution panel by default |
| PB_PASSWORD | true | Activate password protected paste |
| PB_BURNAFTERREAD | false | Check burn after read case by default |
| PB_SIZELIMIT | 2097152 | Maximum paste size (In bytes) |
| PB_TEMPLATE | bootstrap | Pastebin template |
| PB_QRCODE | true | Activate QRCode sharing |
| PB_ZEROBINCOMPAT | false | Activate compatibility mode for zerobin |
| PB_DB | none | Select model storage DB (none -> filesystem, sqlite, mysql) |
| PB_MYSQL_DB_HOST |  | MySQL Database host |
| PB_MYSQL_DB_NAME |  | MySQL Database name |
| PB_MYSQL_DB_USERNAME |  | MySQL Username |
| PB_MYSQL_DB_PASSWORD |  | MySQL Password |