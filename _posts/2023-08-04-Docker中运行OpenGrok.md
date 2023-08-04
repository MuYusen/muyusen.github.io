---
layout: mypost
title: Docker中运行OpenGrok
categories: [Docker,OpenGrok]
---

## docker image

```bash
docker pull scue/docker-opengrok:latest
```

## docker run

```bash
docker run -itd --name opengrok -p 8888:8080/tcp -e REINDEX="60" -v $(pwd)/src/:/opengrok/src  -v $(pwd)/data/:/opengrok/data/ --restart=on-failure scue/docker-opengrok:latest
```

## reindex

```bash
docker exec -it opengrok bash

/opengrok-0.12.1.5/bin/OpenGrok index /opengrok/src
```
