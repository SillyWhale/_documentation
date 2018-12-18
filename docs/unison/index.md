# Docker Unison

## Official informations

Next is according to [https://www.cis.upenn.edu/~bcpierce/unison/](https://www.cis.upenn.edu/~bcpierce/unison/) :

Unison is a file-synchronization tool for OSX, Unix, and Windows. It allows two replicas of a collection of files and directories to be stored on different hosts (or different disks on the same host), modified separately, and then brought up to date by propagating the changes in each replica to the other.

Unison shares a number of features with tools such as configuration management packages ([CVS](http://www.cyclic.com/), [PRCS](http://www.xcf.berkeley.edu/~jmacd/prcs.html), [Subversion](http://subversion.tigris.org/), [BitKeeper](https://www.bitkeeper.org/), etc.), distributed filesystems ([Coda](http://www.coda.cs.cmu.edu/), etc.), uni-directional mirroring utilities ([rsync](https://rsync.samba.org/), etc.), and other synchronizers ([Intellisync](www.pumatech.com), [Reconcile](http://www.merl.com/publications/TR99-14), etc).

-----

## Introduction

Unison image was built to be lighter than possible. This image could be provide a fully functional Unison app to easy and safer sync your files.

Application was built from sources and work in two different modes:

- Server: To send your file from other device to unison container.
- Client: To sync your local files to destination unison server.

## Image information

### Dockerfile

You could find **Dockerfile** used to create image [here](https://github.com/SillyWhale/unison/blob/master/Dockerfile).

### Base image

Unison image was basically deployed on [Alpine Linux](https://alpinelinux.org/) official image.  
This choice was done to get a light images for a light application.

### Work modes

#### Server

Unison image could run in server mode coupled with openssh server directly into image.  
This ssh server is natively configured to get minimal basic securities excluding password authentication method.

To run image in server mode, refer to [environment](#environment) paragraph to get all needed variables.

#### Client

Unison image could run too in client mode.

In this case, you must be use other volumes to map :
- Your private ssh key into container.
- Your data you'll sync to remote server into container.
- Your customized unison configuration.

To run image in client mode, refer to [environment](#environment) paragraph to get all needed variables.

### Volumes

#### Server mode

Unison is configured in standard to use filesystem storage to be totaly autonomous.  
Data are stored into **/unison-data** folder into image.

You must map volume into this folder to keep your data out of container in persistent storage.

#### Client mode

To run Unison image in client mode you must map somes volumes to get needed information to unison to work.

You must map :

- Your private ssh key into /root/.ssh/id_xxx (according to your key type)
- Your datas into /unison-datas
- Your unison configuration into /root/.unison/config.prf (Or directly all .unison folder)

## Usage

### Direct run

```bash
docker run -d \
           -p 2222:22 \
           -e US_TYPE='server'
           -e US_SSHKEY_PUB='Your ssh public key content' \
           unison:2.51.2
```

### Docker Compose

You could use docker-compose.yml file provided in [repository](https://github.com/SillyWhale/unison.git) :

```yaml
version: '3.6'

services:
  unison-srv:
    image: unison:2.51.2
    ports:
      - 2222:22
    volumes:
      - ${PWD}/datas:/unison-datas
    environment:
      - US_TYPE=server
      - US_SSHKEY_PUB='Paste your ssh public key here'
    restart: always
```

You just do launch in repository folder :

```bash
docker-compose up -d
```


## Environment

| Title | Default value | Action |
| :---: | :-----------: | :----- |
| US_TYPE | server | Select container working type server or client |
| US_SSHKEY_PUB | N/A | **Mandatory in server mode** Your public ssh key to could connect to container. |
| US_USER | unison | If you would define specific user to connect to container |