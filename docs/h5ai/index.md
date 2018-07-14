# Docker H5AI

## Official informations

Next is according to [https://larsjung.de/h5ai/](https://larsjung.de/h5ai/) :

h5ai is a modern file indexer for HTTP web servers with focus on your files. Directories are displayed in a appealing way and browsing them is enhanced by different views, a breadcrumb and a tree overview. Initially h5ai was an acronym for HTML5 Apache Index but now it supports other web servers too.

-----

## Introduction

H5AI image was built to be lighter than possible. This image could be provide a fully functional H5AI app to easy access to your content through classic web browser.

This image run with [nginx](https://nginx.org/) and [php-fpm](https://php-fpm.org) in version 7.

## Image information

### Dockerfile

You could find **Dockerfile** used to create image [here](https://github.com/SillyWhale/h5ai/blob/master/Dockerfile).

### Base image

H5AI image was basically deployed on [Alpine Linux](https://alpinelinux.org/) official image.  
This choice was done to get a light images for a light application.

### Network Listening

H5AI is launched by default in image to listen on :

- Network address : **0.0.0.0**
- Network port : **80**

See [environment section](#environment) to change this.

### Volumes

H5AI not use static content to work. You've no specific configuration file on data to keep external to container to run working container.

But, you must map data into container to expose them by h5ai bias.

All volume must be mapped into _/h5ai/<folder>_.

Example :  
You would expose content of /srv/files by h5ai, you must map same as there :

```bash
-v /srv/files:/h5ai/files
```

## Usage

### Direct run

#### Filesystem storage

```bash
docker run -p 80:80 \
        -v ${PWD}/data:/h5ai/data \
        sillywhale/h5ai:latest
```

### Docker Compose

You could use docker-compose.yml file provided in [repository](https://github.com/SillyWhale/h5ai.git) :

```yaml
version: '3.6'

services:
  autre:
    image: sillywhale/h5ai:latest
    ports: 
      - 80:80
    volumes:
      - ${PWD}/data:/h5ai/data
```