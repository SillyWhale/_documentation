# Docker MkDocs

**INFO**: Work in progress

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