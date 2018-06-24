# Docker MkDocs

## Official informations

Next is according to [https://mkdocs.readthedocs.io](https://mkdocs.readthedocs.io) :

MkDocs is a fast, simple and downright gorgeous static site generator that's geared towards building project documentation. Documentation source files are written in Markdown, and configured with a single YAML configuration file.

**MkDocs is currently still in development.**

-----

## Introduction

MkDocs image was built to be lighter than possible. This image could be provide a fully functional MkDocs app to serve your documentation or other blog content why not.

This image create, if no existing configuration was provide, a default basis configuration (aka: ```mkdocs new project```).

## Image information

### Dockerfile

You could find **Dockerfile** used to create image [here](https://github.com/SillyWhale/mkdocs/blob/master/Dockerfile).

### Base image

MkDocs image was basically deployed on [Alpine Linux](https://alpinelinux.org/) official image.  
This choice was done to get a light images for a light application.

### Network Listening

MkDocs is launched in image to listen on :

- Network address : **0.0.0.0**
- Network port : **8000**

You couldn't change port without rebuild image.

### Volumes

MkDocs is configured in image to work with content of **/mkdocs**. You couldn't use any other location without rebuild image.

If you haven't any existing configuration, we recommend to map a persistent volume before first image run. [See usage paragraph](#usage).

This permit you to keep out of container your documentation content.

## Usage

### Direct run

```bash
docker run -p 8000:8000 -v ${PWD}/mkdocs-content:/mkdocs sillywhale/mkdocs:latest
```

### Docker Compose

You could use docker-compose.yml file provided in [repository](https://github.com/SillyWhale/mkdocs.git) :

```docker
version: '3.6'

service:
  mkdocs:
    image: sillywhale/mkdocs:latest
    ports:
      - 8000:8000
    volumes:
      - ${PWD}/mkdocs-content:/mkdocs
    restart: on-failure
```

You just do launch in repository folder :
```bash
docker-compose up -d
```