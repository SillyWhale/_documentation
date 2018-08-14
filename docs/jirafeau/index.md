# Docker Jirafea

## Official informations

Next is according to [https://gitlab.com/mojo42/Jirafeau](https://gitlab.com/mojo42/Jirafeau) :

Jirafeau allows your to "one-click-filesharing". It makes possible to upload a file in a simple way and give an unique link to it.

-----

## Introduction

Jirafeau image was built to be lighter than possible. This image could be provide a fully functional Jirafeau app to serve your secure file sharing platform.

By default, we recommend you to configure Jirafeau with file encryption for improved security.

## Image information

### Dockerfile

You could find **Dockerfile** used to create image [here](https://github.com/SillyWhale/jirafeau/blob/master/Dockerfile).

### Base image

Jirafeau image was basically deployed on [Alpine Linux](https://alpinelinux.org/) official image.  
This choice was done to get a light images for a light application.

### Network Listening

Jirafeau is launched by default in image to listen on :

- Network address : **0.0.0.0**
- Network port : **80**

See [environment section](#environment) to change this.

### Volumes

Jirafeau is configured in image to work with content of **/jirafeau-data** to store datas. You couldn't use any other location without rebuild image.

If you haven't any existing configuration, we recommend to map a persistent volume before first image run. [See usage paragraph](#usage).

This permit you to keep out of container your instance configuration.

## Usage

### Direct run

```bash
docker run -p 80:80 -v ${PWD}/datas/uploads:/jirafeau-data sillywhale/jirafeau:latest
```

### Docker Compose

You could use docker-compose.yml file provided in [repository](https://github.com/SillyWhale/mkdocs.git) :

```docker

version: '3.6'

services:
  app:
    image: sillywhale/jirafeau:latest
    ports:
      - 80:80
    volumes:
      - ${PWD}/data/config.local.php:/Jirafeau/lib/config.local.php
      - ${PWD}/data/uploads:/jirafeau-data/
    restart: on-failure
```

You just do launch in repository folder :

```bash
docker-compose up -d
```

### Environment

| Variable name | Default value | Usage |
| ------------- | ------------- | ----- |
| SIZE\_LIMIT | 1G | Permit to define upload file size limit |