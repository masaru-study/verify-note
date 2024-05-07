+++
title = "コンテナレシピ-Portainerサーバー-"
date = 2024-05-05T03:36:26+09:00
weight = 1
draft = false
categories = ["Server", "Cloud"]
tags = ["Author:あゆむ", "Level:中級", "Docker"]
+++

## docker-compose.yml

```yml
services:
  portainer-mgmt:
    build:
      context: ./
      dockerfile: Dockerfile
    image: custom/portainer-ce:latest
    container_name: portainer-mgmt
    hostname: portainer-mgmt
    restart: always
    volumes:
      - type: bind
        source: "/var/run/docker.sock"
        target: "/var/run/docker.sock"
      - type: bind
        source: "./bind/data"
        target: "/data"
    extra_hosts:
      - host.docker.internal:host-gateway
    ports:
      - "9111:9000"
    networks:
      - backend

networks:
  backend:
    external: true
```

## Dockerfile

```Dockerfile
# syntax=docker/dockerfile:1.7
FROM portainer/portainer-ce:latest
```
